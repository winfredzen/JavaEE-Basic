# CASE

参考：

+ SQL进阶教程

> CASE在SQL里表达条件分支

CASE有2种使用方式，参考官方文档[CASE Syntax](https://dev.mysql.com/doc/refman/5.7/en/case.html)

```mysql
CASE case_value
    WHEN when_value THEN statement_list
    [WHEN when_value THEN statement_list] ...
    [ELSE statement_list]
END CASE
```

或者

```mysql
CASE
    WHEN search_condition THEN statement_list
    [WHEN search_condition THEN statement_list] ...
    [ELSE statement_list]
END CASE
```

注意点：

1. 分支返回的数据类型要一致
2. 不要忘记写END
3. 养成写ELSE子句的习惯。ELSE子句是可选的，不写也不会出错。不写ELSE子句时，CASE表达式的执行结果是`NULL`。但如果不写，会造成不易追查原因

例如：

```mysql
CASE sex 
     WHEN '1' THEN '男' 
     WHEN '2' THEN '女' 
ELSE '其他' END

CASE WHEN sex = '1' THEN '男' 
     WHEN sex = '2' THEN '女' 
ELSE '其他' END 
```

这两种写法的执行结果是相同的

**注意**：在发现WHEN子句为真时，CASE表达式的真假值判断就会停止，而剩余的WHEN子句会被忽略。

```mysql
-- 例如，这样写的话，结果里不会出现“第二”
CASE WHEN col_1 IN ( 'a', 'b') THEN '第一类' 
     WHEN col_1 IN ('a')       THEN '第二类' 
ELSE'其他' END 
```



## CASE的用处

**1.将已有编号方式转化为新的方式并统计**

例如，知道各个省的人口，现在要统计东北、华北、华中的人口，以书中的例子来说明，如下：

![001](https://github.com/winfredzen/JavaEE-Basic/blob/master/MySQL/images/001.png)

> 德岛、香川、爱媛、高知属于四国，福冈、佐贺、长崎属于九州

使用CASE语句

```mysql
-- 把县编号转换成地区编号
SELECT CASE pref_name
	WHEN '德岛' THEN '四国'
	WHEN '香川' THEN '四国' 
	WHEN '爱媛' THEN '四国' 
	WHEN '高知' THEN '四国' 
	WHEN '福冈' THEN '九州' 
	WHEN '佐贺' THEN '九州' 
	WHEN '长崎' THEN '九州' 
	ELSE '其它'
	END AS district,
	SUM(population)
FROM pop_tbl
GROUP BY 
	CASE pref_name
	WHEN '德岛' THEN '四国'
	WHEN '香川' THEN '四国' 
	WHEN '爱媛' THEN '四国' 
	WHEN '高知' THEN '四国' 
	WHEN '福冈' THEN '九州' 
	WHEN '佐贺' THEN '九州' 
	WHEN '长崎' THEN '九州' 
	ELSE '其它'
	END;
```

> 关键是将SELECT子句中的CASE表达式复制到GROUP BY子句

同样，可以将数值按适当的级别进行分类。例如，按人口数量等级：

```mysql
SELECT CASE 
	WHEN population < 100 THEN '01'
	WHEN population >= 100 AND population < 200 THEN '02'
	WHEN population >= 200 AND population < 300 THEN '03'
	WHEN population >= 300 THEN '04'
	ELSE NULL END AS pop_class, COUNT(*) AS cnt
FROM pop_tbl
GROUP BY  
CASE 
	WHEN population < 100 THEN '01'
	WHEN population >= 100 AND population < 200 THEN '02'
	WHEN population >= 200 AND population < 300 THEN '03'
	WHEN population >= 300 THEN '04'
	ELSE NULL END;
```

![002](https://github.com/winfredzen/JavaEE-Basic/blob/master/MySQL/images/002.png)

但，必须在SELECT子句和GROUP BY子句这两处写一样的CASE表达式，有点麻烦。修改的时候，需要同时修改两处。

如下，GROUP BY子句引用了SELECT子句中定义的别名

```mysql
-- 将CASE表达式归纳到一处
SELECT CASE pref_name
	WHEN '德岛' THEN '四国'
	WHEN '香川' THEN '四国' 
	WHEN '爱媛' THEN '四国' 
	WHEN '高知' THEN '四国' 
	WHEN '福冈' THEN '九州' 
	WHEN '佐贺' THEN '九州' 
	WHEN '长崎' THEN '九州' 
	ELSE '其它'
	END AS district,
	SUM(population)
FROM pop_tbl
GROUP BY district;
```

> 严格来说，这样写是违法SQL的规则的。因为GROUP BY子句比SELECT子句先执行，所以GROUP BY子句引用在SELECT子句里定义的别称是不被允许的。



**2.用一条SQL语句进行不同条件的统计**

如下的例子，知道每个县市的性别人数，然后按性别、县名统计人数

![003](https://github.com/winfredzen/JavaEE-Basic/blob/master/MySQL/images/003.png)

通常的做法是，在WHERE子句里分贝写上不同的条件

```mysql
-- 男性人口
SELECT pref_name, SUM(population) FROM PopTbl2 WHERE sex = '1' GROUP BY pref_name;

-- 女性人口
SELECT pref_name, SUM(population) FROM PopTbl2 WHERE sex = '2' GROUP BY pref_name;
```

然后使用UNION，但这样做，工作量并没有减少，SQL语句也变的很长。

使用CASE表达式：

```mysql
SELECT pref_name,
	SUM(CASE WHEN sex = 1 THEN population ELSE 0 END) AS cnt_m,
	SUM(CASE WHEN sex = 2 THEN population ELSE 0 END) AS cnt_f
FROM PopTbl2
GROUP BY pref_name;
```

**这里是将行结构数据转换成了列结构数据**

**新手用WHERE自己进行条件分支，高手用SELECT子句进行条件分支**



**3.用CHECK约束定义多个列的条件关系**



**4.在UPDATE语句里进行条件分支**



**5.表之间的数据匹配**

在CASE表达式里，可以使用BETWEEN、LIKE和<、>等便利的谓词组合，以及能嵌套子查询的IN和EXISTS谓词

如下，有一张课程一览表和一张管理每个月所设课程的表

![004](https://github.com/winfredzen/JavaEE-Basic/blob/master/MySQL/images/004.png)

![005](https://github.com/winfredzen/JavaEE-Basic/blob/master/MySQL/images/005.png)

需要这两张表生成如下的交叉表，用于知道每个与开设的课程

![006](https://github.com/winfredzen/JavaEE-Basic/blob/master/MySQL/images/006.png)

使用CASE表达式

```mysql
-- 使用IN谓词
SELECT CM.course_name,
       CASE WHEN CM.course_id IN 
                    (SELECT course_id FROM OpenCourses 
                      WHERE month = 200706) THEN '○'
            ELSE '×' END AS "6月",
       CASE WHEN CM.course_id IN 
                    (SELECT course_id FROM OpenCourses
                      WHERE month = 200707) THEN '○'
            ELSE '×' END AS "7月",
       CASE WHEN CM.course_id IN 
                    (SELECT course_id FROM OpenCourses
                      WHERE month = 200708) THEN '○'
            ELSE '×' END  AS "8月"
  FROM CourseMaster CM;
```

```mysql
-- 使用EXISTS谓词
SELECT CM.course_name,
       CASE WHEN EXISTS
                    (SELECT course_id FROM OpenCourses OC
                      WHERE month = 200706
                        AND CM.course_id = OC.course_id) THEN '○'
            ELSE '×' END AS "6月",
       CASE WHEN EXISTS
                    (SELECT course_id FROM OpenCourses OC
                      WHERE month = 200707
                        AND CM.course_id = OC.course_id) THEN '○'
            ELSE '×' END AS "7月",
       CASE WHEN EXISTS
                    (SELECT course_id FROM OpenCourses OC
                      WHERE month = 200708
                        AND CM.course_id = OC.course_id) THEN '○'
            ELSE '×' END  AS "8月"
  FROM CourseMaster CM;
```



## 练习题

**1.多列数据的最大值**

在SQL中从多行数据中选出最大值or最小值很容易，通过GROUP BY子句对合适的列进行聚合操作，并使用MAX或MIN聚合函数就可以求出。那从多列数据中选出最大值该怎么办？

如下的数据表：

![007](https://github.com/winfredzen/JavaEE-Basic/blob/master/MySQL/images/007.png)

```mysql
/* 求x、y和z中的最大值 */
SELECT keey,
	CASE 
		WHEN CASE WHEN x < y THEN y ELSE x END < z THEN z
    ELSE CASE WHEN x < y THEN y ELSE x END
  END AS greatest
FROM example1_1_1;
```

或者**转换成行格式后使用MAX函数**

```mysql
/* 转换成行格式后使用MAX函数 */
SELECT keey, MAX(col) AS greatest
  FROM (SELECT keey, x AS col FROM example1_1_1
        UNION ALL
        SELECT keey, y AS col FROM example1_1_1
        UNION ALL
        SELECT keey, z AS col FROM example1_1_1) TMP
 GROUP BY keey;
```

或者使用`GREATEST`函数

```mysql
 SELECT keey, GREATEST(GREATEST(x,y), z) AS greatest
 FROM example1_1_1;
```



**2.转换行列，加入汇总**

使用PopTbl2作为样本数据，把行结构转化成如下的列结构，并加入汇总

![008](https://github.com/winfredzen/JavaEE-Basic/blob/master/MySQL/images/008.png)

```mysql
/* 转换行列——在表头里加入汇总和再揭(p.287) */
SELECT sex,
       SUM(population) AS total,
       SUM(CASE WHEN pref_name = '德岛' THEN population ELSE 0 END) AS col_1,
       SUM(CASE WHEN pref_name = '香川' THEN population ELSE 0 END) AS col_2,
       SUM(CASE WHEN pref_name = '爱媛' THEN population ELSE 0 END) AS col_3,
       SUM(CASE WHEN pref_name = '高知' THEN population ELSE 0 END) AS col_4,
       SUM(CASE WHEN pref_name IN ('德岛', '香川', '爱媛', '高知')
                THEN population ELSE 0 END) AS zaijie
  FROM PopTbl2
 GROUP BY sex;
```

我自己写的：

```mysql
SELECT 
	(CASE WHEN sex = 1 THEN '男' ELSE '女' END) AS '性别',
	SUM(population) AS '全国',
	SUM(CASE WHEN pref_name = '德岛' THEN population ELSE 0 END) AS '德岛',
	SUM(CASE WHEN pref_name = '香川' THEN population ELSE 0 END) AS '香川',
	SUM(CASE WHEN pref_name = '爱媛' THEN population ELSE 0 END) AS '爱媛',
	SUM(CASE WHEN pref_name = '高知' THEN population ELSE 0 END) AS '高知',
	SUM(CASE WHEN pref_name = '德岛' or pref_name = '香川' or pref_name = '爱媛' or pref_name = '高知' THEN population ELSE 0 END) AS '四国'
FROM PopTbl2
GROUP BY sex;
```

