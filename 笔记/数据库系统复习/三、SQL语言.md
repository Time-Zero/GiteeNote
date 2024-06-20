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
	* 