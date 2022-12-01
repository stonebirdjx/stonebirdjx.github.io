---
title: "Mysql权限管理"
date: 2022-12-01T11:07:17+08:00
draft: true
comments: true
ShowReadingTime: true
tags: ["mysql","权限管理"]
---

## 创建用户
```bash
CREATE USER 'stonebirdjx'@'localhost' IDENTIFIED BY '${password}';
```
password：为此新建账号对应的密码。

> 需要远程访问把 localhost 改成对应 ip 即可 

## GRANT语法
```bash
GRANT privilege,[privilege],.. ON privilege_level 
TO user [IDENTIFIED BY password]
[REQUIRE tsl_option]
[WITH [GRANT_OPTION | resource_option]];
```
## REVOKE语法
```bash
REVOKE priv_type [(column_list)]...
ON database.table
FROM user [, user]...
```
## 给账号全部权限
```bash
```bash
GRANT ALL PRIVILEGES ON * . * TO 'stonebirdjx'@'localhost';
# 刷新权限
FLUSH PRIVILEGES;
```
## 查看权限
```bash
SHOW GRANTS FOR 'stonebirdjx'@'localhost';
```

## 取消权限
```bash
REVOKE type_of_permission ON database_name.table_name FROM 'stonebirdjx'@'localhost';
```
## 权限分类
```bash
ALL [PRIVILEGES]:授予除了GRANT OPTION之外的指定访问级别的所有权限
ALTER:允许用户使用ALTER TABLE语句
ALTER ROUTINE:允许用户更改或删除存储程序
CREATE:允许用户创建数据库和表
CREATE ROUTINE: 允许用户创建存储程序
CREATE TABLESPACE：允许用户创建，更改或删除表空间和日志文件组
CREATE TEMPORARY TABLES：允许用户使用CREATE TEMPORARY TABLE创建临时表
CREATE USER：允许用户使用CREATE USER，DROP USER，RENAME USER和REVOKE ALL PRIVILEGES语句。
CREATE VIEW：允许用户创建或修改视图
DELETE：允许用户使用DELETE
DROP：允许用户删除数据库，表和视图
EVENT：能够使用事件计划的事件
EXECUTE：允许用户执行存储过程/存储函数
FILE：允许用户读取数据库目录中的任何文件
GRANT OPTION：允许用户有权授予或撤销其他帐户的权限
INDEX：允许用户创建或删除索引
INSERT:允许用户使用INSERT语句
LOCK TABLES:允许用户在具有SELECT权限的表上使用LOCK TABLES
PROCESS:允许用户使用SHOW PROCESSLIST语句查看所有进程
PROXY:启用用户代理
REFERENCES:允许用户创建外键
RELOAD:允许用户使用FLUSH操作
REPLICATION CLIENT:允许用户查询主服务器或从服务器的位置
REPLICATION SLAVE:允许用户使用复制从站从主机读取二进制日志事件
SELECT:允许用户使用SELECT语句
SHOW DATABASES:允许用户显示所有数据库
SHOW VIEW:允许用户使用SHOW CREATE VIEW语句
SHUTDOWN:允许用户使用mysqladmin shutdown命令
SUPER:允许用户使用其他管理操作，如CHANGE MASTER TO，KILL，PURGE BINARY LOGS，SET GLOBAL和mysqladmin命令
TRIGGER:允许用户使用TRIGGER操作
UPDATE:允许用户使用UPDATE语句
USAGE: 相当于“无权限”
```