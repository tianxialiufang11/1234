## mysql服务停止
`net stop mysql80`
## root密码覆盖
`mysqld --defaults-file="C:\ProgramData\MySQL\MySQL Server 8.0\my.ini" --init-file="F:\yanerxia\temp.txt" --console`
## mysql服务启动
`net start mysql80`

## 基本操作
`SHOW DATABASES;`
`CREATE DATABASE demo;`
`DROP DATABASE demo;`

## 新建查询
`USE test;`

    CREATE TABLE student( 
      id INT UNSIGNED PRIMARY KEY, 
      name VARCHAR(20) NOT NULL, 
      sex CHAR(1) NOT NULL, 
      birthday DATE NOT NULL, 
      Le1 CHAR(11) NOT NULL, 
      remark VARCHAR(200) 
    );

 ```sql
 CREATE TABLE student( 
	id INT UNSIGNED PRIMARY KEY, 
	name VARCHAR(20) NOT NULL, 
	sex CHAR(1) NOT NULL, 
	birthday DATE NOT NULL, 
	Le1 CHAR(11) NOT NULL, 
	remark VARCHAR(200) 
);
 ```


	INSERT INTO student VALUES(1,"李强","男","1995-05-15","13312345678",NULL);

```sql
INSERT INTO student (id, name, sex, birthday, Le1, remark)
VALUES
    (2, "张美丽", "女", "1996-08-20", "13987654321", "某地某街道"),
    (3, "王勇", "男", "1994-02-10", "13765432109", "另一地另一街道");
```

`SHOW tables;`用于显示数据库中的所有表格
`DESC student;`用于显示表格 `student` 的结构（列名、数据类型等）
`SHOW CREATE TABLE student;`显示创建表格 `student` 的 SQL 语句。
`DROP TABLE student;`删除表格 `student` 及其所有数据
|   类型    |  大小  |     说明      |
| :-------: | :----: | :-----------: |
|  TINYINT  | 1字节  |    小整数     |
| SMALLINT  | 2字节  |   普通整数    |
| MEDIUMINT | 3字节  |   普通整数    |
|    INT    | 4字节  |   较大整数    |
|  BIGINT   | 8字节  |    大整数     |
|   FLOAT   | 4字节  | 单精度浮点数  |
|  DOUBLE   | 8字节  | 双精度浮点数  |
|  DECIMAL  | ------ | DECIMAL(10,2) |

|    类型    |      大小      |       说明       |
| :--------: | :------------: | :--------------: |
|    CHAR    |   1-255字符    |  固定长度字符串  |
|  VARCHAR   |  1-65535字符   | 不固定长度字符串 |
|    TEXT    |  1-65535字符   | 不确定长度字符串 |
| MEDIUMTEXT | 1-1千6百万字符 | 不确定长度字符串 |
|  LONGTEXT  |   1-42亿字符   | 不确定长度字符串 |

|   类型    | 大小  |   说明   |
| :-------: | :---: | :------: |
|   DATE    | 3字节 |   日期   |
|   TIME    | 3字节 |   时间   |
|   YEAR    | 1字节 |   年份   |
| DATETIME  | 8字节 | 日期时间 |
| TIMESTAMP | 4字节 |  时间戳  |

## 修改数据表结构

增加列

```sql
ALTER TABLE student
ADD address VARCHAR(200) NOT NULL,
ADD home_tel CHAR(11) NOT NULL;
```
修改列的数据类型或属性
```sql
ALTER TABLE student
MODIFY home_tel VARCHAR(20) NOT NULL;
```
修改列的现有列名称或数据类型
```sql
ALTER TABLE student
CHANGE address home_address VARCHAR(205) NOT NULL;
```
删除列
```sql
ALTER TABLE student
DROP home_address,
DROP home_tel; 
```
## 字段约束

 1. 第一范式（原子性）：每一列不可分割，同一列不能有多个值，也不能存在重复的属性。
 2. 第二范式（唯一性）：每条记录唯一，加上一个列（主键列）用来存储唯一标识。
 3. 第三范式（关联性）：与主键有直接联系，不存在传递依赖。不相关信息保存到不同数据表

| 约束名称 |   关键字    |           描述           |
| :------: | :---------: | :----------------------: |
| 主键约束 | PRIMARY KEY | 字段值唯一，且不能为NULL |
| 非空约束 |  NOT NULL   |     字段值不能为NULL     |
| 唯一约束 |   UNIQUE    | 字段值唯一，且可以为NULL |
| 外键约束 | FOREIGN KEY |   保持关联数据的逻辑性   |

```sql
CREATE TABLE t_TEACHER(
	id INT UNSIGNED PRIMARY KEY AUTO_INCREMENT,  #AUTO_INCREMENT会自动为指定列生成唯一的、递增的值，不需要手动指定，如果手动指定值已存在会报错。
	name VARCHAR(20) NOT NULL,
	tel CHAR(11) NOT NULL UNIQUE,
	married BOOLEAN NOT NULL DEFAULT FALSE
);
INSERT INTO t_TEACHER(name,tel,married) VALUES("李强",15958104798,1);
```
外键约束
```sql
CREATE TABLE t_dept(
	deptno INT UNSIGNED PRIMARY KEY,
	dname VARCHAR(20) NOT NULL UNIQUE,
	tel CHAR(4) UNIQUE
);
CREATE TABLE t_emp(
	empno INT UNSIGNED PRIMARY KEY,
	name VARCHAR(20) NOT NULL,
	sex ENUM("男","女") NOT NULL,
	deptno INT UNSIGNED NOT NULL,
	hiredate DATE NOT NULL,
	FOREIGN KEY (deptno) REFERENCES t_dept(deptno) 
);
```
## 数据库的索引机制

创建索引
```sql
CREATE TABLE t_message(
	id INT UNSIGNED PRIMARY KEY,
	content VARCHAR(200) NOT NULL,
	type ENUM("公告","通知","个人通知") NOT null,
	create_time TIMESTAMP NOT NULL,
	INDEX idx_type (type)
);
```
删除、添加、查询索引
```sql
DROP INDEX idx_type ON t_message;
CREATE INDEX idx_type ON t_message(type);
SHOW INDEX FROM t_message;
ALTER TABLE t_message ADD INDEX idx_type(type); #另一种添加索引方式
```
# 数据库的基本查询

## 查询语句的子句执行顺序

 1. 词法分析与优化
 2. FROM选择数据来源
 3. WHERE子句
 4. GROUP BY分组
 5. SELECT选择输出内容、
 6. ORDER BY
 7. 执行LIMIT子句

## 记录查询

```sql
USE demo;
SELECT * FROM t_emp;
SELECT empno,ename,sal FROM t_emp;
SELECT empno,sal*12 AS 'income' FROM t_emp;  #使用列别名，不会改变原表结构
```


## 分页显示

```sql
SELECT ……  FROM …… LIMIT 起始位置,偏移量;
SELECT empno,sal*12 AS 'income' FROM t_emp LIMIT 0,5;
SELECT empno,sal*12 AS 'income' FROM t_emp LIMIT 5;#如果LIMIT子句只有一个参数，它表示的是偏移量，起始值默认为0
```

## 结果集排序

```sql
SELECT ……  FROM …… ORDER BY 列名 [ASC|DESC];
SELECT empno,ename,job,sal FROM t_emp ORDER BY sal,ename DESC; #先按照首要排序条件排序，然后启用次要排序条件排序，最后按照主键大小排序
```
## 排序加分页
```sql
SELECT empno,ename,sal,deptno 
FROM t_emp
ORDER BY deptno,sal DESC
LIMIT 5;
```
## 去除重复记录
```sql
SELECT DISTINCT 字段 FROM ……;
SELECT DISTINCT job FROM t_emp;
#使用DISTINCT的SELECT子句中只能查询一列数据，如果查询多列，去除重复记录就会失效
SELECT DISTINCT job,ename FROM t_emp;
#DISTINCT关键字只能在SELECT子句中使用一次，并且必须写在第一个字段前面
SELECT DISTINCT job,DISTINCT ename FROM t_emp; #报错
SELECT job,DISTINCT ename FROM t_emp; #报错
```
# 条件查询
WHERE子句中，条件执行的顺序是从左到右的。应该将索引条件，或者筛选掉记录最多的条件写在最左侧。
```sql
SELECT …… FROM …… WHERE 条件 [AND|OR] 条件……;
SELECT empno,ename,sal FROM t_emp
WHERE deptno=10 AND sal >= 2000;
WHERE (deptno=10 OR deptno=20) AND sal >= 2000;
```
## 算数运算符
```sql
SELECT empno,ename,sal,hiredate
FROM t_emp
WHERE deptno=10 AND (sal + IFNULL(comm,0))*12>=15000
AND DATEDIFF(NOW(),hiredate)/365>=20;
```
```sql
SELECT 10+IFNULL(NULL,0); #如果是null则变为0
SELECT NOW();  #输出当下时间
```
## 比较运算符(IN)
|            表达式             |    意义    |            例子            |
| :---------------------------: | :--------: | :------------------------: |
|              IN               |    包含    |    deptno IN(10,30,40)     |
|            IS NULL            |    为空    |        comm IS NULL        |
|          IS NOT NULL          |   不为空   |      comm IS NOT NULL      |
|          BETWEEN AND          |    范围    | sal BETWEEN 2000 AND 3000  |
|             LIKE              |  模糊查询  |      ename LIKE “A%”       |
|            REGEXP             | 正则表达式 | ename REGEXP “[a-zA-Z]{4}” |
| ％匹配多个字符，_匹配单个字符 |            |                            |
```sql
SELECT empno,ename,sal,job,deptno,hiredate
FROM t_emp
WHERE deptno IN(10,20,30) AND job!='SALESMAN'
AND hiredate<'1985-01-01';
```
正则表达式
```sql
SELECT ename,sal,comm
FROM t_emp
WHERE comm IS NOT NULL
AND sal BETWEEN 1000 AND 3000
#AND ename LIKE '%A%';
AND ename REGEXP "^[\\u4e00-\\u9fa5]{2,4}$";
```
## 逻辑运算符(AND,OR,NOT,XOR)
| 表达式 |   意义   |          例子           |
| :----: | :------: | :---------------------: |
|  AND   |  与关系  |   age>18 AND sex="男"   |
|   OR   |  或关系  | empno=8000 OR deptno=20 |
|  NOT   |  非关系  |      NOT deptno=20      |
|  XOR   | 异或关系 |   age>18 XOR sex="男"   |
```sql
SELECT ename,deptno,sal
FROM t_emp
WHERE NOT deptno IN(10,20) XOR sal>=2000;
```
## 按位运算符(二进制)
| 表达式 |   意义   |   例子   |
| :----: | :------: | :------: |
|   &    | 位与关系 |   3&7    |
| &#124; | 位与关系 | 3&#124;7 |
|   ~    |  位取反  |   ~10    |
|   ^    |  位异或  |   3^7    |
|   <<   |   左移   |  10<<1   |
|   >>   |   右移   |  10>>1   |

# 聚合函数(求和、求最大最小，求平均值)

## SUM函数
用于求和，只能用于数字类型，字符类型的统计结果为0，日期类型统计结果是毫秒数相加
```sql
SELECT SUM(sal) FROM t_emp
WHERE deptno IN(10,20);
```
## MAX函数
用于获得非空值的最大值
```sql
#求月收入最高的员工
SELECT MAX(sal+IFNULL(comm,0)) FROM t_emp 
WHERE deptno IN(10,20);
```
```sql
#查询员工名字最长的是几个字符
SELECT MAX(LENGTH(ename)) FROM t_emp;
```
## MIN函数
用于获得非空值的最小值
```sql
#查询员工最小编号和最早入职日期
SELECT MIN(empno) FROM t_emp;
SELECT MIN(hiredate) FROM t_emp;
```

## AVG函数
`AVG`函数用于获得非空值的平均值，非数字数据统计结果为0
```sql
SELECT AVG(sal+IFNULL(comm,0)) AS 'avg_sal'
FROM t_emp;
```
## COUNT函数
`COUNT(*)`用于获得包含空值的记录数，`COUNT(列名)`用于获得包含非空值的记录数
```sql
SELECT COUNT(*),COUNT(comm) FROM t_emp;
```
```sql
#查询10和20部门中，总工资超过2000元并且工龄超过15年的员工人数
SELECT count(*) from t_emp
WHERE deptno IN(10,20) 
AND (sal+IFNULL(comm,0))>=2000
AND DATEDIFF(NOW(),hiredate)/365>=15;
```
不能在 `WHERE` 子句中直接使用聚合函数（如 `AVG()`）。这是因为 `WHERE` 子句用于过滤行，而聚合函数是用于计算多行值的单个值
```sql
#查询1985年以后入职的员工，底薪超过公司平均底薪的员工数量
SELECT COUNT(*) from t_emp
WHERE hiredate>='1985-01-01'
AND sal>=AVG(sal);
```
# 分组查询
## GROUP BY
通过一定规则将一个数据集划分成若干个小的区域，然后针对每个小区域分别进行数据汇总处理
```sql
#查看每个部门平均薪资
SELECT deptno,ROUND(AVG(sal))
FROM t_emp GROUP BY deptno;
#查看每个部门里，每种职位的人员数量和平均底薪
SELECT deptno,job,COUNT(*),ROUND(AVG(sal))
FROM t_emp GROUP BY deptno,job
ORDER BY deptno;
```

## 对SELECT子句的要求

当查询语句如果含有GROUP BY子句，SELECT子句中内容就必须要遵守规定，SELECT子句中可以包括聚合函数，或者GROUP BY子句的分组列，其余内容均不可以出现在SELECT子句中。
```sql
SELECT deptno,COUNT(*),AVG(sal)
FROM t_emp GROUP BY deptno;
#出现sal会报错，一对多
SELECT deptno,COUNT(*),AVG(sal),sal
FROM t_emp GROUP BY deptno;
```
## 汇总计算
对分组结果再次做汇总计算
```sql
SELECT deptno,COUNT(*),AVG(sal),MIN(sal),MAX(sal),SUM(sal)
FROM t_emp GROUP BY deptno WITH ROLLUP;
```
## GROUP_CONCAT函数
把分组查询中的某个字段拼接成一个字符串
```sql
#查询每个部门内底薪超过2000元的人数和员工姓名
SELECT deptno,COUNT(*),GROUP_CONCAT(ename)
from t_emp WHERE sal>=2000 
GROUP BY deptno;
```
## HAVING子句
因为WHERE子句没法使用聚合函数，因此用HAVING子句替代
```sql
#报错1111 - Invalid use of group function
SELECT deptno FROM t_emp
WHERE AVG(sal)>=2000 GROUP BY deptno;

SELECT deptno FROM t_emp
GROUP BY deptno HAVING AVG(sal)>=2000;
```
```sql
#查询每个部门中，1982年以后入职的员工超过2个人的部门编号
SELECT deptno FROM t_emp WHERE hiredate>='1982-01-01' 
GROUP BY deptno HAVING COUNT(*)>=2
ORDER BY deptno ASC;
```
特殊用法（having在where后执行不建议）
```sql
SELECT deptno,COUNT(*) FROM t_emp 
GROUP BY 1 HAVING deptno IN(10,20);
```
# 表连接查询
## 从多张表中提取数据
规定了连接条件的表连接语句，就不会出现笛卡尔积
```sql
SELECT e.empno,e.ename,d.dname 
FROM t_emp e JOIN t_dept d 
ON e.deptno=d.deptno;
```
## 内连接
最常见的一种表连接，用于查询多张关系表符合连接条件的记录
```sql
SELECT …… FROM 表1
[INNER] JOIN 表2 ON 条件
[INNER] JOIN 表3 ON 条件
……
SELECT …… FROM 表1 JOIN 表2 WHERE 连接条件;
SELECT …… FROM 表1,表2 WHERE 连接条件;
```
```sql
SELECT e.empno,e.ename,d.dname 
FROM t_emp e JOIN t_dept d WHERE e.deptno=d.deptno;
SELECT e.empno,e.ename,d.dname 
FROM t_emp e , t_dept d WHERE e.deptno=d.deptno;
```
## 内连接练习1

内连接的数据表不一定必须有同名字段，只要字段之间符合逻辑关系就可以
```sql
#查询每个员工的工号、姓名、部门名称、底薪、职位、工资等级
SELECT e.empno,e.ename,d.dname,e.sal,e.job,s.grade
FROM t_emp e 
JOIN t_dept d ON e.deptno=d.deptno
JOIN t_salgrade s ON e.sal BETWEEN s.losal AND s.hisal;
```
## 内连接练习2
```sql
#查询与SCOTT相同部门的员工都有谁
SELECT ename FROM t_emp
WHERE deptno=(SELECT deptno FROM t_emp where ename='SCOTT') AND ename!='SCOTT';
#内连接查询方式，相同数据表也可以做表连接
SELECT e2.ename FROM t_emp e1 JOIN t_emp e2 ON e1.deptno=e2.deptno
WHERE e1.ename='SCOTT' AND e2.ename!='SCOTT';
```

## 内连接查询联系1
```sql
#查询底薪超过公司平均底薪的员工信息
SELECT e.empno,e.ename,e.sal
FROM t_emp e JOIN  
(SELECT AVG(sal) AS avg FROM t_emp) t ON e.sal>=t.avg;
```
```sql
#查询RESEARCH部门的人数、最高底薪、最低底薪、平均底薪、平均工龄
SELECT COUNT(*),MAX(e.sal),MIN(e.sal),AVG(e.sal),FLOOR(AVG(DATEDIFF(NOW(),e.hiredate)/365))
FROM t_emp e JOIN
t_dept d ON e.deptno=d.deptno 
WHERE d.dname='RESEARCH';
```
## 内连接查询联系2
```sql
#查询每种职业的最高工资、最低工资、平均工资、最高工资等级和最低工资等级
SELECT 
e.job,
MAX(e.sal+IFNULL(comm,0)),
MIN(e.sal+IFNULL(comm,0)),
AVG(e.sal+IFNULL(comm,0)),
MAX(s.grade),MIN(s.grade)
FROM t_emp e JOIN t_salgrade s ON (e.sal+IFNULL(comm,0)) BETWEEN s.losal AND s.hisal
GROUP BY e.job;
```
```sql
#查询每个底薪超过部门平均底薪的员工信息
SELECT e.empno,e.ename,e.sal
FROM t_emp e JOIN (SELECT deptno,AVG(sal) AS avg FROM t_emp GROUP BY deptno) t ON e.deptno=t.deptno 
WHERE e.sal >= t.avg;
#等价
SELECT e.empno,e.ename,e.sal
FROM t_emp e JOIN (SELECT deptno,AVG(sal) AS avg FROM t_emp GROUP BY deptno) t 
ON e.deptno=t.deptno AND e.sal >= t.avg;
```
## 外连接
外连接与内连接的区别在于，除了符合条件的记录之外，结果集中还会保留不符合条件的记录
```sql
#查询各部门人员信息
SELECT e.empno,e.ename,d.dname
FROM t_emp e LEFT JOIN t_dept d ON e.deptno=d.deptno;
```
## 左连接和右连接
左外连接就是保留左表所有的记录，与右表做连接。如果右表有符合条件的记录就与左表连接。如果右表没有符合条件的记录，就用NULL与坐标连接。右外连接也是如此。
表A
|            id            |   name   |
| :----------------------: | :------: |
|            1             |   John   |
|            2             |   Emma   |
|           表B            |          |
|            id            |  score   |
|           ----           | -------  |
|            1             |    85    |
|            3             |    90    |
| 左连接 A LEFT JOIN B结果 |          |
|           A.id           |  A.name  |
|          ------          | -------- |
|            1             |   John   |
|            2             |   Emma   |
| 右连接A RIGHT JOIN B结果 |          |
|           A.id           |  A.name  |
|          ------          | -------- |
|            1             |   John   |
|           NULL           |   NULL   |

## 外连接练习1
```sql
#查询每个部门的名称和部门的人数
SELECT d.dname,COUNT(*)
FROM t_dept d LEFT JOIN t_emp e ON d.deptno=e.deptno
GROUP BY d.deptno;
#在第一个查询中，使用 `COUNT(*)`，这意味着对于每个部门，无论是否存在与之匹配的员工记录，都将计算行的数量。因为 `COUNT(*)` 会计算包含NULL值的行，所以即使部门没有员工，也会得到结果，结果为1（因为至少有一个NULL值）
SELECT d.dname,COUNT(e.deptno)
FROM t_dept d LEFT JOIN t_emp e ON d.deptno=e.deptno
GROUP BY d.deptno;
#在第二个查询中，使用 `COUNT(e.deptno)`，这意味着对于每个部门，只有当存在与之匹配的员工记录时，才会计算行的数量。因为 `COUNT(column)`会忽略 NULL 值，所以如果部门没有员工，则会得到结果为0
```
`UNION`关键字可以将多个查询语句的结果集进行合并 : (查询语句)`UNION`(查询语句)`UNION`(查询语句)
```sql
#查询每个部门的名称和部门的人数?如果没有部门的员工，部门名称用NULL代替
(SELECT d.dname,COUNT(e.deptno)
FROM t_dept d LEFT JOIN t_emp e ON d.deptno=e.deptno
GROUP BY d.deptno)
UNION
(SELECT d.dname,COUNT(*)
FROM t_dept d RIGHT JOIN t_emp e ON d.deptno=e.deptno
GROUP BY d.deptno);
```

## 子查询
 `WHERE`子查询最简单，最容易理解，但是却是效率很低的子查询
```sql
#查询底薪超过公司平均底薪的员工的信息  每条记录都要重新执行子查询
SELECT empno,ename,sal 
FROM t_emp 
WHERE sal>=(SELECT AVG(sal) FROM t_emp);
```
`FROM`子查询只会执行一次，所以查询效率很高
```sql
SELECT e.empno,e.ename,e.sal,t.avg
FROM t_emp e JOIN
(SELECT deptno,AVG(sal) as avg FROM t_emp GROUP BY deptno) t ON e.deptno=t.deptno AND e.sal>=t.avg;
```
`SELECT`子查询每输出一条记录的时候都要执行一次，查询效率很低
```sql
SELECT e.empno,e.ename, 
(SELECT dname FROM t_dept WHERE deptno=e.deptno)
FROM t_emp e;
```
## 单行子查询和多行子查询

 1. 单行子查询的结果集只有一条记录，多行子查询结果集有多行记录
 2. 多行子查询只能出现在WHERE子句和FROM子句中
```sql
#如何用子查询查找FORD和MARTIN两个人的同事
SELECT ename,deptno FROM t_emp 
WHERE deptno IN(SELECT deptno FROM t_emp WHERE ename IN('FORD','MARTIN')) AND ename NOT IN('FORD','MARTIN');
#替换用FROM子查询查找
SELECT e.ename
FROM t_emp e JOIN (SELECT deptno,ename FROM t_emp WHERE ename IN('FORD','MARTIN')) t ON e.deptno=t.deptno AND e.ename NOT IN('FORD','MARTIN');
```
## WHERE子句中的多行子查询
`WHERE`子句中，可以使用IN、ALL、ANY、EXISTS关键字来处理多行表达式结果集的条件判断
```sql
#查询比FORD和MARTIN底薪都高的员工信息
SELECT ename FROM t_emp WHERE sal>=ALL(SELECT sal FROM t_emp WHERE ename IN('FORD','MARTIN')) AND ename NOT IN('FORD','MARTIN');
```
## `EXISTS`关键字是把原来在子查询之外的条件判断，写到了子查询里面
`SELECT …… FROM 表名 WHERE [NOT] EXISTS(子查询);`
```sql
#查询工资等级是3级或者4级的员工信息
SELECT empno,ename,sal 
FROM t_emp
WHERE EXISTS(
SELECT grade FROM t_salgrade
WHERE sal BETWEEN losal AND hisal 
AND grade IN(3,4));
```

# 数据插入操作

## INSERT语句
  可以向数据表写入记录，可以是一条记录，也可以是多条记录
```sql
INSERT INTO 表名(字段1,字段2,……)
VALUES(值1,值2,……),(值1,值2,……);
```
```sql
INSERT INTO t_dept(deptno,dname,loc)
VALUES (50,'技术部','北京'),(60,'后勤部','北京'),(70,'保安部','北京');
```
```sql
#向技术部添加一条员工记录
INSERT INTO t_emp
(empno,ename,job,mgr,hiredate,sal,comm,deptno)
VALUES(8001,'刘娜','SALESMAN',8000,'1988-12-20',2000,NULL,(SELECT deptno FROM t_dept WHERE dname='技术部'));
```
## INSERT语句方言
```sql
INSERT INTO 表名 SET 字段1=值1,字段2=值2,……;
```
```sql
INSERT INTO t_emp
SET empno=8002,ename='JACK',job='SALESMAN',mgr=8000,hiredate='1985-3-14',sal=2500,comm=NULL,deptno=50;
#删除指定行
DELETE FROM t_emp WHERE empno=8002;
```
## IGNORE关键字

`IGNORE`关键字会让`INSERT`只插入数据库不存在的记录
```sql
INSERT [IGNORE] INTO 表名……;
INSERT IGNORE INTO t_dept(deptno,dname,loc)
VALUES(40,'技术部','北京');
> Affected rows: 0
> 查询时间: 0s
```
## UPDATE语句
`UPDATE`语句用于修改表的记录
```sql
UPDATE [IGNORE] 表名
SET 字段1=值1,字段2=值2,……
[WHERE 条件1 ……]
[ORDER BY ……]
[LIMIT ……]；
```
## 课堂练习1
```sql
#把每个员工的编号和上司的编号+1，用ORDER BY子句完成
UPDATE t_emp
SET empno=empno+1,mgr=mgr+1
ORDER BY empno DESC;
```
```sql
#把月收入前三名的员工底薪减100元，用LIMIT子句完成
UPDATE t_emp SET sal=sal-100
ORDER BY sal+IFNULL(comm,0) DESC LIMIT 3;
```
## 课堂练习2
```sql
#把10部门中，工龄超过20年的员工，底薪增加200元
UPDATE t_emp SET sal=sal+200
WHERE deptno=10 AND DATEDIFF(NOW(),hiredate)/365>20;
```
## `UPDATE`语句的表连接（一）
```sql
UPDATE 表1 JOIN 表2 ON 条件
SET 字段1=值1,字段2=值2,……
```
## `UPDATE`语句的表连接（二）
```sql
UPDATE 表1，表2
SET 字段1=值1,字段2=值2,……
WHERE 连接条件;
```
```sql
#把ALLEN调往RESEARCH部门，职务调整为ANALYST
UPDATE t_emp e JOIN t_dept d 
SET e.job='ANALYST',e.deptno=d.deptno
WHERE e.ename='ALLEN' AND d.dname='RESEARCH';
```
```sql
#把底薪低于公司平均底薪的员工，底薪增加150元
UPDATE t_emp e JOIN (SELECT AVG(sal) AS avg FROM t_emp) t
SET sal=sal+150
WHERE e.sal<t.avg;
#gpt说两个没啥区别，但我感觉下面这个更好一点
UPDATE t_emp e JOIN (SELECT AVG(sal) AS avg FROM t_emp) t ON e.sal<t.avg
SET sal=sal+150;
```
## `UPDATE`语句的表连接（三）
`UPDATE`语句的表连接既可以是内连接，又可以是外连接
```sql
UPDATE 表1 [LEFT|RIGHT] JOIN 表2 ON 条件
SET 字段1=值1,字段2=值2,……；
```
```sql
#把没有部门的员工，或者SALES部门低于2000元底薪的员工，都调往20部门
UPDATE t_emp e LEFT JOIN t_dept d on e.deptno=d.deptno 
SET e.deptno=20 WHERE e.deptno IS NULL OR (d.dname='SALES' AND e.sal<2000);
```

# 数据删除操作
`DELETE`语句用于删除记录，语法如下:
```sql
DELETE [IGNORE] FROM 表名
[WHERE 条件1,条件2,……]
[ORDER BY……]
[LIMIT……];
```
## 课堂练习1
```sql
#删除10部门中，工龄超过20年的员工记录
DELETE FROM t_emp
WHERE deptno=10 AND DATEDIFF(NOW(),hiredate)/365>=20;
#删除20部门中工资最高的员工记录
DELETE FROM t_emp
WHERE deptno=20 ORDER BY sal+IFNULL(comm,0) DESC LIMIT 1;
```
## 课堂练习2（DELETE语句的表连接一）
```sql
DELETE 表1,…… FROM 表1 JOIN 表2 ON 条件
[WHERE 条件1,条件2,……]
[ORDER BY……]
[LIMIT……];
```
```sql
#删除SALES部门和该部门的全部员工记录
DELETE e,d FROM t_emp e JOIN t_dept d ON e.deptno=d.deptno 
WHERE d.dname='SALES';
#删除每个低于部门平均底薪的员工记录
DELETE e FROM t_emp e JOIN
(SELECT deptno,AVG(sal) AS avg FROM t_emp GROUP BY deptno) t ON e.deptno=t.deptno AND e.sal<t.avg;
```
```sql
#删除员工KING和他的直接下属的员工记录，用表连接实现 （标准答案）
DELETE e
FROM t_emp e JOIN 
(SELECT empno FROM t_emp WHERE ename='KING') t 
ON e.mgr=t.empno OR e.empno=t.empno;
#自己写的，结果是一样的
DELETE e1
FROM t_emp e1 LEFT JOIN t_emp e2 on e1.mgr=e2.empno
WHERE e2.ename='KING' OR e1.ename='KING';
```
## DELETE语句的表连接二

DELETE语句的表连接既可以是内连接，又可以是外连接
 ```sql
DELETE 表1,…… FROM 表1 [LEFT|RIGHT] JOIN 表2 ON 条件……;
 ```
```sql
#删除SALES部门的员工，以及没有部门的员工
DELETE e
FROM t_emp e LEFT JOIN t_dept d on e.deptno=d.deptno
WHERE d.dname='SALES' OR e.deptno IS NULL;
```

## 快速删除数据表全部记录
`DELETE`语句是在事务机制下删除记录，删除记录之前，先把将要删除的记录保存到日志文件里，然后在删除记录。
`TRUNCATE`语句在事务机制之外删除记录，速度远超过DELETE语句
```sql
TRUNCATE TABLE 表名;
```

# MYSQL函数

## 数字函数（一）
| 函数  | 功能                 | 用例        |
| ----- | -------------------- | ----------- |
| ABS   | 绝对值               | ABS(-100)   |
| ROUND | 四舍五入             | ROUND(4.62) |
| FLOOR | 强制舍位到最近的整数 | FLOOR(9.9)  |
| CEIL  | 强制进位到最近的整数 | CEIL(3.2)   |
| POWER | 幂函数               | POWER(2,3)  |
| LOG   | 对数函数             | LOG(7,3)    |
| LN    | 对数函数             | LN(10)      |
## 数字函数（二）
| 函数    | 功能                 | 用例        |
| ------- | -------------------- | ----------- |
| SQRT    | 开平方               | SQRT(9)     |
| PI      | 四舍五入             | PI()        |
| SIN     | 强制舍位到最近的整数 | SIN(1)      |
| COS     | 强制进位到最近的整数 | COS(1)      |
| TAN     | 幂函数               | TAN(1)      |
| COT     | 对数函数             | COT(1)      |
| RADIANS | 角度转换弧度         | RADIANS(30) |
| DEGREES | 弧度转换角度         | DEGREES(1)  |
## 获取系统时间函数

 - `NOW()`函数能获得系统日期和时间，格式yyyy-MM-dd hh:mm:ss
 - `CURDATE()`函数能获得当前系统日期，格式yyyy-MM-dd
 - `CURTIME()`函数能获得当前系统时间，格式hh:mm:ss
```sql
SELECT NOW(),CURDATE(),CURTIME();
```
## 日期格式化函数（一）
 - `DATE_FORMAT()`函数用于格式化日期，返回用户想要的日期格式
```sql
DATE_FORMAT(日期,表达式)
```
```sql
SELECT ename,DATE_FORMAT(hiredate,'%Y') FROM t_emp;
```
| 占位符 | 作用       | 占位符 | 作用       |
| ------ | ---------- | ------ | ---------- |
| %Y     | 年份       | %m     | 月份       |
| %d     | 日期       | %w     | 星期(数字) |
| %W     | 星期(名称) | %j     | 本年第几天 |
| %U     | 本年第几周 | %H     | 小时(24)   |
| %h     | 小时(12)   | %i     | 分钟       |
| %s     | 秒         | %r     | 时间(24)   |
| %T     | 时间(12)   |        |            |
## 课堂练习
```sql
#利用日期函数，查询明年你的生日是星期几
SELECT DATE_FORMAT('2024-06-21','%W');
#利用日期函数，查询1981年上半年入职的员工有多少人
SELECT COUNT(*)
FROM t_emp WHERE DATE_FORMAT(hiredate,'%Y')=1981 AND DATE_FORMAT(hiredate,'%m')<6;
```
 - MYSQL数据库里，两个日期不能直接加减，日期也不能与数字加减
```sql
SELECT hiredate+1 FROM t_emp; #该操作会将日期转换为数字后加1不再是日期格式
```
## 日期偏移计算
 - DATE_ADD()函数可以实现日期的偏移计算，而且时间单位很灵活
 `DATE_ADD`(日期，INTERVAL 偏移量 时间单位)
```sql
SELECT DATE_ADD(NOW(),INTERVAL 100 DAY);
SELECT DATE_ADD(NOW(),INTERVAL 100 MINUTE);
SELECT DATE_FORMAT(DATE_ADD(DATE_ADD(NOW(),INTERVAL -6 MONTH),INTERVAL -3 DAY),'%Y-%m-%d');
```
## 计算日期之间相隔的天数
 - DATEDIFF()函数用来计算两个日期之间相差的天数
 `DATEDIFF(日期,日期)`
## 字符函数
|   函数    |             功能              |               用例                |
| :-------: | :---------------------------: | :-------------------------------: |
|   LOWER   |         转换小写字符          |           LOWER(ename)            |
|   UPPER   |         转换大写字符          |           UPPER(ename)            |
|  LENGTH   |           字符数量            |           LENGTH(ename)           |
|  CONCAT   |          连接字符串           |          CONCAT(sal,'$')          |
|   INSRT   |        字符出现的地方         |         INSTR(ename,"A")          |
|  INSERT   |         插入/替换字符         |     INSERT("你好",1,0,"先生")     |
|  REPLACE  |           替换字符            | REPLACE("你好先生","先生","女士") |
|  SUBSTR   | 截取字符串(起始位置,结束位置) |      SUBSTR("你好世界",3,4)       |
| SUBSTRING |   截取字符串(起始位置,长度)   |     SUBSTRING("你好世界",3,2)     |
|   LPAD    |         左侧填充字符          |       LPAD("Hello",10,"*")        |
|   RPAD    |         右侧填充字符          |       RPAD("Hello",10,"*")        |
|   TRIM    |         去除首尾空格          |      TRIM("  你好先生    ")       |
```sql
INSERT(str, pos, len, newstr)
-`str`是原始字符串。
-`pos`是插入新字符串的起始位置（从1开始）。
-`len`是要替换的字符数。
-`newstr`是要插入的新字符串。
```
```sql
SELECT SUBSTR("你好世界",3,4),SUBSTRING("你好世界",3,2);
SELECT LPAD(SUBSTRING('15958104798',8,4),11,"*");
SELECT RPAD(SUBSTRING('孙益铭',1,1),LENGTH('孙益铭')/3,'*'); #汉字字符长度是三倍
SELECT TRIM('    Hello World        ');
```
## 条件函数
 - SQL语句中可以利用条件函数来实现编程语言里的条件判断
 `IFNULL(表达式,值)`
  `IF(表达式,值1,值2)`
```sql
#中秋节公司发放礼品,SALES部门发放礼品A,其余部门发放礼品B,打印每名员工获得的礼品
SELECT 
  e.ename,e.deptno,d.dname,
	IF(d.dname='SALES','礼品A','礼品B') 
FROM t_emp e JOIN t_dept d on e.deptno=d.deptno;
```
## 复杂条件判断
```sql
CASE
  WHEN 表达式 THEN 值1
  WHEN 表达式 THEN 值2
  ……
  ELSE 值N
END
```
```sql
#公司年庆决定组织员工集体旅游，每个部门旅游目的地是不同的。SALES部门去P1地点，ACCOUNTING部门去P2地点，RESEARCH部门去P3地点，查询每名员工的旅行地点。
SELECT e.empno,e.ename,
	CASE 
		WHEN d.dname='SALES' THEN 'P1'
	  WHEN d.dname='ACCOUNTING' THEN 'P2'
	  WHEN d.dname='RESEARCH' THEN 'P3'		 
	END AS place
FROM t_emp e JOIN t_dept d ON e.deptno=d.deptno;
```

| 序号 |              条件              |  涨幅  |
| :--: | :----------------------------: | :----: |
|  1   |    SALES部门中工龄超过20年     |  10%   |
|  2   |    SALES部门中工龄不满20年     |   5%   |
|  3   |         ACCOUNTING部门         | +300元 |
|  4   | RESEARCH部门里低于部门平均底薪 | +200元 |
|  5   |         没有部门的员工         | +100元 |
```sql
UPDATE t_emp e LEFT JOIN t_dept d ON e.deptno=d.deptno
LEFT JOIN (SELECT deptno,AVG(sal) AS avg FROM t_emp GROUP BY deptno) t ON e.deptno=t.deptno
SET e.sal=(
	CASE 
	  WHEN d.dname='SALES' AND DATEDIFF(NOW(),hiredate)/365>=20 THEN e.sal*1.1
	  WHEN d.dname='SALES' AND DATEDIFF(NOW(),hiredate)/365<20 THEN e.sal*1.05
	  WHEN d.dname='ACCOUNTING' THEN e.sal+300
	  WHEN d.dname='RESEARCH' AND e.sal<t.avg THEN e.sal+200
	  WHEN e.deptno IS NULL THEN e.sal+100
		ELSE e.sal
  END
);
```
