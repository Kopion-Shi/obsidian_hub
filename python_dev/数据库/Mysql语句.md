- 登录

```mysql
mysql -uroot -p
登录：
root
123456
```

```mysql
-- 每条语句都是以分号结尾的
-- 库---》文件夹
-- 表 ———》文件
-- 记录---》文件里面的一行行数据
/*
注释
*/

show databases;
\s -- 查看数据库字符编码以及其他信息
\c -- 结束当前语句
exit; -- 退出连接
quit; -- 退出连接

help -- 命令查看命令的帮助信息
```
- 操作库
```mysql
-- 增
create database db1;
create database db1 charset=utf8; -- 推荐 指定字符编码格式
-- 删
drop database db2;
-- 改
alter database db2 charset=utf8;-- 数据库只能该编码格式，其他的都不能改
-- 查
show database ;-- 查看所有
show create database db1; -- 查一个，查看创建这个库的sql语句 
```
- 操作表
```mysql
select database();-- 查看当前所在的数据库
use db1; -- 切换数据库
-- 增
create table movies(id int,name char);-- 创建表（默认的字符编码，就是库的字符编码）
create table movies(id int,name char) charset=utf-8;-- 创建表
-- 删
drop table movies; 
-- 改
alter table movies modify name char(4);-- 修改字段类型
alter table movies change name Name char(5);-- 修改字段的名字和类型
-- 查
show tables;-- 查看当前库下所有的表
show create table movies;-- 查看创建表的sql语句
describe movies;-- 查看一张表的结构，简写desc movies；
-- 所有对表的操作，都可以使用绝对路径的方式，这样即使比起切换数据库，也可以操作数据库对应的表
create table db2.movies(id int ,name char)
```
- 操作记录
```mysql
-- 增
insert into movies value(1,'流浪地球');
insert into movies value(1,'流浪地球'),(2,'三体');
-- 删
delete from movies where name='三体';
-- 改
update movies set name='满江红' where id=1;
-- 查
select * from movies;
select name from movies;
select id name  from movies;
select user host  from mysql.user;
```