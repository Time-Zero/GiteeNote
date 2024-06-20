# 3.1 SQL概述
## 3.1.1 SQL的产生和发展
* ANSI和ISO统一SQL标准
* 是关系数据库语言，是一个通用的、功能极强的关系数据库语言

## 3.1.2 SQL语言的特点
* 综合统一：SQL拥有数据定义语言（DDL），数据操控语言（DML），数据控制语言（DCL）于一体
* 可以独立完成数据库生命周期中的全部活动
* 非过程化语言，面向集合的操作方式
* 提供交互式和嵌入式两种使用方法


**主要还是SQL由DDL、DML和DCL语言组成重要**

# 3.2 数据定义
## 3.2.1 SQL 可以定义的对象
* 模式（架构）的定义 =》其实也就是数据库的定义
* 基本表的定义
* 索引的定义
* 视图的定义
* 其他（用户、触发器、过程和函数等）

## 3.2.2 架构（Schema）的定义与删除
### 定义
* 数据库架构是一个独立于数据库用户的非重复命名空间，可以将结构视为对象的容器。

**实际上架构约等于数据库**

```SQL
create database 数据库名

create login 用户名 with password='密码' default_database=数据库名
```


```SQL
create schema 架构名

create login 用户名 with password='密码' default_schema=架构名
```

### 删除
```SQL
drop schema 架构名 [cascade | restrict]
```
* cascade: 级联
	* 删除架构的同时把该架构中的所有的数据库对象全部删除
* restrict: 限制
	* 如果架构中包含表、视图等，就拒绝删除语句的执行（也就是只删除空数据库）

## 3.2.3 基本表的创建、删除和修改
### 创建
```SQL
create table 表名(
	列名 数据类型 [列级约束条件],
	列名 数据类型 [列级约束条件],
	列名 数据类型 [列级约束条件],
	[表级约束条件]
);
```

#### 例子
一个简单的教学管理实体联系图
![[Pasted image 20240620104819.png]]

```SQL
创建学生表
create table student(
	sno char(10) primary key,
	sname varchar(20) unique,
	ssex char(2) check(ssex in ('男','女')),
	sage smallint,
	sdept char(20)
);

创建课程表
create table course(
	cno char(4) primary key,
	cname varchar(40) not null,
	cpno char(4) references course(cno)
);

创建学生选课表
create table sc(
	sno char(10),
	cno char(4),
	grade smallint, 
	primary key(sno, cno),
	foreign key(sno) references student(sno),
	foreign key(cno) references course(cno)
);
```

## 修改
![[Pasted image 20240620121720.png]]

**例:**
```SQL
--向表中添加s_entrance 属性，数据类型为date
alter table student add s_enterance date;

--将表中sage属性修改为int
alter table student alter column sage int;

--增加course表中cname属性的唯一性约束
alter table course add constraint uncame unique(cname);

--删除约束
alter table course drop constraint uncname;

--删除行
alter table student drop column s_entrance;
```

## 删除
```SQL 
drop table 表名 [restrict | cascade]
```
* restrict：删除表是有限制的
	* 想要删除的表不能被其他的表的约束所使用
	* 如果存在依赖该表的对象（比如视图），则此表不能被删除
* cascade：删除该表没有限制
	* 在删除表的同时，相关的依赖对象一起删除


## 3.2.4 索引的建立和删除
* 索引建立的目的：
	* 加快查询速度
* 索引的种类：
	* 顺序索引
	* B+树索引
	* 哈希索引
	* 位图索引等
* 谁可以建立索引
	* DBA或者表的建立者
	* DBMS一般会自动建立以下类型的索引
		* primary key
		* unique
* 谁维护索引
	* DBMS
* 谁使用索引
	* DBMS会自动选择使用哪些索引

### 索引的建立
![[Pasted image 20240620162044.png]]

* unique：唯一性索引，是非聚集索引，一个表可以建立多个唯一性索引，并且唯一性索引可以不按照数据的顺序来排序，也可以按照数据的顺序来排序
	* unique索引实际建立的是指向数据的指针
* clustered：物理索引，是聚集索引，一个表只能够建立一个聚集索引，并且建立的聚集索引一定有顺序的
	* clustered索引才是真正的索引，他把数据和索引放在同一位置，索引直接指向数据，所以只能建立一个

```sql
create clustered index stname on student(sname);

create unique index stusno on student(sno);

create unique index coucno on course(cno);

create unique index scno on sc(sno asc, cno desc);
```
注意，注意最后一种索引的建立方式，索引是建立在多个属性上的，而且制定了数据的顺序

### 删除索引
![[Pasted image 20240620162747.png]]
```SQL
drop index scno on sc;
```

# 3.3 数据查询
![[Pasted image 20240620163220.png]]
这个图实在是复杂，还是得看具体情况

## 查询条件
![[Pasted image 20240620163255.png]]

## 3.3.1 单表查询
```SQL
--显示学生表中所有学生的学号和姓名并且在查询结果中使用‘学号’和‘姓名’代替列名
select sno 学号,sname 姓名 from student;
```

```SQL
--从学生选课表中查询所有学生的学号，并且去重
select distinct sno from sc;
```
`distinct`这个参数就是去除重复值

```SQL
--从学生表中查询所有学生的学号、姓名、并且使用时间减去年纪表示出生时间
select sno,sname, 2020-sage from student;
```
上面这个例子也就是可以直接在`select`语句中进行简单的数值计算

```SQL
select sno,sname,'合肥工业大学' from student;
```
显示结果如下
![[Pasted image 20240620163919.png]]
这也就是笛卡尔积了，查询结果 x '合肥工业大学' 

### 条件查询
条件查询也就是使用`where`子句对进行条件查询
```SQL
--查询student表中学号为'2021218192'的学生的所有信息
select * from student where sno = '2021218192';
```

```sql
--查询student表中03班的并且性别为男的学生的所有信息
select * from student where spno='03' and ssex='男';
```

```sql
--查询学生表中年纪在20和22之间的学生的所有信息
select * from student where sage between 20 and 22;
```

```sql
--查询学生表中所有年纪是20或者22的学生的所有信息
select * from student where sage in (20,22);
```
要注意`between...and...`结构和`in`的区别


### 字符串匹配
实际也就是使用`like`
![[Pasted image 20240620164449.png]]
```sql
--查询学生表中名字第一个字为`宋`，并且名字是两个字的人
select * from student where sname like '宋_';
```
这里`_`符号是占位符，代表一个字符，如果想查询`宋xx`，就要使用`宋__`了
![[Pasted image 20240620164755.png]]

```sql
select * from student where sname like '%胜%';
```
这里`%`是指可能存在这个字符，也就是`%`所在的位置要么是一个字符，要么是空的
![[Pasted image 20240620164804.png]]

### 涉及空值的查询
![[Pasted image 20240620164839.png]]
```sql
select * from sc where cno='c104' and grage is not null;
```

### 对结果进行排序的查询
![[Pasted image 20240620165015.png]]
**注意默认就是升序的，并且空值在数据库中代表无穷小**
```sql
--结果排序，专业号升序、年龄降序
select * from student order by spno, sage desc;
```
![[Pasted image 20240620165149.png]]

### 基于统计的查询(聚集函数)
![[Pasted image 20240620165217.png]]

```sql
--查询学生总人数 
select count(*) from student;
```

```sql
--这也是查询学生总人数
select count(spno) from student;
```

```sql
--这是查询专业数了
select count(distinct spno) from student;
```

```sql
select sum(grade) from sc where cno='c104';
```
`sum`是求和，并且自动忽略空值

```sql
select avg(grage) from sc where cno='c104';
```
`avg` 是求均值，也是忽略`null`的

```sql
select max(grade) from sc where cno='c104';
```
`max`是求最大值，并且也是忽略`null`的

```sql
select min(grage) from sc where cno='c104';
```
求最小值，其他同上

### 基于分组查询（group by子句）
先把查询结果分组，然后再运行`select`后面紧跟着的要显示的属性
* 细化聚集函数的作用对象
* 作用对象是查询的中间信息表（作用对象是`from...where...`的返回值）
* 按照指定的一列或者多列值分组
```sql
select cno,count(sno) from sc group by cno;
```
先把sc表中的符合条件的返回（这里没有where子句，所以sc表全部返回），然后按照`cno`属性分组，最后执行`select cno,count(sno)`部分，即显示`cno`列并且对`sno`列进行统计
![[Pasted image 20240620170003.png]]

#### 注意
`group by`的分组条件一定要在`select`后面得到展现，例如 
```sql
select sname,count(*) from student group by spno;
```
就是一个错误的sql语句，因为分组条件`spno`没有在`select`后面

正确的是
```sql
select spno,count(*) from student group by spno;
```

## 3.3.2 多表连接查询
连接操作的几种执行方式：
* 嵌套循环
* 排序合并
* 索引连接

### 笛卡尔积（无条件连接, R1 cross join R2）
笛卡尔积返回的结果是表一中的每一行和表二中的每一行组合后的结果

```sql
--查询已经选课的学生名单
select distinct student.sno,sname from student cross join sc;
```

或者这样的格式
```sql
select distinct student.sno, sname from student,sc; 
```

查询结果 
![[Pasted image 20240620191832.png]]

### 等值连接
```sql
--查询学生的所选课程编号
select student.sno,sname,cno from student join sc on(student.sno = sc.sno);
```
不同于普通的`select`语句，`join`后如果想要指定两个表之间的条件，需要使用`on`关键字而非`where`关键字
![[Pasted image 20240620192343.png]]

也可以写成
```sql
select student.sno, sname, cno from student, sc where student.sno = sc.sno;
```
这里没有使用`join`关键字，所以也就还是使用`where`指定条件

### 自然连接
```sql
--查询选课学生信息
select * from student natural join sc;
```
natural这个关键字好像还真没几个数据库支持

### 自身连接（自连接）
```sql
--查询同时选修了c104和c210课的同学
select sc1.sno from sc sc1, sc sc2 where sc1.sno = sc2.sno and sc1.sno = 'c104' and sc2.sno = 'c210';
```
注意：自连接必须为自己取两个别名，不然无法区分结果，这里就给sc取了sc1和sc2两个别名

### 外连接
* 左外连接
* 右外连接
* 全外连接
```sql
--左外连接
select student.sno sname, cno from student left join sc on(student.sno = sc.sno and cno='c210');
```
是`student`表`left join`表`sc`所以是以`student`表为主

![[Pasted image 20240620193123.png]]


### 多表连接
```sql
 --多表连接
 select student.sno,sname,t_d.dno,dname from student, t_sp, t_d where student.spon = t_sp.spno and t_sp.dno = t_d.dno;
```
![[Pasted image 20240620193300.png]]

### 总结
那么多连接，实际用的就是下面几个
* 内连接
* 外连接
	* 左外
	* 右外

## 3.3.3 嵌套连接
把一个`select-from-where`查询的结果作为另一个`select-from-where`的查询块

**注意**
* 子查询不能使用order by子句
* 将另一个select结果放入另一个查询块的where或者having短语块中

### 带有`in`子句的查询

```sql
--嵌套查询
select sno,sname from student where sno in (select sno from sc where cno='c104');
```
![[Pasted image 20240620193843.png]]


### 带有比较运算符的子查询
* 使用比较运算符的前提是你能确认内查询返回的是单值
```sql
select * from student where spon = (select spon from student where sname = '李逵');
```

例子：
查询其他专业中比软件工程专业任意一个学生学生年龄小的学生
```sql
select * from student where age < any(select sage from student where spon = '03') and spon <> '03';
```

### exists
这纯纯靠背的
![[Pasted image 20240620201704.png]]

![[Pasted image 20240620201715.png]]
## 3.3.4 集合查询

* union:并
* intersect：交
* except:差
```sql
--查询非计算机系，或者没有选修课程的学生
select sno,sname from student where in (select spon frim t_sp where dno in (select dno from t_d where dname <> '计算机' ))
unino 
select sno,sname from student where sno not in (select student.sno from student,sc where student.sno = sc.sno);
```

```sql
--查询同时选择了'c210'和'c211'课程的学生学号和姓名
select sno,sname from student where sno in (
(select sno from sc where cno='c210')
intersect
(select sno from sc where cno='c211')
) ;
```

```sql
--查询没有选择'c211'课程的学生学号
select sno from student 
except 
select sno from sc where cno='c211';
```

## 3.3.5 基于派生表的查询
就是把一个`select`查询的结果放在另一个`select-from`结构的`from`位置

# 3.4 数据更新
* 插入（insert）
* 修改 (update)
* 删除 (delete)

## 1. 插入数据
### 插入元组
![[Pasted image 20240620202611.png]]

### 插入子查询结果
![[Pasted image 20240620202632.png]]

### 样例
![[Pasted image 20240620202732.png]]

## 2. 修改数据
![[Pasted image 20240620202752.png]]

### 样例
```sql'
update t_t set tphone='18905510123' where tname = '高球'；

update sc set grade=80 where cno='c210' and '03' = (select spon from student where student.sno = sc.sno);
```

### 3. 删除数据
![[Pasted image 20240620203343.png]]

```sql
--删除学生张三的信息
delete from student where sname='张三';

--删除选了'c210'这这门课，并且是'03'这个系的学生
delete from sc where cno = 'c210' and '03' = (select spon from student where student.sno = sc.sno);
```

# 3.5 视图
**什么是视图：**
* 一个虚表，从一个或几个基本表或者视图导出的表
* 只存放视图的定义，但是具体的数据还是在基表中
* 基表中的数据发生变化，从视图中查询出的数据也发生变化

## 3.5.1 定义视图

这里还是看[[介绍和基本语法]]
### 1. 创建视图
![[Pasted image 20240620203739.png]]

### 2. 视图的作用
1. 简化用户操作
2. 使得用户可以从多个角度看待同一数据
3. 视图让数据拥有了一定的独立性，为重构数据库提供了一定的逻辑独立性
4. 视图能够对机密数据提供安全保护
5. 适当的利用视图可以更清晰的表达查询

