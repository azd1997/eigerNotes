---
title: "MySQL安装使用全教程"
date: 2019-11-24T21:00:38+08:00
draft: false
categories: ["linux"]
tags: ["mysql"]
keywords: ["mysql"]
---

## 1. 安装

ubuntu下安装参考： <https://www.jianshu.com/p/35e7af7db96a>

### 1.1 安装过程无设置用户密码提示

如果安装过程没有设置密码，之后怎么登录mysql并设置自己的密码呢？

参考：<https://www.cnblogs.com/super-zhangkun/p/9435974.html>

## 2. 使用

列举所有数据库：

```shell
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.02 sec)
```

创建新数据库：

```shell
mysql> create database go_video;
Query OK, 1 row affected (0.00 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| go_video           |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```

创建新数据表之前需要先使用某一个数据库：

```shell
mysql> USE go_video;
Database changed
```

创建新数据表：

```shell
mysql> CREATE TABLE users ( id INTEGER(10) UNSIGNED NOT NULL AUTO_INCREMENT, login_name VARCHAR(64) NULL UNIQUE, pwd TEXT NOT NULL, PRIMARY KEY (id));
Query OK, 0 rows affected (0.08 sec)

mysql> DESCRIBE users;
+------------+------------------+------+-----+---------+----------------+
| Field      | Type             | Null | Key | Default | Extra          |
+------------+------------------+------+-----+---------+----------------+
| id         | int(10) unsigned | NO   | PRI | NULL    | auto_increment |
| login_name | varchar(64)      | YES  | UNI | NULL    |                |
| pwd        | text             | NO   |     | NULL    |                |
+------------+------------------+------+-----+---------+----------------+
3 rows in set (0.01 sec)


mysql> CREATE TABLE video_info ( id VARCHAR(64) NOT NULL, author_id INTEGER(10), name TEXT, display_ctime TEXT, create_time DATETIME DEFAULT CURRENT_TIMESTAMP, PRIMARY KEY (id) );
Query OK, 0 rows affected (0.05 sec)

mysql> DESCRIBE video_info;                                                     +---------------+-------------+------+-----+-------------------+-------+
| Field         | Type        | Null | Key | Default           | Extra |
+---------------+-------------+------+-----+-------------------+-------+
| id            | varchar(64) | NO   | PRI | NULL              |       |
| author_id     | int(10)     | YES  |     | NULL              |       |
| name          | text        | YES  |     | NULL              |       |
| display_ctime | text        | YES  |     | NULL              |       |
| create_time   | datetime    | YES  |     | CURRENT_TIMESTAMP |       |
+---------------+-------------+------+-----+-------------------+-------+
5 rows in set (0.00 sec)


mysql> CREATE TABLE comments ( id VARCHAR(64) NOT NULL, video_id VARCHAR(64), author_id INTEGER(10), content TEXT, time DATETIME DEFAULT CURRENT_TIMESTAMP, PRIMARY KEY (id) );
Query OK, 0 rows affected (0.05 sec)

mysql> DESCRIBE comments;
+-----------+-------------+------+-----+-------------------+-------+
| Field     | Type        | Null | Key | Default           | Extra |
+-----------+-------------+------+-----+-------------------+-------+
| id        | varchar(64) | NO   | PRI | NULL              |       |
| video_id  | varchar(64) | YES  |     | NULL              |       |
| author_id | int(10)     | YES  |     | NULL              |       |
| content   | text        | YES  |     | NULL              |       |
| time      | datetime    | YES  |     | CURRENT_TIMESTAMP |       |
+-----------+-------------+------+-----+-------------------+-------+
5 rows in set (0.00 sec)


mysql> CREATE TABLE sessions ( session_id TINYTEXT, TTL TINYTEXT, login_name TEXT, PRIMARY KEY (session_id) );
ERROR 1170 (42000): BLOB/TEXT column 'session_id' used in key specification without a key length
mysql> CREATE TABLE sessions ( session_id TINYTEXT NOT NULL, TTL TINYTEXT, login_name TEXT, PRIMARY KEY (session_id) );
ERROR 1170 (42000): BLOB/TEXT column 'session_id' used in key specification without a key length
mysql> CREATE TABLE sessions ( session_id VARCHAR(255) NOT NULL, TTL TINYTEXT, login_name TEXT, PRIMARY KEY (session_id) );
Query OK, 0 rows affected (0.06 sec)

mysql> DESCRIBE sessions;
+------------+--------------+------+-----+---------+-------+
| Field      | Type         | Null | Key | Default | Extra |
+------------+--------------+------+-----+---------+-------+
| session_id | varchar(255) | NO   | PRI | NULL    |       |
| TTL        | tinytext     | YES  |     | NULL    |       |
| login_name | text         | YES  |     | NULL    |       |
+------------+--------------+------+-----+---------+-------+
3 rows in set (0.01 sec)
```

关于错误`ERROR 1170 (42000): BLOB/TEXT column 'session_id' used in key specification without a key length`的解释：

- <https://blog.csdn.net/helloxiaozhe/article/details/83018347>
- <https://bbs.csdn.net/topics/390242637>

就是说mysql的主键必须提供键长，而TEXT/BLOB类型是变长且不能指定长度的，所以不能作为主键，使用VARCHAR并指定长度，作为这种情况下的替代。

