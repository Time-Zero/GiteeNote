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

# 3.2 数据定义



