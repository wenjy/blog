---
url: /2019/10/11/mysql-index-optimize.html
title: "Mysql 索引学习"
keywords: "Mysql,索引优化"
description: "Mysql 索引优化"
date: 2019-10-11T22:30:57+08:00
draft: false
tags: ["MySQL"]
tags_weight: 100
categories: ["MySQL"]
categoryes_weight: 100
---

## 一、索引
### 索引的类型（数据结构）
#### 1. B-Tree
```
所有引擎都支持
InnoDB(根据主键引用被索引的行，索引和数据同一个文件)、MyISAM(通过数据的物理位置应用被索引的行，索引和数据是不同的文件)
```
- 多列索引
```
需要满足最左原则，where条件必须和索引的顺序一致，如果只用到单列则必须是最左列
```

- InnoDB使用聚簇索引（一种数据存储方式）
```
聚簇索引的表现形式为，二级索引里包含了主键列（二级索引的行指针是指向行的主键值而不是行的物理地址），所以使用InnoDB时应该尽可能建一个递增的（顺序主键）如果没有则会隐式定义一个主键来作为聚簇索引
```

- 覆盖索引
```
如果一个索引包含所有需要查询的字段的值
```

- 使用索引扫描来做排序
```
只有当索引的列顺序和ORDER BY子句的顺序完全一致，并且所有列的排序方向（倒序或正序）都一样时，MySQL才能够使用索引来对结果做排序。如果查询需要关联多张表，则只有当ORDER BY子句引用的字段全部为第一个表时，才能使用索引做排序。也需要满足索引的最左前缀要求（例外：前导列为常量的时候）。
```

- 建如下表测试索引排序
```mysql
CREATE TABLE `test` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `realname` varchar(10) NOT NULL DEFAULT '',
  `sex` tinyint(4) NOT NULL DEFAULT '0',
  `age` tinyint(4) NOT NULL DEFAULT '0',
  `job` varchar(10) NOT NULL DEFAULT '',
  PRIMARY KEY (`id`),
  KEY `test_realname_sex_age_index` (`realname`,`sex`,`age`),
  KEY `sex_index` (`sex`),
  KEY `age_index` (`age`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```
#### 1. 使用两种不同的排序方向排序

- 能使用索引排序
```mysql
EXPLAIN SELECT * FROM test WHERE realname = 'wen' ORDER BY sex DESC ,age DESC;
```

- 不能使用索引排序
```mysql
EXPLAIN SELECT * FROM test WHERE realname = 'wen' ORDER BY sex DESC ,age ASC;
```
#### 2. 用了一个不在索引中的列

- 不能
```mysql
EXPLAIN SELECT * FROM test WHERE realname = 'wen' ORDER BY sex,job;
```
#### 3. where order by中的列无法组合成索引的最左前缀

- 不能
```mysql
EXPLAIN SELECT * FROM test WHERE realname = 'wen' ORDER BY age;
```
- 能
```mysql
EXPLAIN SELECT * FROM test WHERE realname = 'wen' ORDER BY sex;
```
#### 4. 查询在索引第一列上是范围条件

- 不能
```mysql
EXPLAIN SELECT * FROM test WHERE realname > 'wen' ORDER BY sex,age;

EXPLAIN SELECT * FROM test WHERE realname BETWEEN 'wen1' AND 'wen2' ORDER BY sex,age;
```
#### 5. 索引上有多个等于条件

- 不能
```
EXPLAIN SELECT * FROM test WHERE realname = 'wen' AND sex IN (1,2) ORDER BY  age;
```
#### 6. 前导列不为常量，并且使用范围条件

不能
```mysql
EXPLAIN SELECT * FROM test WHERE realname > 'qqq' ORDER BY realname;
```

能
```mysql
EXPLAIN SELECT * FROM test WHERE realname = 'qqq' ORDER BY realname;
```
- 选择合适的索引顺序

一般来说：将选择性最高的列放到索引的最前列。计算方法
```mysql
SELECT count(DISTINCT realname)/COUNT(\*) realname_selectivity,count(DISTINCT sex)/COUNT(\*) sex_selectivity,count(DISTINCT age)/COUNT(\*) age_selectivity,count(\*) total FROM test;
```

- 单索引排序

#### 1. where条件和排序的列不一致

不能使用索引排序 
```mysql
EXPLAIN SELECT * FROM test WHERE sex = 1 ORDER BY age;
```
能 
```mysql
EXPLAIN SELECT * FROM test WHERE sex = 1 ORDER BY sex;
```
能 
```mysql
EXPLAIN SELECT * FROM test WHERE age > 17 ORDER BY age;
```
#### 2. 没有where条件直接排序

主键可以排序 
```mysql
EXPLAIN SELECT * FROM test ORDER BY id;
```
不能 
```mysql
EXPLAIN SELECT * FROM test ORDER BY sex;
```
#### 3. 多where条件

不能，不满足左前缀 
```mysql
EXPLAIN SELECT * FROM test WHERE sex = 1 AND age > 17 ORDER BY age;
```
不能，不满足左前缀 
```mysql
EXPLAIN SELECT * FROM test WHERE sex > 1 AND age > 17 ORDER BY age;
```

不能，没有建多列索引 
```mysql
EXPLAIN SELECT * FROM test WHERE sex > 1 AND age > 18 ORDER BY sex,age;
```

能 
```mysql
EXPLAIN SELECT * FROM test WHERE age > 17 ORDER BY age;
```

能 
```mysql
EXPLAIN SELECT * FROM test WHERE sex > 1 AND age > 17 ORDER BY sex;
```
- 优化排序分页

排序分页达到一定数量时（使用ORDER BY LIMIT），越往后面翻页越卡，可以通过使用覆盖索引查询返回需要的主键，再根据主键关联原表获得所需要的行
```mysql
SELECT * FROM test WHERE realname = 'wen' ORDER BY sex LIMIT 100000,10;

SELECT * FROM test INNER JOIN (SELECT id FROM test WHERE realname = 'wen' ORDER BY sex LIMIT 100000,10) t USING (id);
```

#### 2. 哈希索引
Memory引擎显式支持

#### 3. 空间数据索引（R-Tree）

#### 4. 全文索引(Full-text)
同列可创建全文索引和B-Tree索引

## 二、查询优化
