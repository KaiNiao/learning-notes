# 数据库

- DB：database 数据库，存储数据的仓库，保存了一系列有组织的数据；
- DBMS：database management system 数据库管理系统（软件），数据库是通过DBMS创建和操作的容器。分类为两大类，基于共享文件系统的DBMS（如Access）和基于C/S（客户端-服务端）的DMBS（如MySQL）；
- SQL：structure query language 结构化查询语言，用来与数据库通信的通用语言。

##  MySQL

主要是指安装MySQL的服务端。mysql代指客户端，mysqld代指服务端。一般安装的是服务端。

### 基础

#### 安装 MySQL 8.0.13

推荐版本5.5，linux下三种安装方式：yum、tar.gz、rpm。

第一次安装：

- ZIP Archive版是免安装的。只要解压就行了。和安装版的没什么不同，但就是不需要安装；

- 新建my.ini文件，修改目录（配置文件），更改后重启服务；
- 以管理员身份打开cmd窗口后，将目录切换到你解压文件的bin目录；
- cmd下输入.\mysqld install开始安装；
- net start mysql（服务名），启动mysql服务。关闭服务net stop mysql；
- mysql -uroot -p 命令打开MySQL，password 912（root），初始密码为空；
- 可能报错，如10061，原因是服务MySQL未开启；如3534，服务启动后停止，data文件夹；

服务启动后自动停止：

<https://blog.csdn.net/SantoriniBat/article/details/82659010>！！！！

- cmd下输入 mysqld --console  可以查看报错信息；
- MySQL本地安装配置环境变量，新建MYSQL_HOME变量。

重装时的命令：

- mysqld -remove，移除mysqld服务
- mysqld --initialize-insecure，自动生成data文件夹
- mysqld --install，安装mysqld服务

<https://jingyan.baidu.com/article/597035521d5de28fc00740e6.html>

<https://www.cnblogs.com/jianz/p/6756771.html>

<https://blog.csdn.net/github_38832708/article/details/83037241>

#### 常用命令

**sql语句以分号;结尾。**DOS命令结尾不用加分号。

- 使用mysql自带的客户端登录，只限于root用户，直接输密码进入。mysql>表示进入了客户端；
- 使用windows自带的客户端登录，mysql -uroot -p 命令打开MySQL，password 912；root；
- 退出，exit 或者 crtl+c（亲测无效）；
- mysql --version 或者 mysql> select version(); 查看当前版本号
- show databases; 显示当前所有数据库
- create database python3 charset=utf8; 创建数据库
- use python3; 使用数据库
- select databases(); 查看当前使用的数据库
- create table 表名students(列字段+类型+约束); 创建表，int默认长度11
- id int auto_increment primary key not null, 创建主键
- show tables; 查看当前表，from 表名; 可以查看其他数据库中的表
- desc students; 显示表的结构
- insert into students values(); 表中增加字段一行
- insert into stuinfo (id, name) values(1, 'John'); 添加一行的部分列，没有指定的列取默认值
- select * from students where 条件; 显示表中数据
- update students set 列字段名=值 where 条件; 修改表中数据，可以进行逻辑删除
- delete from students where 条件; 物理删除
- mysqldump -uroot -p python3 > ~/Desktop/bak.sql; 数据库备份
- mysql -uroot -p py3 < ~/Desktop/bak.sql; 数据库恢复
- alter table 表名 add|modify|drop 列名 类型 约束; 修改表
- alter table stu modify column isDelete bit not null default 0;
- show create table students; 查看表的创建语句，ENGINE=InnoDB
- set names gbk 修改命令行窗口的编码格式，当中文乱码时使用。

### DQL

数据查询语言。

分类存储减少数据冗余。

不区分字符与字符串，统称字符型。字符型和日期型使用单引号''，数值型不需要。

课程中相关sql代码：<https://blog.csdn.net/qq_41855420/article/details/99726758>

#### 查询列表

Query查询语句，select。查询的结果是一个虚拟的表格。

语法如下：select 查询列表 from 表名

查询列表包括：

- 表中字段、常量值、表达式、函数，没有顺序。*有顺序；
- 着重号``用于区分字段与关键字；
- 可以起别名，空格分隔的别名加上“”；
- distinct字段去重；
- +的唯一作用是运算符，不能实现字符串拼接。如果操作数是字符型，则将字符型转换成数值型，如'123'转换为123。如果转换失败，则将字符型转换成0，如'john'转换为0。如果有操作数为NULL，则运算结果为Null；
- 因此字符串拼接使用concat()函数。如果有操作数为NULL，则运算结果为Null。如 SELECT CONCAT(last_name,' ', first_name) AS 姓名 FROM employees;
- ifnull()函数可以指定NULL的返回值。isnull()函数NULL返回1。

#### 条件查询

部分行，语法如下：select 查询列表 from 表名 where 筛选条件

执行顺序如下：from --> where --> select

筛选条件分类：

1. 条件运算符：>、<、=、<>（!=）、>=、<=；

2. 逻辑运算符：&&（and)、||(or)、！(not)，用于连接条件表达式；

3. 模糊查询：

- like：大小写不区分，一般和通配符搭配使用，%任意多个字符，包括0个。_任意单个字符。支持转义escape。可以判断字符型与数值型；
- between and：等价于>= and <=，因此前后顺序不能变，包含临界值；
- in：等价于=，小括号内包括同一类型或兼容数据；
- is null / is not null：字段=NULL不能用于判断空。如commission_pct IS NULL。is null只能判断NULL值，不能判断其他具体的值。

#### 排序查询

语法如下：select 查询列表 from 表名 where 筛选条件 order by 排序列表【asc / desc】，默认升序asc。

- 排序列表支持字段、别名、表达式；

- 多个字段排序，逗号分隔；
- order by 一般放在查询语句的最后面，limit子句之前。

#### 函数查询

语法如下：select 函数名(实参列表) 【from 表名】

1. 单行函数

- 字符函数：如 length（utf8字符集中英文一个字符，汉字三个字符）、concat、upper、lower、substr（指定索引与长度，**索引从1开始**，字符长度）、instr（返回起始索引，找不到返回0）、trim（前后截取）、lpad（使用指定的字符实现左填充指定长度）、replace（字符替换）；
- 数学函数：如 round（可以指定小数点后位数）、truncate（截断小数点后指定位数）、mod（a-a/b*b实现取余运算）；
- 日期函数：如 now（返回当前系统日期+时间）、curdate、curtime、year、str_to_date（字符转日期，如 WHERE hiredate = STR_TO_DATE('4-3 1992', '%c-%d %Y')）、date_format（日期转字符）；
- 流程控制函数：如 if（类似于三元运算符）、case（字段或表达式 when then else end，分等值判断与区间判断）。

2. 多行函数

又称为聚合函数、分组函数，实现数据的统计。多行函数都忽略NULL值，都可以搭配distinct实现去重统计。

和分组函数一同查询的字段要求是group by后的字段，其他不可以。因此查询结果不是表格。

- sum：支持数值型
- avg：支持数值型
- max：支持数值型、字符型、日期型
- min：支持数值型、字符型、日期型
- count：支持数值型、字符型、日期型。如 **count(*)结果集的总行数**，包括NULL。count(1)增加一列1。对于存储引擎MyISAM内部有计数器，count( *)效率高。对于InnoDB，两者效率类似。

#### 分组查询

语法如下：select 分组函数，**列（要求是出现在group by后的字段）** from 表名 【where 筛选条件】 group by 分组列表。

- 常用语如“每个**”，该字段用于分组。多个分组字段以逗号分隔，没有顺序要求；

- 分组查询中的筛选条件分为两种，**分组后筛选使用having，分组前筛选使用where**；

- **查询原始表中不包含的字段时，需要分步完成，先生成虚拟表。**

如查询每个工种有奖金的员工的最高工资大于12000的工种编号与最高工资：

~~~sql
SELECT
job_id, MAX(salary)
FROM employees
WHERE commission_pct IS NOT NULL	# 筛选条件1，先筛选后分组，原始表中包含该字段
GROUP BY job_id
HAVING MAX(salary) > 12000;			# 筛选条件2，先分组后筛选，select生成的虚拟表中包含该字段

# 多个分组字段 
SELECT department_id, job_id, AVG(salary)
FROM employees
GROUP BY department_id, job_id;
~~~

#### 连接查询

**外连接存在NULL。内连接不存在NULL。**

MySQL对任何关联都执行**嵌套循环**关联操作，即MySQL先在一个表中循环取出单条数据，然后在嵌套循环到下一个表中寻找匹配的行，依次下去，直到找到所有表中匹配的行为止。然后根据各个表匹配的行，返回查询中需要的各个列。

又称为多表查询。重名字段避免歧义，**可以给表起别名**。

没有添加**有效的连接条件**时，出现笛卡尔积现象，也就是多表所有行完全连接。如：

~~~sql
SELECT
`name`, boyName
FROM beauty, boys; # 12 * 4 笛卡尔积

SELECT
`name`, boyName
FROM beauty, boys
WHERE boyfriend_id = boys.id; # 等值连接
~~~

连接查询按照版本分类：

- sql92语法：select 查询列表 from 表1，表2 where 连接条件 and 筛选条件
- sql99语法：select 查询列表 from 表1 【连接类型】join 表2 on 连接条件 where 筛选条件，优点是**连接条件与筛选条件相分离**，可读性较高。

sql92语法连接查询之内连接（不支持外连接）：

- 等值连接：两张表，根据 where 条件进行筛选，结果为**多表交集**；
- **非等值连接**：两张表，从一张表中逐个取出每一行匹配另一张表的每一行。on后面不一定是等于号；
- 自连接：一张表当做多张表使用。

```sql
# 自连接：查询员工名与上级的名称，将表分别视为员工表与上级表。
SELECT e.employee_id, e.last_name, m.employee_id, m.last_name
FROM employees AS e, employees AS m
WHERE e.employee_id = m.manager_id;
```

sql99语法连接查询按照功能分类（join）：

1. 内连接（inner，可省略）：

- 等值连接：两张表，执行效果与sql92相同。不分不从表，可以调换顺序；
- 非等值连接：两张表，从一张表中逐个取出每一行匹配另一张表的每一行；
- 自连接：一张表当做多张表使用，如查询员工名与上级的名称，将表分别视为员工表与上级表。

~~~sql
# 非等值连接：查询员工个数>10的工资级别，并按照工资级别降序
SELECT g.grade_level, COUNT(*)
FROM employees AS e
INNER JOIN job_grades AS g
ON e.salary BETWEEN g.lowest_sal AND g.highest_sal
GROUP BY g.grade_level
HAVING COUNT(*) > 10
ORDER BY g.grade_level DESC;
~~~

2. 外连接（outer，可省略）：

用于查询一个表中有，另一个表中没有的记录。**分主从表**。

外连接的查询结果为主表中的所有记录。从表中没有的主表的记录用NULL填充。

因此，外连接的查询结果 = 内连接的查询结果 + 主表中有而从表中没有的记录。

- 左外连接（left）：left左边是主表。
- 右外连接（right）：right右边是主表。
- 全外连接（full）：两表并集，不分左右。MySQL不支持。**可以使用union实现合并与去重。**

~~~sql
# 查询哪个部门没有员工，因此部门表是主表
SELECT d.department_id, e.* 			# 两表合并，从表中缺失部分为NULL
FROM employees AS e
RIGHT JOIN departments AS d
ON e.department_id = d.department_id
WHERE e.employee_id IS NULL; 			# 主键不为空
~~~

3. 交叉连接（cross）：

没有on条件，查询结果为笛卡尔乘积。

常见的几种查询如下图所示。![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170502214408477?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VzZXl1a3Vp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 子查询

嵌套在其他语句中的select语句，称为子查询或内查询、嵌套查询。

子查询放在小括号内。子查询的执行优先于主查询。

按照结果集的行列数分类：

- 标量子查询：结果集为一行一列，又称为单行子查询，一般搭配单行操作符使用（如 =、>、<）；
- 列子查询：最常见。结果集为一列多行，又称为多行子查询，一般搭配多行操作符使用（如 in / not in、any / some、all）。结果集是列表，用于主查询；
- 行子查询：结果集为一行多列；
- 表子查询：结果集为多行多列。

按照子查询可以出现的位置进行分类：

- **where / having后面**：最常见。支持标量子查询、列子查询、行子查询；

```sql
# 1. 标量子查询
# 查询谁的工资比 Abel 高
SELECT last_name, salary
FROM employees
WHERE salary > (						# 括号内结果为单行单列
	SELECT salary
	FROM employees
	WHERE last_name = 'Abel'
);

# 2. 列子查询
# 返回location_id是1400或1700的部门中的所有员工的姓名
SELECT last_name
FROM employees
WHERE department_id IN (				# 括号内结果为多行单列。in 等价于 = any
	SELECT DISTINCT department_id		# DISTINCT 去重，提高效率
	FROM departments
	WHERE location_id IN (1400, 1700)
);

# 3. 行子查询
# 查询员工编号最小并且工资最高的员工信息。下面两种方式等价，当操作符相同时可以合并
SELECT *
FROM employees
WHERE employee_id = (
	SELECT MIN(employee_id)
	FROM employees
) AND (
	SELECT MAX(salary)
	FROM employees
);

SELECT *
FROM employees
WHERE (employee_id, salary) = (
	SELECT MIN(employee_id), MAX(salary) 
	FROM employees
);

# 查询平均工资最高的部门的manager的详细信息，一个部门一个领导
SELECT last_name, d.department_id, email, salary
FROM departments AS d
INNER JOIN employees AS e
ON d.manager_id = employee_id
WHERE d.department_id = (
	SELECT department_id 				# 查询平均工资最高的部门id
	FROM employees
	GROUP BY department_id
	ORDER BY AVG(salary) DESC
	LIMIT 0, 1
);
```

- select 后面：仅仅支持标量子查询。不可以返回多行或者多列；
- from 后面：支持表子查询，也就是将查询的结果集作为新表，**要求该表必须起别名**；
- exits 后面：相关子查询，支持表子查询。返回结果为布尔类型，1代表有返回值。

~~~sql
# 1. select 后面
# 查询每个部门的员工个数。当部门没有员工时员工个数为0。使用group by无法实现
SELECT d.*, (										# 从d表中每次取出一行
	SELECT COUNT(*)
	FROM employees AS e
	WHERE e.department_id = d.department_id
) 个数
FROM departments AS d;

# 查询每个专业的男生个数与女生个数，查询结果显示的效果优于两次分组
SELECT majorid,
	(SELECT COUNT(*) FROM student WHERE sex='男' AND majorid=s.majorid) 男生个数,
	(SELECT COUNT(*) FROM student WHERE sex='女' AND majorid=s.majorid) 女生个数
FROM student AS s
GROUP BY majorid;

# 2. from 后面
# 查询每个部门平均工资所对应的工资级别
SELECT mean_salary.* , g.grade_level
FROM (
	SELECT e.department_id, AVG(e.salary) AS mean 	# 先查询每个部门的平均工资
	FROM employees AS e
	GROUP BY e.department_id
) AS mean_salary									# 该表必须取别名
INNER JOIN job_grades AS g 							# 新建连接
ON mean_salary.mean BETWEEN g.lowest_sal AND g.highest_sal;

# 3. exits 后面
# 查询有员工的部门名
SELECT d.department_name
FROM departments AS d
WHERE EXISTS(
	SELECT *					 					# 是什么无所谓
	FROM employees AS e
	WHERE d.department_id = e.department_id
);
~~~

#### 分页查询

当查询结果较多时，需要分页提交sql请求。

语法如下：limit offset, size。**limit语句的书写位置与执行顺序都是在查询语句的最后**。

- offset：偏移，要显示条目的起始索引。起始索引从0开始，可省略；

- size：条目数，不是结束索引。

#### 联合查询

union 联合，将多条查询语句的结果合并为一个结果，实现代码拆分的效果。

语法如下：查询语句1 union 查询语句2

适用场景：当要查询的结果来自多个表，而且相互之间没有直接的连接关系，且查询的信息一致，列数相同。

**union查询结果默认去重，使用 union all 可以包含重复项。**

~~~sql
# 如：查询部门编号>90或邮箱中包含'a'的用户信息
SELECT * FROM employees
WHERE email LIKE '%a%' OR department_id > 90;

SELECT * FROM employees WHERE email LIKE '%a%'			# 等价
UNION
SELECT * FROM employees WHERE department_id > 90;
~~~

### DML

数据操作语言，包括增删改。

#### 插入语句

语法一：insert into 表名(列名)  values(列值)

要求列数与值的个数一致。

语法二：insert into 列名 = 列值，列名 = 列值

区别：

- 方式1支持插入多行， 方式2不支持；
- 方式1支持子查询， 方式2不支持。

~~~sql
INSERT INTO beauty										# 插入多行
VALUES(18, '刘涛', '女', '1999-9-9', '110', NULL, 3)
, (19, '佟丽娅', '女', '1999-9-9', '110', NULL, 4);

INSERT INTO beauty(id, NAME, phone)						# 子查询
SELECT 20, '朱茵', '120';
~~~

#### 修改语句

1. 修改单表的记录

语法：update 表名 set 列名 = 新值，列名 = 新值 where 筛选条件

2. 修改多表的记录（级联更新）

先建立连接再修改值。

sql99语法：

update 表1 别名

连接类型 join 表2 别名 on 连接条件 

set 列名 = 新值，列名 = 新值 where 筛选条件

~~~sql
# 修改没有男朋友的女神的男朋友编号为2
UPDATE beauty AS g
LEFT JOIN boys AS b							# 外连接存在NULL。内连接不存在NULL
ON g.boyfriend_id = b.id
SET g.boyfriend_id = 2
WHERE g.boyfriend_id IS NULL;
~~~

#### 删除语句

删除表的数据，但表的结构还在。

1. 同样分单表删除与多表删除两种。

语法一：delete from 表名 别名  where 筛选条件 【limit 条目数】。整行删除。有返回值（受影响行数）。可以回滚。

语法二：truncate tabel 表名。整表删除。不能接筛选条件。没有返回值。不可以回滚。

2. 假如要删除的表中含有自增长列：

如果使用delete删除后，再插入数据时，自增长列的值从断点开始；

如果使用truncate删除后，再插入数据时，自增长列的值从1开始。

~~~sql
# 多表删除。删除张无忌的女朋友的信息
DELETE g
FROM beauty AS g
INNER JOIN boys AS b
ON g.boyfriend_id = b.id
WHERE b.boyName = '张无忌';
~~~

3. 删除外键

主表删除记录时从表主键的变化。

分为级联删除与级联置空两种，在创建外键时设置。

~~~sql
ALTER TABLE stuinfo DROP FOREIGN KEY fk_stu_major;					# 删除主键

ALTER TABLE stuinfo ADD CONSTRAINT fk_stu_major 					# 添加主键
# FOREIGN KEY(majorid) REFERENCES major(id) ON DELETE CASCADE;		# 级联删除
FOREIGN KEY(majorid) REFERENCES major(id) ON DELETE SET NULL;		# 级联置空

DELETE FROM major WHERE id = 2;
~~~

### DDL

数据定义语言。

包括库与表的管理（创建 create 、修改 alter、删除 drop）。

DML操作的是数据，DDL操作的是库或表的结构。

#### 库的管理

- 创建：create database 【if nott exists】库名；

- 修改：更改库的字符集 character set，默认utf8；

- 删除：drop database 库名 【if exists】。

#### 表的管理

- 创建：create tabel 表名 【if nott exists】（ 列名 列的类型【（长度） 约束】，）；
- 修改：alter tabel 表名 change | modify | add | drop column 列名 类型 【约束】；
- 删除：drop tabel 表名【if exists】；
- 表的复制：分两种，包括复制表的结构与复制结构+数据。可以跨库复制。

```sql
ALTER TABLE book CHANGE COLUMN publishdate pubdate datetime;	# 修改列名
ALTER TABLE book MODIFY COLUMN pubdate TIMESTAMP;				# 修改列的类型或约束
ALTER TABLE book ADD COLUMN annual DOUBLE;						# 增加列
ALTER TABLE book DROP COLUMN annual;							# 删除列
ALTER TABLE author RENAME TO book_author; 						# 修改表名

CREATE TABLE book_copy LIKE book;								# 复制表的结构
CREATE TABLE book_data_copy										# 复制表的结构+数据
	SELECT id, bname 
	FROM book
	WHERE price > 10;
```

#### 常见数据类型

1. 数值类型：

整数：

保存值的范围不同。分有符号与无符号，默认有符号，通过unsigned设置无符号。

如果插入的数据超出了参数的范围，会报 out of range 异常，并且插入临界值。

- tinyint：占用1个字节，相对于java中的 byte；
- smallint：占用2个字节，相对于java中的 short；
- int：占用4个字节，相对于java中的 int。默认无符号，长度11（不代表范围，代表的是结果显示的最小宽度）；
- bigint：占用8个字节，相对于java中的 long。

小数：

参数 (M, D) 的含义分别为：M整数位数+小数位数，D小数位数。

- 浮点型：float 占用4个字节，double 占用8个字节；
- 定点型：dec 或 decimal。decimal 默认 (10, 0)。精度较高。

2. 字符类型：

较短文本：

M为最多字符数。**一个字母或汉字都是一个字符。**

- char(M)：定长字符串，最长255个字符，默认为1。定长会浪费空间，截取插入或空格填满。查询效率高，如手机号；
- varchar(M)：变长(不定长)字符串，最长不超过 65535个字节，英文字符。需要有一个prefix来存储字符串的长度。

较长文本：

- text
- blob：用于保存较大的二进制。如图片。

3. 日期类型：

- date：日期，年月日；

- time：时间，时分秒；

- datetime：年月日 时分秒。占用8个字节；

- timestamp：时间戳，与datetime存储相同的数据。占用4个字节。随时区变化，相对准确。

  timestamp最大表示1970~2038年，而datetime范围是1000~9999年。

  timestamp在插入数、修改数据时，可以自动更新成系统当前时间。

~~~sql
DROP TABLE IF EXISTS tab_date;
CREATE TABLE tab_date(
	t1 datetime,
	t2 TIMESTAMP
);

SET time_zone = '+9:00';
SHOW VARIABLES LIKE 'time_zone';

INSERT INTO tab_date VALUES(NOW(), NOW());
SELECT * FROM tab_date;
~~~

#### 常见约束

用于限制表中的数据。

建表先建主表，主表先插入数据，删除先删从表。

1. 约束类型

- 非空约束，not null；
- 默认约束，default；

- 主键约束，primary key，具有唯一性，且非空；
- 唯一约束，unique，具有唯一性，可以为空；
- 检查约束，check，mysql中不支持，兼容性；
- 外键约束，foreign key，用于限制两个表的关系，**在从表中添加外键，用于引用主表中某字段的值**，默认NULL。
- 建立索引，key / index。

**其中主键、外键、唯一键自动生成索引。**

2. 列级约束与表级约束

- 列级约束是在建表时在字段名和类型后面追加约束类型，不支持外键约束；

- 表级约束位于建表语句的最后一行，不支持非空约束，通常用于外键约束，可用于组合约束，使用constraint关键字，后面加名字。

~~~sql
CREATE TABLE IF NOT EXISTS major(
	id INT PRIMARY KEY,
	majorname VARCHAR(20)
);

CREATE TABLE IF NOT EXISTS stuinfo(
	id INT,
	stuname VARCHAR(20) NOT NULL,
	gender CHAR(1) CHECK(gender IN ('男', '女')), 		   # check无效
	seat INT UNIQUE,
	age INT DEFAULT 18,
	# majorid INT REFERENCES major(id)						# 外键无效
	majorid INT,

    PRIMARY KEY(id, stuname),								# 组合主键，一个索引
	CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)	# 表级约束
);

SHOW INDEX FROM stuinfo;									# 查看全部索引
~~~

![1573438153396](C:\Users\HMZ\AppData\Roaming\Typora\typora-user-images\1573438153396.png)

3. 主键与唯一

- 主键：保证唯一性，不允许为空，一个表中至多有一个，允许组合；

- 唯一：保证唯一性，允许为空（多个NULL值不报错），一个表中可以有多个，允许组合。

4. 外键

- 在从表中设置外键关系，包含外键的引用，主表中的对应字段称为关联列；

- 主表的关联列必须是一个key（一般为主键、唯一）。

5. 约束的添加与删除

add column是添加列，添加约束要使用 modify column。

不同约束的添加方法类似，而删除方法不同。

- drop foreign key 删除外键。

- drop primary key 删除主键。

- modify column 删除非空与默认。

- drop index 删除唯一约束。

~~~sql
ALTER TABLE stuinfo MODIFY COLUMN seat INT UNIQUE;		# 添加唯一约束
ALTER TABLE stuinfo MODIFY COLUMN seat INT NOT NULL;	# 添加非空约束（列级约束）

ALTER TABLE stuinfo MODIFY COLUMN seat INT;				# 删除非空约束
ALTER TABLE stuinfo DROP INDEX seat;					# 删除唯一约束

ALTER TABLE stuinfo ADD UNIQUE(seat);					# 添加唯一约束（表级约束，带小括号）
SHOW KEYS FROM stuinfo;
~~~

6. 标识列（自增长列）

- 不用手动插入值，系统提供默认的序列值；

- 标识列不是必须与主键搭配，但要求是key；
- 一个表至多一个标识列。类型要求是数值型。

~~~sql
# 修改表时添加标识列
ALTER TABLE tab_identity MODIFY COLUMN id INT PRIMARY KEY auto_increment;
SHOW VARIABLES LIKE '%auto_increment%';
SET auto_increment_increment = 5;					# 修改标识列步长

INSERT INTO tab_identity VALUES(NULL, 'tom');		# NULL的意思是缺省，主键非空
INSERT INTO tab_identity VALUES(10, 'tom');			# 可以直接插入id，间接修改起始值
INSERT INTO tab_identity VALUES(NULL, NULL);		# 真正的NULL

INSERT INTO tab_identity VALUES(5, 'tom');			# 存储的顺序与插入的顺序不同
SET auto_increment_increment = 1;
INSERT INTO tab_identity VALUES(NULL, 'tom');		# 自增长列的值从最大断点处开始；

ALTER TABLE tab_identity MODIFY COLUMN id INT;		# 修改表时删除标识列
~~~

### TCL

transcation control language 事务控制语言。

#### 事务

事务中涉及到的语句包括 DML + DQL，即增删改查，不包括 DDL。

- 执行单元：一个或一组sql语句组成的一个**执行单元**。每个sql语句相互依赖。这个控制单元要么执行，要么全部不执行。

如转账。如果单元中某条sql语句执行失败或产生错误，整个单元将会回滚，撤销已执行的操作。

- 存储引擎：在mysql中的数据用各种不同的技术存储在文件或内存中。在mysql中用的最多的存储引擎有innodb、myisam、memory等。其中innodb支持事务，myisam、memory等不支持事务。

5.5版本以后默认的存储引擎为innodb，以前默认为myisam。

#### 事务属性

- 原子性 Atomicity 

事务的原子性指的是，事务中包含的程序作为数据库的逻辑工作单位，它所做的对数据修改操作要么全部执行，要么完全不执行。这种特性称为原子性。 

- 一致性 Consistency 

事务的一致性指的是在一个事务执行之前和执行之后数据库都必须处于一致性状态。这种特性称为事务的一致性。假如数据库的状态满足所有的完整性约束，就说该数据库是一致的。如转账时的账目总和。

- 隔离性 Isolation 

隔离性指并发的事务是相互隔离的。即一个事务内部的操作及正在操作的数据必须封锁起来，不被其它企图进行修改的事务看到。 

- 持久性 Durability 

持久性意味着当系统或介质发生故障时，确保已提交事务的更新不能丢失。即一旦一个事务提交，DBMS保证它对数据库中数据的改变应该是永久性的，耐得住任何数据库系统故障。持久性通过数据库备份和恢复来保证。

严格来说**数据库事务属性（ACID）**都是由数据库管理系统来进行保证的，在整个应用程序运行过程中应用无需去考虑数据库的ACID实现。

#### 事务创建

- 开启事务。默认自动提交，将自动提交关闭，显式事务；
- 编写一组sql语句作为执行单元，数据更新；
- 结束事务，将内存数据提交至磁盘文件，数据提交。

~~~sql
# 开启事务，只对当前事务生效
SET autocommit = 0;			
START TRANSACTION;							# 可以不写

# 编写一组sql语句作为执行单元
UPDATE account SET balance = 500 WHERE username = '张无忌';
UPDATE account SET balance = 1500 WHERE username = '赵敏';

# 结束事务，将内存数据提交至磁盘文件，更新后提交
# COMMIT;									# 提交事务
ROLLBACK;									# 回滚事务
~~~

#### 事务隔离级别

并发事务。类似于线程安全问题。

数据库事务的隔离级别有4种，由低到高分别为Read uncommitted 、Read committed 、Repeatable read 、Serializable 。而且，**在事务的并发操作中可能会出现脏读（update），不可重复读（update），幻读（insert）**。下面通过事例一一阐述它们的概念与联系。

- Read uncommitted

读未提交，顾名思义，就是一个事务可以读取另一个未提交事务的数据。

事例：老板要给程序员发工资，程序员的工资是3.6万/月。但是发工资时老板不小心按错了数字，按成3.9万/月，该钱已经打到程序员的户口，但是事务还没有提交，就在这时，程序员去查看自己这个月的工资，发现比往常多了3千元，以为涨工资了非常高兴。但是老板及时发现了不对，马上回滚差点就提交了的事务，将数字改成3.6万再提交。

分析：实际程序员这个月的工资还是3.6万，但是程序员看到的是3.9万。他看到的是老板还没提交事务时的数据。这就是脏读。

那怎么解决脏读呢？Read committed！读提交，能解决脏读问题。

- Read committed

读提交，顾名思义，就是一个事务要等另一个事务提交后才能读取数据。

事例：程序员拿着信用卡去享受生活（卡里当然是只有3.6万），当他埋单时（程序员事务开启），收费系统事先检测到他的卡里有3.6万，就在这个时候！！程序员的妻子要把钱全部转出充当家用，并提交。当收费系统准备扣款时，再检测卡里的金额，发现已经没钱了（第二次检测金额当然要等待妻子转出金额事务提交完）。程序员就会很郁闷，明明卡里是有钱的…

分析：这就是读提交，若有事务对数据进行更新（UPDATE）操作时，读操作事务要等待这个更新操作事务提交后才能读取数据，可以解决脏读问题。但在这个事例中，**出现了一个事务范围内两个相同的查询却返回了不同数据，这就是不可重复读。**

那怎么解决可能的不可重复读问题？Repeatable read ！

- Repeatable read

重复读，就是在开始读取数据（事务开启）时，不再允许修改操作

事例：程序员拿着信用卡去享受生活（卡里当然是只有3.6万），当他埋单时（事务开启，不允许其他事务的UPDATE修改操作），收费系统事先检测到他的卡里有3.6万。这个时候他的妻子不能转出金额了。接下来收费系统就可以扣款了。

分析：重复读可以解决不可重复读问题。写到这里，应该明白的一点就是，**不可重复读对应的是修改，即UPDATE操作**。但是可能还会有幻读问题。因为**幻读问题对应的是插入INSERT操作，而不是UPDATE操作**。

什么时候会出现幻读？

事例：程序员某一天去消费，花了2千元，然后他的妻子去查看他今天的消费记录（全表扫描FTS，妻子事务开启），看到确实是花了2千元，就在这个时候，程序员花了1万买了一部电脑，即新增INSERT了一条消费记录，并提交。当妻子打印程序员的消费记录清单时（妻子事务提交），发现花了1.2万元，似乎出现了幻觉，这就是幻读。

那怎么解决幻读问题？Serializable！

- Serializable 串行化

Serializable 是最高的事务隔离级别，在该级别下，事务串行化顺序执行，禁止其他事务对该表执行插入、更新和删除操作，可以避免脏读、不可重复读与幻读等所有并发问题。但是这种事务隔离级别效率低下，比较耗数据库性能，一般不使用。

值得一提的是：**大多数数据库默认的事务隔离级别是Read committed，比如Sql Server , Oracle。Mysql的默认隔离级别是Repeatable read。**

~~~sql
# 设置隔离级别，当前会话（连接）session
mysql> set session transaction isolation level serializable;	
Query OK, 0 rows affected (0.00 sec)				
# 查看隔离级别。老版本mysql用的是tx_isolation。Mysql8更名为 transaction_isolation
mysql> select @@transaction_isolation;
+-------------------------+
| @@transaction_isolation |
+-------------------------+
| SERIALIZABLE            |
+-------------------------+
1 row in set (0.00 sec)

mysql> set autocommit=0;				# 开启事务
Query OK, 0 rows affected (0.00 sec)
mysql> select * from account;			# 阻塞操作，等待锁的释放。
ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
~~~

#### 事务回滚

delete与truncate在事务中的区别：

delete可以回滚；

truncate不可以回滚。

savepoint搭配rollback使用，设置保存的断点。

~~~sql
DELETE FROM account WHERE id=1;
SAVEPOINT a;
DELETE FROM account WHERE id=2;
ROLLBACK TO a;
~~~

#### 视图

类似于查询结果集，虚拟的表格。封装查询语句。语法与表格类似。

从mysql5.0.1版本出现，通过表动态生成的数据。只保存sql逻辑，不保存查询结果。

当多个地方使用同样的查询结果时可以使用，简化代码，便于重用，隐藏基表数据。

show tables时视图与表都会显示。

1. 视图的增删改查

~~~sql
CREATE OR REPLACE VIEW myv2			# 修改的第二种方法 ALTER VIEW AS 视图名
AS
SELECT job_id, AVG(salary)
FROM employees
GROUP BY job_id;

DROP VIEW myv2;						# 删除

DESC myv1;							# 显示视图结构
SHOW CREATE VIEW myv1;
~~~

2. 视图的更新

视图的更新会对原始表也生成更新。因此往往为视图设置权限，不可更改。

简单的增删改查语句会更新原表，大多数语句不允许更新原表 not updatable。

~~~sql
CREATE OR REPLACE VIEW myv3
AS
SELECT last_name, email
FROM employees;

# 增删改查，简单语句。
INSERT INTO myv3 VALUES('ting', '123@qq.com');
UPDATE myv3 SET last_name = 'kai' WHERE last_name = 'ting';
DELETE FROM myv3 WHERE last_name IN ('ting', 'kai');
SELECT * FROM myv3;
~~~

### 变量与语句

#### 变量类型

1. 系统变量

变量由系统提供，不是用户定义，属于服务器层面。分两种，作用域不同。

- 全局变量：global，作用域为整个服务器，跨连接（会话）有效，但不能跨重启。需要super权限；

- 会话变量：session，作用域为当前连接（会话）。

查看全部或部分系统变量时，使用show语句，默认显示会话变量。

查看指定的系统变量时，使用select语句，变量名前加@@。

```sql
SHOW GLOBAL VARIABLES;
SELECT @@session.autocommit;
SET @@session.autocommit = 0; # 等价于 SET 【session】 autocommit = 0;
```

2. 自定义变量

步骤：声明、赋值（操作符 = 或 :=）、使用。

- 用户变量：当前连接（会话）有效，变量名前加@。变量弱类型，类型不声明，可以修改；
- 局部变量：仅仅在定义它的begin end中有效，且为第一句。变量名前一般不用加@。必须声明类型。

局部变量只能用于函数、存储过程内部、游标、触发器的定义内部。

~~~sql
# 用户变量
SET @name = 'tom';								# 声明并初始化	
SET @name = 100;

SELECT COUNT(*) INTO @count FROM stuinfo;		# 赋值
SELECT @count;									# 查看

# 局部变量
BEGIN
DECLARE a INT DEFAULT 1;
DECLARE b INT DEFAULT 2;
DECLARE sum INT;
SET sum = a + b;
SELECT sum;
END
~~~

#### 存储过程

类似于方法。方法的作用如提高代码的重用性与简化操作。

存储过程是一组预先**编译**好的SQL语句的集合，可以理解成批处理语句。可以减少编译次数以及与数据库的连接次数，提高效率。常用于一次性插入大量数据。

存储过程创建语法：create procedure 存储过程名（参数列表）begin 存储过程体 end

- 参数列表包含三部分，包括参数模式（in输入参数、out返回参数、inout 传入并返回）、参数名、参数类型；
- 如果存储过程体仅仅只有一句话，begin与end可以省略。存储过程体中的每条SQL语句的结尾都需要加分号，存储过程的结尾可以使用delimiter重新设置，设置以后的每条语句结束标记都不再是分号。begin与end内部结束标记依然是分号；
- 存储过程的调用：call 存储过程名（实参列表）。

~~~sql
delimiter $ 							# 设置存储过程结尾。sqlyog不支持，因此使用CMD运行代码

# 1. 创建存储过程实现（两个in模式），判断用户是否登陆成功。期间使用到变量与if函数
CREATE PROCEDURE myp3(IN username VARCHAR(20), IN password VARCHAR(20))
BEGIN
	DECLARE result VARCHAR(20) DEFAULT '';					# 声明并初始化局部变量

	SELECT COUNT(*) INTO result								# 赋值
	FROM admin
	WHERE admin.username = username AND admin.`password` = password;
	SELECT IF(result>0, '登录成功', '登录失败') AS 登录状态;	# 赋值
END $

CALL myp3('john', 8888)$									# 调用存储过程
DROP PROCEDURE myp3$										# 删除存储过程

# 2. 一个in，一个out模式
CREATE PROCEDURE myp5(IN beautyName VARCHAR(20), OUT boyName VARCHAR(20))
BEGIN
	SELECT b.boyName INTO boyName
	FROM boys AS b
	INNER JOIN beauty AS g ON b.id = g.boyfriend_id
	WHERE g.name = beautyName;
END $

SET @bName = '' $ # 用户变量
CALL myp5('贾静雯', @bName) $
SELECT @bName $

# 3. inout 模式
CREATE PROCEDURE myp7(INOUT a INT, INOUT b INT)
BEGIN
	SET a = a * 2; # 局部变量
	SET b = b * 2;
END $

SET @a = 10 $ # 用户变量
SET @b = 15 $

CALL myp7(@a, @b) $
CALL myp7(@a, @b) $
~~~

#### 函数

**函数与存储过程的区别在于返回值**，存储过程可以有0个、多个返回值，函数有且仅有一个返回值。

因此存储过程适用于做批量处理、批量更新。函数适用于处理数据后返回一个结果。

函数创建语法：create function 函数名（参数列表） returns 返回类型 begin 函数体 end

- 参数列表包含两部分，包括参数名、参数类型；
- 使用delimiter设置结束函数体标记；
- 函数的调用：select 函数名（参数列表），执行并显示返回值。

~~~sql
# 报错：This function has none of DETERMINISTIC, NO SQL, or READS SQL DATA in its de
# 在MySQL中创建函数时出现这种错误的解决方法：set global log_bin_trust_function_creators=TRUE;
# 如根据部门名，返回该部门的平均工资
CREATE FUNCTION myf1(ename VARCHAR(20)) RETURNS DOUBLE
BEGIN
	DECLARE n DOUBLE DEFAULT 0; # 声明变量，用户变量或局部变量都可以
	# SET @n = 0;
	SELECT AVG(e.salary) INTO n   # 赋值
	FROM employees AS e
	INNER JOIN departments AS d
	ON e.department_id = d.department_id
	WHERE d.department_name = ename;
	RETURN n;
END $
~~~

#### 流程控制结构

1. 顺序结构
2. 分支结构

- if 函数：实现简单的双分支。语法：select if(表达式1，表达式2，表达式3)；
- case 结构：可以作为表达式也可以作为独立的语句（begin end中）。类似于JAVA中的switch语句，一般用于实现等值判断。或者类似于多重if语句，一般用于区间判断。语法：case 【字段或表达式】 when 值或条件 then else end；
- if 结构：实现多重分支。语法：if 条件1 then 语句1；elseif 条件2 语句2；else 语句n；end if；。应用在begin end 中。

~~~sql
# 1. 分别case与if使用实现功能：根据传入的成绩，来显示等级。其中case中不设置返回值，if中设置返回值
# 1.1 case 作为独立的语句（位于begin end中）
CREATE PROCEDURE myp5(IN score INT)
BEGIN
	CASE
	WHEN score BETWEEN 90 AND 100 THEN SELECT 'A';
	WHEN score BETWEEN 80 AND 90 THEN SELECT 'B';
	WHEN score BETWEEN 60 AND 80 THEN SELECT 'C';
	ELSE SELECT 'D';
	END CASE;
END $

# 1.2 if 结构（位于begin end中）
CREATE FUNCTION myf2(score INT) RETURNS CHAR
BEGIN
	if score>=90 AND score<=100 THEN RETURN 'A';
	ELSEIF score>=80 THEN  RETURN 'B';
	ELSEIF score>=60 THEN  RETURN 'C';
	ELSE RETURN 'D';
	END IF;
END $
~~~

3. 循环结构

用在 begin end 中。可以给循环语句起名（标签），便于使用循环控制语句。

- while：语法：【标签：】while 循环条件 do 循环体；end while【标签】；
- loop：语法：【标签：】loop 循环体；end loop【标签】；可以用来模拟简单的死循环，跳出需搭配循环控制语句；
- repeat：类似于do-while。语法：【标签：】repeat 循环体 until 结束循环的条件 end repeat 【标签】；

循环控制语句：

- iterate：类似于continue；
- leave：类似于break。

~~~sql
# 1. 实现批量插入，只插入偶数次。没有返回值，因此创建存储过程而非函数
CREATE PROCEDURE pro_while(IN number INT)
BEGIN
	DECLARE i INT DEFAULT 0;
	a: WHILE i<number DO
		SET i = i+1;
		IF MOD(i, 2) != 0 THEN ITERATE a;			# 类似于continue
		END IF;
		INSERT INTO admin(username, PASSWORD) VALUES(CONCAT('dancy', i), '666');
	END WHILE a;
END	$

# 2. 实现批量插入，生成随机字符串，多用于测试
CREATE PROCEDURE insert_str(IN strcount INT)
BEGIN
	DECLARE i INT DEFAULT 0;						# 插入次数
	DECLARE startindex INT DEFAULT 1; 				# 起始索引
	DECLARE len INT DEFAULT 1; 						# 截取的字符串长度
	DECLARE str VARCHAR(26) DEFAULT 'abcdefghigklmnopqrstuvwxyz';
	WHILE i<strcount DO
		SET i = i+1;	
		SET startindex = FLOOR(RAND()*26+1); 		# 产生随机数，代表起始索引，取值范围为1-26
		SET len = FLOOR(RAND()*(26-startindex+1)+1); # 产生随机数，代表字符串长度
		INSERT INTO stringcontent(content) VALUES(SUBSTR(str, startindex, len));
	END WHILE;
END $
~~~

### 基础架构

#### 安装MySQL

linux常见的三种centos、redhat、ubuntu（sudo apt-get install）安装mysql的方法均有所不同。

CentOS 6.5安装MySQL 5.7.10（rpm方式）部分过程：

https://blog.csdn.net/zhu19774279/article/details/50418711

- 查看CentOS自带MySQL 5.1组件 rpm -qa | grep -i mysql 并卸载；
- rpm -ivh 显示进度条；
- 安装MySQL（下面的安装顺序不能错，否则会安装失败）；

```mysql
rpm -ivh mysql-community-common-5.7.10-1.el6.x86_64.rpm
rpm -ivh mysql-community-libs-5.7.10-1.el6.x86_64.rpm
rpm -ivh mysql-community-client-5.7.10-1.el6.x86_64.rpm
rpm -ivh mysql-community-server-5.7.10-1.el6.x86_64.rpm
```

- 启动MySQ service mysqld start；
- 获得MySQL初始密码 grep 'temporary password' /var/log/mysqld.log；
- 初始密码登录MySQL，并修改初始密码 ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码root'。

~~~sql
[kz@localhost home]$ mysqladmin --version 				# 查看版本
mysqladmin  Ver 8.42 Distrib 5.7.10, for Linux on x86_64
[kz@localhost home]$ cat /etc/passwd|grep mysql			# 查看安装时创建的mysql用户和mysql组
mysql:x:496:501::/home/mysql:/bin/bash
[kz@localhost home]$ cat /etc/group|grep mysql		
mysql:x:501:
[kz@localhost home]$ ps -ef|grep mysql					# 查看安装位置，启动失败
kz         2916   2662  0 12:53 pts/0    00:00:00 grep mysql
~~~

启动失败，跳过几集。

#### 逻辑架构

如图所示，MySQL逻辑系统架构分为四层：

- 应用层（连接层）：客户端和连接服务，包含本地socket通信和大多数基于C/S工具实现的类似于TCP/IP的通信。在该层上引入了线程池的概念，为通过认证安全接入的客户端提供线程，减少了创建线程和释放线程所花费的时间；
- MySQL服务层（业务逻辑处理层）：MySQL Server的核心层，提供了MySQL Server数据库系统的所有逻辑功能；
- 存储引擎层：可拔插，灵活。可以满足不同的业务需求和实际需要。存储引擎真正负责MySQL中数据的存储与提取，服务器通过API与存储引擎进行通信；
- 存储层：将数据存储在运行于裸设备的文件系统之上，并完成与存储引擎的交换。

MySQL的架构可以在不同场景中应用并发挥良好作用，主要体现在存储引擎的架构上。**插件式的存储引擎将查询处理和其他的系统任务以及数据的存储提取相分离**。

![img](https://pic2.zhimg.com/80/v2-4ce9c07e160c6c3e48387bc98d5295e1_hd.jpg)

#### 搜索引擎

MyISAM和InnoDB的区别：

**InnoDB支持事务、外键、行锁。**具体见下表。

![img](https://img-blog.csdn.net/20180309145145549?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXNfY29kZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 索引优化

#### 性能下降SQL慢

执行时间长、等待时间长。

- 查询语法烂

- 索引失效：单值索引、复合索引。索引对字段进行排序；
- 关联查询太多join
- 服务器调优及各个参数设置：如缓冲、线程数等。

#### SQL执行加载顺序

- 书写顺序

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170502212731593?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VzZXl1a3Vp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 解析器执行顺序

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170502212921830?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VzZXl1a3Vp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 索引

1. 概念

索引是帮助MySQL高效获取数据的数据结构，即索引是一种数据结构。

索引的目的是提高查询速度，可以类比字典。**索引就是排好序的快速查找数据结构。**索引会影响到查找与排序。索引本身也很大，不可能存储在内存中，因此索引往往以索引文件的形式存储在磁盘中。

在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指针）数据，这样就可以在这些数据结构上实现高级查找算法，这种数据结构就是索引。如使用二叉查找树，每个节点分别包含索引键值和一个指向对应数据记录物理地址的指针，可以运用二叉查找在一定的复杂度内获取到相应数据。

如图所示是一种可能的索引方式。左边是数据表，最左边的是数据记录的物理地址（注意逻辑上相邻的记录在磁盘上也并不是一定物理相邻的）。为了加快Col2的查找，可以维护一个右边所示的二叉查找树。虽然这是一个货真价实的索引，但是实际的数据库系统几乎没有使用二叉查找树或其进化品种红黑树（red-black tree）实现的。

![img](http://blog.codinglabs.org/uploads/pictures/theory-of-mysql-index/1.png)

2. 优点

- 提高数据检索的效率，降低数据库的IO成本；
- 降低数据排序的成本，降低了CPU的消耗。

3. 缺点

- 实际上索引也是一张表，该表保存了主键与索引字段，并指向实体表的记录，所以索引列也是要占空间的；
- 虽然索引大大提高了查询速度，同时确会降低更新表的速度，如对表进行INSERT、UPDATE、DELETE。因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件每次更新添加了索引列的字段。都会调整因为更新所带来的键值变化后的索引信息；
- 建立优秀索引的时间成本。

4. 索引分类

- 单值索引：即一个索引只包含单个列，一个表可以有多个单列索引；
- 唯一索引：索引列的值必须唯一 unique，但允许有空值；
- 复合索引：即一个索引包含多个列。

5. 语法

![img](https://images2015.cnblogs.com/blog/1001655/201706/1001655-20170622163219273-351707462.png)

![img](https://images2015.cnblogs.com/blog/1001655/201706/1001655-20170622163449648-1145782677.png)

#### 索引结构原理

- BTree索引：尽量加宽树的结构，而不是加深。如图所示。

**真实的数据存在于叶子结点，非叶子结点不存储真实的数据，只存储指引搜索方向的数据项。**如17、35并不真实存在于数据表中，当磁盘块1由磁盘加载到内存中时，发生一次磁盘IO，耗时操作。

![ä¸ç¯æç« å½»åºææMySQLçç´¢å¼åç](https://www.seoxiehui.cn/data/attachment/portal/201908/22/211907zfi05idmd68zfaaz.jpg)

B树的搜索，从根结点开始，对结点内的关键字（有序）序列进行二分查找，如果命中则结束，否则进入查询关键字所属范围的儿子结点；重复，直到所对应的是叶子结点。

查找文件29的过程：

1. 根据根结点指针找到文件目录的根磁盘块1，将其中的信息导入内存。（磁盘IO操作1次）
2. 此时内存中有两个文件名17，35和三个存储其他磁盘页面地址的数据。根据算法我们发现17<29<35，因此我们找到指针p2。
3. 根据p2指针，我们定位到磁盘块3，并将其中的信息导入内存。（磁盘IO操作2次）
4. 此时内存中有两个文件名26，30和三个存储其他磁盘页面地址的数据。根据算法我们发现26<29<30，因此我们找到指针p2。
5. 根据p2指针，我们定位到磁盘块8，并将其中的信息导入内存。（磁盘IO操作3次）
6. 此时内存中有两个文件名28，29。根据算法我们查找到文件29，并定位了该文件内存的磁盘地址。

- Hash索引
- full-text全文索引
- R-Tree索引

#### 需要与不需要索引

1. 哪些情况需要创建索引

- 主键自动建立唯一索引；
- 频繁作为查询条件的字段应该创建索引；
- 查询中与其他表关联的字段，外键关系建立索引；
- 单键/组合索引的选择问题，在高并发倾向创建组合索引；
- 查询中排序的字段，排序字段若通过索引去访问将大大提高排序速度。组合索引的字段与排序字段顺序相同；
- 查询中统计或分组字段，分组的前提是排序。

2. 哪些情况下不需要创建索引

- 表记录太少；
- 频繁更新的字段不适合创建索引，因为每次更新不单单是更新了记录还会更新索引，加重了IO负担；
- where条件里用不到的字段不创建索引；
- 数据重复且分布平均的表字段（若某个数据列包含许多重复的内容，为它建立索引就没有太大的实际效果，即过滤性不好的字段）。

#### EXPLAIN性能分析

使用EXPLAIN关键字可以模拟优化器执行SQL查询语句，**从而知道MYSQL是如何处理SQL语句的**，分析查询语句或是表结构的性能瓶颈。

语法：EXPLAIN +SQL 语句，返回执行计划包含的信息，包含的字段见下图：

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20180520151002824?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doeTE1NzMyNjI1OTk4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

通过EXPLAIN，我们可以分析出以下结果：

- 表的读取顺序
- 数据读取操作的操作类型
- 哪些索引可以使用
- 哪些索引被实际使用
- 表之间的引用
- 每张表有多少行被优化器查询

#### EXPLAIN字段解释

1. id: SELECT 查询的序列号，包含一组数字，表示查询中执行select子句或操作表的顺序。**id值越大优先级越高，id相同时顺序执行。**

id的结果共有3中情况，具体如下：

- id相同，执行顺序由上至下；

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20180520162837993?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doeTE1NzMyNjI1OTk4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

- id不同，如果是子查询，id的序号会递增，id值越大优先级越高，越先被执行；

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20180520163258457?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doeTE1NzMyNjI1OTk4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

- id相同不同，同时存在。

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20180520165331951?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doeTE1NzMyNjI1OTk4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

2. select_type: SELECT 查询的类型。

分别用来表示查询的类型，主要是用于区别普通查询、联合查询、子查询等的复杂查询。

- SIMPLE 简单的select查询，查询中不包含子查询或者UNION；
- PRIMARY 查询中若包含任何复杂的子部分，最外层查询则被标记为PRIMARY；
- SUBQUERY 在SELECT或WHERE列表中包含了子查询；
- DERIVED 在FROM列表中包含的子查询被标记为DERIVED（衍生），MySQL会递归执行这些子查询，把结果放在**临时表**中；
- UNION 若第二个SELECT出现在UNION之后，则被标记为UNION：若UNION包含在FROM子句的子查询中，外层SELECT将被标记为DERIVED；
- UNION RESULT 从UNION表获取结果的SELECT。

3. table: 查询的是哪个表

4. type: join 类型



5. possible_keys: 此次查询中可能选用的索引



6. key: 此次查询中确切使用到的索引.



7. ref: 哪个字段或常数与 key 一起被使用



8. rows: 显示此查询一共扫描了多少行. 这个是一个估计值.



9. filtered: 表示此查询条件所过滤的数据的百分比



10. extra: 额外的信息







