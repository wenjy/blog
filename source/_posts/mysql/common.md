---
url: /2020/08/02/mysql-common-sql.html
title: "Mysql 常用的查询"
keywords: "Mysql"
description: "Mysql"
date: 2020-08-02T22:30:57+08:00
draft: false
tags: ["MySQL"]
tags_weight: 100
categories: ["MySQL"]
categoryes_weight: 100
---

## 查看数据版本
```mysql
select version();

select @@version;

SHOW VARIABLES LIKE "version";
```
## 查看当前会话隔离级别

```mysql
select @@tx_isolation;
```

## 查看系统当前隔离级别

```mysql
select @@global.tx_isolation;
```

## 查看当前的事务

```mysql
SELECT * FROM INFORMATION_SCHEMA.INNODB_TRX;
```

## 查看当前锁定的事务

```mysql
SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCKS;
```

## 查看当前等锁的事务

```mysql
SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCK_WAITS;
```

## 启用标准InnoDB监视器
```mysql
SET GLOBAL innodb_status_output=ON;
```

## 启用InnoDB锁定监视器
```mysql
SET GLOBAL innodb_status_output_locks=ON;
```

## 显示加锁信息
```mysql
SHOW ENGINE INNODB STATUS;
```

## 新建用户
```mysql
CREATE USER 'test1'@'127.0.0.1' IDENTIFIED BY '123456';
```

## 修改用户
```mysql
RENAME USER 'test1'@'127.0.0.1' TO 'test1'@'127.0.0.2';

SET PASSWORD FOR 'test1'@'127.0.0.2' = PASSWORD('1234567');
update user set password = password('123456') where user = 'test1';
grant all privileges on test_dadabase.* to 'test1'@'127.0.0.2' identified by '1234567';

flush privileges;
```
