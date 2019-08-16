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

**注意**：在发现为真的WHEN子句时，CASE表达式的真假值判断就会停止，而剩余的WHEN子句会被忽略。

```mysql
-- 例如，这样写的话，结果里不会出现“第二”
CASE WHEN col_1 IN ( 'a', 'b') THEN '第一类' 
     WHEN col_1 IN ('a')       THEN '第二类' 
ELSE'其他' END 
```



## CASE的用处

**1.将已有编号方式转化为新的方式并统计**

例如，知道各个省的人口，现在要统计东北、华北、华中的人口















