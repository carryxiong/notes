#SQL的select语句完整的执行顺序 
	1、from子句组装来自不同数据源的数据；  
	2、where子句基于指定的条件对记录行进行筛选；  
	3、group by子句将数据划分为多个分组；   
	4、使用聚集函数进行计算；  
	5、使用having子句筛选分组；  
	6、计算所有的表达式；  
	7、select 的字段； 
	8、使用order by对结果集进行排序。 

##SQL 语言不同于其他编程语言的最明显特征是处理代码的顺序。在大多数据库语言中，代码按编码顺序被处理。但在 SQL 语句中，第一个被处理的子句式 FROM，而不是第一出现的 SELECT。SQL 查询处理的步骤序号：
	 (1) FROM  
	(2) JOIN  
	(3) ON  
	(4) WHERE  
	(5) GROUP BY  
	(6) WITH {CUBE | ROLLUP} 
	(7) HAVING  
	(8) SELECT 
	(9) DISTINCT 
	(9) ORDER BY  
	(10)  
##以上每个步骤都会产生一个虚拟表，该虚拟表被用作下一个步骤的输入。这些虚拟表对调用者(客户端应用程序或者外部查询)不可用。只有最后一步生成的表才会会给调用者。如果没有在查询中指定某一个子句，将跳过相应的步骤。 逻辑查询处理阶段简介： 
	1、 FROM：对FROM子句中的前两个表执行笛卡尔积(交叉联接)，生成虚拟表VT1。 
	2、 ON：对VT1应用ON筛选器，只有那些使为真才被插入到TV2。 
	3、 OUTER (JOIN):如果指定了OUTER JOIN(相对于CROSS JOIN或INNER JOIN)，保留表中未找到
	匹配的行将作为外部行添加到VT2，生成TV3。如果FROM子句包含两个以上的表，则对上一个联接生成的
	结果表和下一个表重复执行步骤1到步骤3，直到处理完所有的表位置。 
	4、 WHERE：对TV3应用WHERE筛选器，只有使为true的行才插入TV4。 
	5、 GROUP BY：按GROUP BY子句中的列列表对TV4中的行进行分组，生成TV5。 
	6、 CUTE|ROLLUP：把超组插入VT5，生成VT6。 
	7、 HAVING：对VT6应用HAVING筛选器，只有使为true的组插入到VT7。 
	8、 SELECT：处理SELECT列表，产生VT8。 
	9、 DISTINCT：将重复的行从VT8中删除，产品VT9。 
	10、 ORDER BY：将VT9中的行按ORDER BY子句中的列列表顺序，生成一个游标(VC10)。 
	11、 TOP：从VC10的开始处选择指定数量或比例的行，生成表TV11，并返回给调用者。 
	where子句中的条件书写顺序

#来源
	http://blog.sina.com.cn/s/blog_c00ccc680102xxwx.html