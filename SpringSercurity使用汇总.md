# SpringSecurity使用问题汇总





# 待解决

1. springsecurity为什么配置完下面这个以后，之前的放行也都失效了，需要token，这是一个问题。

2. 对于这个使用了“/api/**"需要验证的配置为什么没有生效。

3. 可能答案在过滤器链中。

   ```java
    .anyRequest()//任何请求
    .authenticated()//都需要认证
    //应该下面都需要验证，但是没有，除非写上具体的角色，不然可以直接访问到接口。    
   .antMatchers(SecurityConstants.FILTER_ALL).authenticated()
   ```

# 放行失效的原因

对于一个拦截配置来说，为什么会失效，因为我在之前的项目里面配置了context-path，配置了项目路径。

然后我在配置放行的url的时候，加上了这个api，但是springsecurity默认的读取url路径的时候，它认为这个/api是一个项目路径，而不是url，所以这个地方读到的就不带/api，但是你配置的antmatch上又有这个东西，所以会导致匹配不上。

## 问题1 

**在我写完注册接口以后，我设置的时候，对于一个刚注册的用户来说，并没有设置一个默认的角色，那么也就是它的userRole为空。**

```java
 private List<UserRole> userRoles;//这个地方没有设置它的UserRole，那么就是没有权限
```

```java
/**
*登录的时候，我们在这里进行了密码校验，匹配上以后就会进去，通过这个getRoleByUser(user)得到我们的角色名**称，当然这里这个list里面一个元素都没有。然后生成令牌，都没有问题，就是角色为空。
*问题出在哪呢？就是这个getAJwtUtils.getAuthentication(token);这个方法中，这里面需要根据角色名称去*new SimpleGrantedAuthority对象，但是这个对象传入的角色名称就是权限不可以为空
*/
if(bCryptPasswordEncoder.matches(password, user.getPassword()))
        {
            //根据user获取角色名称，一个用户可能有多个角色
            List<String> roleByUser = userService.getRoleByUser(user);
            //生成token令牌
            String token = JwtUtils.generateToken(username, roleByUser, false);

            // 认证成功后，设置认证信息到 Spring Security 上下文中
            Authentication authentication = getAJwtUtils.getAuthentication(token);
            SecurityContextHolder.getContext().setAuthentication(authentication);

            //返回生成的令牌
            return token;
        }



/**
     * 根据 token 获取用户认证信息
     *
     * @param token token 信息
     * @return 返回用户认证信息
     */
    public static Authentication getAuthentication(String token) {
        Claims claims = getTokenBody(token);
        // 获取用户角色字符串
        String roles = (String) claims.get(SecurityConstants.ROLE_CLAIMS);
        List<SimpleGrantedAuthority> authorities =
                // 这里如果不加上为空的判断逻辑的话，会报错 A granted authority textual representation is required] with root cause
                // 对于这个SimpleGrantedAuthority权限来说可能传入的参数不能为空
                Objects.isNull(roles) ? Collections.singletonList(new SimpleGrantedAuthority("ROLE_USER")) :
                 Arrays.stream(roles.split(","))
                        .map(SimpleGrantedAuthority::new)
                        .collect(Collectors.toList());
        // 获取用户名
        String userName = claims.getSubject();

        return new UsernamePasswordAuthenticationToken(userName, token, authorities);

    }
```

## 2.对于登录认证流程源码解析

首先对于一个请求而言，会被拦截器拦截，而跟登录相关的拦截器主要有BasicAuthenticationFilter和UsernamePasswordAuthenticationFilter，分别是basic认证和表单认证。一般是UsernamePasswordAuthenticationFilter拦截登录的请求，其他请求都让BasicAuthenticationFilter拦截。

```java
可以看到进入了这个attemptAuthentication方法中，首先校验一下是不是post请求，不是的话抛出一个异常。
// ~ Methods
	// ========================================================================================================

	public Authentication attemptAuthentication(HttpServletRequest request,
			HttpServletResponse response) throws AuthenticationException {
		if (postOnly && !request.getMethod().equals("POST")) {
			throw new AuthenticationServiceException(
					"Authentication method not supported: " + request.getMethod());
		}

		String username = obtainUsername(request);
		String password = obtainPassword(request);

		if (username == null) {
			username = "";
		}

		if (password == null) {
			password = "";
		}

		username = username.trim();

		UsernamePasswordAuthenticationToken authRequest = new UsernamePasswordAuthenticationToken(
				username, password);

		// Allow subclasses to set the "details" property
		setDetails(request, authRequest);

		return this.getAuthenticationManager().authenticate(authRequest);
	}

然后从request中获取用户名和密码，可以看到这个obtainUsername就是request.getParameter(usernameParameter),从request中获取参数。

  /**
	 * Enables subclasses to override the composition of the username, such as by
	 * including additional values and a separator.
	 *
	 * @param request so that request attributes can be retrieved
	 *
	 * @return the username that will be presented in the <code>Authentication</code>
	 * request token to the <code>AuthenticationManager</code>
	 */
    protected String obtainUsername(HttpServletRequest request) {
    return request.getParameter(usernameParameter);
}

然后可以看到对参数判空之类的操作后，就生成一个UsernamePasswordAuthenticationToken，点进去看看这个token
    /**
	 * This constructor can be safely used by any code that wishes to create a
	 * <code>UsernamePasswordAuthenticationToken</code>, as the {@link #isAuthenticated()}
	 * will return <code>false</code>.
	 *
	 */
	public UsernamePasswordAuthenticationToken(Object principal, Object credentials) {
		super(null);
		this.principal = principal;
		this.credentials = credentials;
		setAuthenticated(false);
	}
可以看到这个这个token调用父类的构造方法，点进去发现这个父类的构造方法就是对参数authorities初始化，就是生成了一个空的ArrayList，因为目前还是没有权限。
    	public AbstractAuthenticationToken(Collection<? extends GrantedAuthority> authorities) {
		if (authorities == null) {
			this.authorities = AuthorityUtils.NO_AUTHORITIES;
			return;
		}

		for (GrantedAuthority a : authorities) {
			if (a == null) {
				throw new IllegalArgumentException(
						"Authorities collection cannot contain any null elements");
			}
		}
		ArrayList<GrantedAuthority> temp = new ArrayList<>(
				authorities.size());
		temp.addAll(authorities);
		this.authorities = Collections.unmodifiableList(temp);
	}
我们继续回到之前，return this.getAuthenticationManager().authenticate(authRequest);
可以看到这里调用AuthenticationManager进行authenticate，

```

<img src="D:\Programfile\Typora\images\image-20210112090106485.png" alt="image-20210112090106485" style="zoom:33%;" />

可以看到这个authenticationManager是传入的，也就是我们在spring security的配置文件中传入的。







# 对于springsecurity默认配置流程

```properties
# AutoConfigureMockMvc auto-configuration imports
org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc=\
org.springframework.boot.test.autoconfigure.web.servlet.MockMvcAutoConfiguration,\
org.springframework.boot.test.autoconfigure.web.servlet.MockMvcWebClientAutoConfiguration,\
org.springframework.boot.test.autoconfigure.web.servlet.MockMvcWebDriverAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.client.servlet.OAuth2ClientAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.resource.servlet.OAuth2ResourceServerAutoConfiguration,\
org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration,\
org.springframework.boot.autoconfigure.security.servlet.SecurityFilterAutoConfiguration,\
org.springframework.boot.autoconfigure.security.servlet.UserDetailsServiceAutoConfiguration,\
org.springframework.boot.test.autoconfigure.web.servlet.MockMvcSecurityConfiguration
```

这是spring.factories中关于springsecurity的自动配置类。

进入第一个securityAutoConfiguration中，

```java
@Configuration(proxyBeanMethods = false)
//ConditionalOnClass说明这个类，依赖DefaultAuthenticationEventPublisher这个类
@ConditionalOnClass(DefaultAuthenticationEventPublisher.class)
//SecurityProperties中有关于它的配置。
@EnableConfigurationProperties(SecurityProperties.class)
//导入了这三个类
@Import({ SpringBootWebSecurityConfiguration.class, WebSecurityEnablerConfiguration.class,
		SecurityDataConfiguration.class })
public class SecurityAutoConfiguration {

    //这里可以看到如果不存在AuthenticationEventPublisher，就自己new了一个
	@Bean
	@ConditionalOnMissingBean(AuthenticationEventPublisher.class)
	public DefaultAuthenticationEventPublisher authenticationEventPublisher(ApplicationEventPublisher publisher) {
		return new DefaultAuthenticationEventPublisher(publisher);
	}

}
而这个DefaultAuthenticationEventPublisher实现了AuthenticationEventPublisher接口。
```

<img src="D:\Programfile\Typora\images\image-20210121092528091.png" alt="image-20210121092528091" style="zoom:50%;" />

所以不管如何这个securityAutoConfiguration都会生效，而我们进入这个SpringBootWebSecurityConfiguration类中。

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnDefaultWebSecurity
@ConditionalOnWebApplication(type = Type.SERVLET)
class SpringBootWebSecurityConfiguration {

	@Bean
	@Order(SecurityProperties.BASIC_AUTH_ORDER)
	SecurityFilterChain defaultSecurityFilterChain(HttpSecurity http) throws Exception {
		http.authorizeRequests().anyRequest().authenticated().and().formLogin().and().httpBasic();
		return http.build();
	}

}
```

发现这里与我们配置了基本的springsecurity拦截的配置，这里与我们自己写的继承WebSecurityConfigurerAdapter类的配置很相似。这里可以看到http声明拦截所有的请求，并且规定了basic登录，调用httpsecurity的bulid方法生成了一个SecurityFilterChain，这就是默认的拦截器链了。我们知道springsecurity的拦截器都是由SecurityFilterChain管理

```java
可以看到SecurityFilterChain接口里面就是由一个list装载着这些过滤器。
public interface SecurityFilterChain{

	boolean matches(HttpServletRequest request);

	List<Filter> getFilters();
}
//这是SecurityFilterChain的默认实现，就是一个list集合。
public final class DefaultSecurityFilterChain implements SecurityFilterChain {
	private static final Log logger = LogFactory.getLog(DefaultSecurityFilterChain.class);
	private final RequestMatcher requestMatcher;
	private final List<Filter> filters;
   

```

匿名用户访问接口的异常堆栈------------>

![image-20210121123329455](D:\Programfile\Typora\images\image-20210121123329455.png)

![image-20210121123356835](D:\Programfile\Typora\images\image-20210121123356835.png)

![image-20210121123417248](D:\Programfile\Typora\images\image-20210121123417248.png)

关键在于super.beforeInvocation(fi);这个里面，认证正常的会在这里面抛出异常，而不正常的时候会返回一个null，token就是null，导致后面不一样。也就是说beforeInvocation里面的逻辑就是判断有没有权限。

<img src="D:\Programfile\Typora\images\image-20210121152924676.png" alt="image-20210121152924676"  />



