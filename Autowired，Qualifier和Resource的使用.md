#Autowired，Qualifier，Resource注解的使用
1. Autowired注解在注入bean对象时，是先直接以对象类型在容器中寻找，如果只有一个此类型的对象，那么就可以直接注入成功，但是有多个的情况下，那么它将把变量名称作为id值，在容器中的key中比较，如果找到了，就将那个对象注入，不然就报错。有个request属性，默认是true。要求必须注入。
2. Qualifier注解可以对类成员注入，也可以对方法中的成员注入，但是在注入类成员时必须和Autowired配合使用，无法单独使用。它的value是可以来帮助Autowired来指定id，明确指定map容器中的那个id，也就是key。
3. 对于Resource来说，当出现多个此类型的对象时，使用Autowired和Qualifier太过麻烦的情况下，可以使用Resource来直接指定id，但是这个属性不是value，而是name，因此，不能省略不写。

***
#Value注解
对于上面的三个注解来说，只能用于一些bean类型的数据注入，对于基本的数据类型和字符串来说需要是使用Value注解来完成。
比如说在数据库的四个连接信息来说，就可以这样定义以后使用Value注解来注入。