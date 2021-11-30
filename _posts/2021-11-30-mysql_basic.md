---
layout: post
title: "mysql基础"
date:   2021-11-30
tags: [mysql]
comments: true
author: fyzjhh
---


### 一、安装mysql



#### yum 安装



#### 二进制包安装



1 、 下载mysql的二进制软件包 ， 解压 ，初始化环境

```
cd /usr/local
wget -c https://mirrors.163.com/mysql/Downloads/MySQL-5.7/mysql-5.7.33-linux-glibc2.12-x86_64.tar.gz
tar zxf mysql-5.7.33-linux-glibc2.12-x86_64.tar.gz
ln -s  mysql-5.7.33-linux-glibc2.12-x86_64  mysql
```


2 、 添加mysql账号 ， 把mysql的命令添加到path中


<!-- more -->


useradd mysql 



在文件 ~/.bashrc 中追加 如下内容

export PATH=/usr/local/mysql/bin:$PATH



3 、 编辑默认配置文件 vim /etc/my.cnf 



```
[mysqld]
server-id = 3306    
port   = 3306   # 端口
user   = mysql   # 启动账号
basedir   = /usr/local/mysql  # 软件目录
datadir   = /data/mysqldata_3306    # 实例的数据目录
socket    = mysql.sock   # socket文件
log_error   = error.log  # 错误日志文件
slow_query_log_file   = slow.log
skip-external-locking
skip-name-resolve 
explicit_defaults_for_timestamp
character-set-server = utf8mb4    # 默认字符集
slow_query_log =1             
long_query_time = 1          
binlog_format = row  
sync_binlog=1 
log_bin     = mysql-bin
max_binlog_size = 128M
binlog_cache_size = 10M
expire-logs-days = 15    
lower_case_table_names=1
max_connections=1000       
interactive_timeout=7200
connect_timeout = 60
slave_net_timeout=60

log_slave_updates   = 1
max_allowed_packet = 1024M
sort_buffer_size = 1M
read_buffer_size = 1M
read_rnd_buffer_size = 1M
join_buffer_size = 1M
tmp_table_size = 16M
max_heap_table_size = 64M
bulk_insert_buffer_size = 16M
thread_cache_size = 100
skip-slave-start
gtid_mode = on
enforce_gtid_consistency = 1

innodb_data_file_path = ibdata1:128M:autoextend  # innodb 系统表空间 配置
innodb_buffer_pool_size = 4G  # innodb 缓存大小 ， 重要
innodb_buffer_pool_instances = 8
innodb_log_file_size = 128M   # innodb 重做日志文件大小
innodb_log_buffer_size = 16M
innodb_log_files_in_group = 3  # innodb 重做日志的个数
innodb_flush_log_at_trx_commit = 0        
innodb_lock_wait_timeout = 30 
innodb_max_dirty_pages_pct = 75
innodb_thread_concurrency = 16      
innodb_flush_method = O_DIRECT
innodb_file_per_table = 1    
innodb_file_format = Barracuda
transaction_isolation=READ-COMMITTED   # 事务个隔离级别
```



4 、 初始化数据库的文件系统， 任选一个即可



```
mysqld  --initialize 
mysqld  --defaults-file=/etc/my.cnf --initialize 
```



5 、 启动 ， 任选一个即可



```
mysqld &
mysqld --defaults-file=/etc/my.cnf &

mysqld_safe &
mysqld_safe --defaults-file=/etc/my.cnf &
```





6 、 连接 和 简单测试



```
p=`egrep "password.*root" /data/mysqldata_3306/error.log  | awk -F': ' '{print $2}'`
mysql  --socket=/data/mysqldata_3306/mysql.sock  -h localhost -P 3306 -uroot -p${p}  -A
```



```
alter user root@localhost identified by  'xxx';
create database db1 ;
create user jhh@'%' identified by 'xxx' ;
```



#### 源码编译安装



#### 购买云数据库



1 初始化

- 创建数据库

- 添加账号

- 添加白名单

- 开通外网地址



2 连接



- mysql  客户端命令

 ```shell
 # 安装命令
 yum install mysql
 
 
 # 不带具体数据库的
 
 mysql -h ip或者域名 -P 端口 -u 账号 -p密码 
 
  
 
 # 带具体数据库的
 
 mysql -h ip或者域名 -P 端口 -u 账号 -p密码 库名
 
 # 例子
 mysql -h rm-bp1ca6l0s9a9z7d567o.mysql.rds.aliyuncs.com -P 3306 -u jhh -pxxx
 ```



-  dms

- workbench

- navicat





### 二、数据库操作





| 项目           | 命令                          | 备注                                   |
| -------------- | ----------------------------- | -------------------------------------- |
| 显示所有数据库 | show databases ;              |                                        |
| 查看某个数据库 | show create database dbname ; |                                        |
| 增加           | create database dbname xxx ;  | xxx 包含了 字符集 和 collation 2个选项 |
| 修改           | alter database dbname xxx ;   | xxx 包含了 字符集 和 collation 2个选项 |
| 删除           | drop database dbname ;        |                                        |







### 三、数据类型



#### 整数



| 数据类型  | 字节数 | 带符号最小值         | 带符号最大值        | 不带符号最小值 | 不带符号最大值       | 备注 |
| --------- | ------ | -------------------- | ------------------- | -------------- | -------------------- | ---- |
| TINYINT   | 1      | -128                 | 127                 | 0              | 255                  |      |
| SMALLINT  | 2      | -32768               | 32767               | 0              | 65535                |      |
| MEDIUMINT | 3      | -8388608             | 8388607             | 0              | 16777215             |      |
| INT       | 4      | -2147483648          | 2147483647          | 0              | 4294967295           | 常用 |
| BIGINT    | 8      | -9223372036854775808 | 9223372036854775807 | 0              | 18446744073709551616 | 常用 |



#### 小数



| 数据类型       | 字节数 | 备注         |
| -------------- | ------ | ------------ |
| FLOAT（M,D）   | 4      | 单精度浮点型 |
| DOUBLE（M,D）  | 8      | 双精度浮点型 |
| DECIMAL（M,D） | M+2    | 定点型 常用  |



浮点 ： 不能精确存储数据

定点： 可以精确存储数据

M ：整数和小数的总位数

D ：小数的位数



#### 时间





| 数据类型  | 字节数 | 格式                | 备注                                                    |
| --------- | ------ | ------------------- | ------------------------------------------------------- |
| date      | 3      | yyyy-MM-dd          | 存储日期值               常用                           |
| time      | 3      | HH:mm:ss            | 存储时分秒                                              |
| year      | 1      | yyyy                | 存储年                                                  |
| datetime  | 8      | yyyy-MM-dd HH:mm:ss | 1000-01-01 到 9999-12-31 存储日期+时间             常用 |
| timestamp | 4      | yyyy-MM-dd HH:mm:ss | 1970-01-01 到 2038-01-19 存储日期+时间                  |



#### 字符串





| 类型         | 大小                  | 用途                                 | 备注 |
| ------------ | --------------------- | ------------------------------------ | ---- |
| CHAR（N）    | 0-255 bytes           | 定长字符串                           | 常用 |
| VARCHAR（N） | 0-65535 bytes         | 变长字符串，建议存储的长度到4096以内 | 常用 |
| BLOB         | 0-65 535 bytes        | 二进制的长文本数据                   |      |
| TEXT         | 0-65 535 bytes        | 长文本数据                           | 常用 |
| LONGBLOB     | 0-4 294 967 295 bytes | 二进制的大文本数据                   |      |
| LONGTEXT     | 0-4 294 967 295 bytes | 大文本数据                           | 常用 |



CHAR 和 VARCHAR 需要带一个最大长度的参数



### 四、表操作



#### 创建

- 例子

```
    create table t1        (
    id                   bigint               not null  ,
    int_col              int                  not null  ,
    bigint_col           bigint               not null  ,
    varchar_col          varchar(64)          not null  ,
    datetime_col         datetime             not null  ,
    primary key(id)
    ) ;

/*
非空属性，带有默认值 ，注释等信息
*/
CREATE TABLE  if not exists  `t2` (
  `id` bigint(20) unsigned NOT NULL,
  `int_col` int(11) NOT NULL DEFAULT '1'  comment '用户的id',
  `bigint_col` bigint(20) NOT NULL,
  `varchar_col` varchar(64) NOT NULL DEFAULT '默认的说明'  comment '用户的姓名',
  `datetime_col` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ;
```

- 说明
  - 列的格式为 列名 类的数据类型（注意类型的参数） 非空属性 默认值 注释等
  - 可以带有 if not exists 选项

```
   create table if not exists t1        (
   id                      bigint                  not null  ,
   int_col                 int                     not null  ,
   primary key(id)
   ) ;
```

- 可以先使用use ，然后再创建表 ， 也可以直接创建表， 如需要在db2 中创建 tab 表 ，当前在db1 中

```
   use db2 ;
   create table t1        (
   id                      bigint                  not null  ,
   int_col                 int                     not null  ,
   primary key(id)
   ) ;

   create table db2.t1        (
   id                      bigint                  not null  ,
   int_col                 int                     not null  ,
   primary key(id)
   ) ;
```

- 主键 可以放在特定字段的后面， 也可以放到 所有字段的后面 ， 不推荐建完表之后 再添加主键

```
   create table t1        (
   id                      bigint                  not null  ,
   int_col                 int                     not null  ,
   primary key(id)
   ) ;

   create table t1        (
   id                      bigint                  not null  primary key,
   int_col                 int                     not null  
   ) ;
```

- 自增属性 直接加到字段的后面

```
   create table t1        (
   id                      bigint                  not null  primary key auto_increment,
   int_col                 int                     not null  
   ) ;
```

- 经常使用的表属性包括 引擎 字符集 自增值 分区 注释等

```
   create table t1        (
   id                      bigint                  not null  primary key auto_increment,
   int_col                 int                     not null  
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 AUTO_INCREMENT=1 COMMENT='注释信息';
```

#### 删除

- 例子

```
   drop table t1 ;
```

- 说明
  - 可以带有 if not exists 选项

```
   drop table if exists t1 ;
```

- 危险操作，执行前进行确认！

  

#### 修改



- 添加字段

```
    alter table t1 add column  col_10 int not null  ;
```

- 修改字段 , 注意 modify 和 change 的区别 , change 可以改名字

```
    alter table t1 modify column  col_10 bigint not null  ;
    alter table t1 modify change  col_11 bigint null  ;
```

- 表属性修改

```
    alter table t1 engine = myisam ;
    alter table t1 auto_increment  = 100 ;
```

#### 查看

- 查看所有表

```
    show tables ;
```

- 查看具体表

```
    show create table tablename ;
    desc tablename ;
```



#### 建表案例1 ： 企业的用户系统

```

CREATE TABLE t_user (
  user_id int(11) NOT NULL COMMENT '用户id' auto_increment,
  uname varchar(32) NOT NULL DEFAULT '' COMMENT '用户名',
  ubirthday date NOT NULL DEFAULT '1970-01-01' COMMENT '用户出生日期' ,
  uphone varchar(16) NOT NULL DEFAULT '' COMMENT '用户手机号' ,
    
  PRIMARY KEY (user_id),
  UNIQUE KEY uname (uname),
  KEY uphone (uphone)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='用户信息表';

CREATE TABLE t_login (
  user_id int(11) NOT NULL,
  uloginip varchar(32) NOT NULL DEFAULT '',
  ulogindatetime datetime NOT NULL DEFAULT '1970-01-01 00:00:00',
  uloginchannel varchar(32) NOT NULL DEFAULT '手机' COMMENT '手机，电脑，ipad等',
    
  PRIMARY KEY (user_id,ulogindatetime),
  KEY uloginip (uloginip),
  KEY ulogindatetime (ulogindatetime)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='用户登录信息表';

```



#### 建表案例2：电商系统

```sql
tab_user              用户
tab_user_address      用户地址
tab_friend            好友关系
tab_item              商品信息
tab_base_order        订单基本信息
tab_detail_order      订单详细信息
tab_logistics         物流信息


CREATE TABLE tab_user (
 id bigint(20) NOT NULL auto_increment,
 user_id bigint(20) NOT NULL ,
 user_name varchar(64) DEFAULT NULL,
 user_telephone varchar(64) DEFAULT NULL,
 create_datetime datetime NOT NULL ,
 PRIMARY KEY (id)
)  comment = '用户'  ; 

alter table tab_user
 add key key_user_id (user_id)
 ;

 
CREATE TABLE tab_user_address (
 id bigint(20) NOT NULL auto_increment,
 user_id bigint(20) NOT NULL ,
 receiver_name varchar(64) DEFAULT NULL,
 receiver_telephone varchar(64) DEFAULT NULL,
 receiver_sheng varchar(64) DEFAULT NULL,
 receiver_shi varchar(64) DEFAULT NULL,
 receiver_address varchar(512) DEFAULT NULL,
 create_datetime datetime NOT NULL ,
 PRIMARY KEY (id)
)   comment = '用户地址'  ; 

alter table tab_user_address
 add key key_user_id (user_id)
 ;
 
CREATE TABLE tab_friend (
 id bigint(20) NOT NULL auto_increment,
 user_id bigint(20) NOT NULL ,
 friend_id bigint(20) NOT NULL ,
 create_datetime datetime NOT NULL ,
 PRIMARY KEY (id)
)    comment = '用户好友关系'  ; 

alter table tab_friend
 add key key_user_id (user_id),
 add key key_friend_id (friend_id)
 ;


CREATE TABLE tab_item (
 id bigint(20) NOT NULL auto_increment,
 item_id bigint(20) NOT NULL ,
 item_name varchar(128) NOT NULL ,
 item_desc varchar(1024) NOT NULL ,
 create_datetime datetime NOT NULL ,
 PRIMARY KEY (id)
)  COMMENT = '商品信息' ; 

alter table tab_item
 add key key_item_id (item_id)
 ;
 
 
CREATE TABLE tab_order_base (
 id bigint(20) NOT NULL auto_increment,
 order_base_id bigint(20) NOT NULL ,
 user_id bigint(20) NOT NULL ,
 order_total_money decimal(16,2) NOT NULL ,
 create_datetime datetime NOT NULL ,
 PRIMARY KEY (id)
)  comment = '订单基本信息'  ; 

alter table tab_order_base
 add key key_user_id (user_id) ,
 add key key_order_base_id (order_base_id) 
 ;

 
CREATE TABLE tab_order_detail (
 id bigint(20) NOT NULL auto_increment,
 order_base_id bigint(20) NOT NULL ,
 order_detail_id  bigint(20) NOT NULL ,
 item_id bigint(20) NOT NULL ,
 item_count int(11) NOT NULL ,
 item_unit_money decimal(16,2) NOT NULL ,
 create_datetime datetime NOT NULL ,
 PRIMARY KEY (id)
)   COMMENT = '订单详细信息' ; 

alter table tab_order_detail
 add key key_order_base_id (order_base_id) ,
 add key key_order_detail_id (order_detail_id) 
 ;
 
 
CREATE TABLE tab_logistics (
 id bigint(20) NOT NULL auto_increment,
 logistics_id bigint(20) NOT NULL ,
 logistics_company varchar(128) NOT NULL ,
 order_detail_id bigint(20) NOT NULL ,
 create_datetime datetime NOT NULL ,
 PRIMARY KEY (id)
)   COMMENT = '物流信息' ; 

alter table tab_logistics
 add key key_logistics_id (logistics_id)
 ;
```





### 五、索引操作

#### 索引的概念

```
用来快速访问数据库表里的数据
不同的数据操作语句需要不同的索引，故表中需要建哪些索引是根据实际的需求而定
详细的结构和原理后续再讲
```

#### 操作

- 创建
  - 索引的结构为 index 索引名 (索引包含的字段)
  - 建表时创建索引 ， 建议通过此方式

```
        create table t1        (
        id                   bigint               not null  ,
        int_col              int                  not null  ,
        bigint_col           bigint               not null  ,
        varchar_col          varchar(64)          not null  ,
        datetime_col         datetime             not null  ,
        primary key(id),
        index idx1 (int_col),
        index idx2 (varchar_col)
        ) ;
```

- 建完表之后，再添加索引

```
    alter table t1 add index idx3 (bigint_col) ;
    CREATE INDEX idx3 ON t1 (bigint_col) ;
```

- 删除
  - 通过修改表结构完成 ， 建议通过此方式

```
    alter table t1 drop index idx1 ;
```

- 通过删除索引的命令完成

```
    drop index idx1 on t1 ;
```

- 修改索引
  - 通过删除再添加来完成
- 查看
  - 通过查看表结构来查看 ， 建议通过此方式
  - 通过查看索引命令查看

```
    show indexes from t1 ;
```





六、插入语句



#### 1、基本语法

```sql
INSERT INTO TABLENAME [(字段列表)] VALUES (值列表) [ ,(值列表) ] ;
```

#### 2、说明



- 插入的值 是数字 后者 函数 不要带引号 ， 其他的需要带引导
- 插入可能成功，可能失败 ， 比如主键冲突， 字段不匹配等
- 多行插入的时候，要不都成功 ， 要不都失败



#### 3  、 不带字段

```sql

 insert into t_user values  ( 1 , '江和慧' , '1987-11-08' , '13900001234')  ;

```

#### 4 、 带有全部字段



```sql

insert into t_user (user_id , uname , ubirthday , uphone ) values ( 1 , '江和慧' , '1987-11-08' ,'13900001234')  ;

```



#### 5 、 带有部分字段 



部分字段的可能性 ：

- 自增  

  ```sql
  insert into t_user (  uname , ubirthday , uphone )  values  ( 'a' , '1987-11-08' , '13900001234')  ;
  ```

  

- 默认值 

  ```sql
  insert into t_user (user_id , uname , uphone ) values ( 6 , 'c' , '13900001234')  ;
  ```

  

- 自生成的时间 

  ```sql
  CREATE TABLE tab_1 (
    id int(11) NOT NULL COMMENT '用户id' auto_increment,
    create_time datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    update_time datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间' ,
    primary key(id)
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
  
  insert into tab_1 ( id ) values ( 3 )  ; 
  ```

  

#### 6 、 多行插入 

```sql
insert into t_user (user_id , uname , ubirthday , uphone )  values  
( 1 , '江和慧' , '1987-11-08' , '13900001234') ,
( 2 , 'a' , '1990-01-08' , '13700001234') ,
( 1 , 'b' , '1991-11-18' , '13900001234') 
;

```

#### 7 、 带有函数插入 

```sql
insert into t_user (user_id , uname , ubirthday , uphone )  values  ( 1 , '江和慧' , CURRENT_DATE() , '13900001234')  ;


insert into t_user (user_id , uname , ubirthday , uphone )  values  ( 2 , '江' , ADDDATE(CURRENT_DATE(), INTERVAL -30 YEAR)  , '13900001234' )  ;

insert into t_user (user_id , uname , ubirthday , uphone )  values 
(

ROUND(RAND()*(999999)) , 
concat ('uname_' , ROUND(RAND()*(999999)) ) ,    
DATE_ADD( now(), INTERVAL -ROUND(RAND()*9999)-1000 DAY ) ,
concat ('139000000' , ROUND(RAND()*(99)) )

);

```



#### 8、主键和唯一键冲突

```sql

# uname 是唯一索引
insert into t_user (  uname , ubirthday , uphone )  values  (   '江和慧' , '1987-11-08' , '13900001234')  ;
insert into t_user (  uname , ubirthday , uphone )  values  (   '江和慧' , '1987-11-08' , '13900001234')  ;


# user_id 和 ulogindatetime  复合主键
insert into t_login (user_id , uloginip , ulogindatetime  , uloginchannel )  values  
( 10 , '12.23.12.98' , '2021-04-08 11:10:01' ,'手机'),
( 10 , '12.23.12.98' , '2021-04-09 11:10:01' ,'手机')
;
insert into t_login (user_id , uloginip , ulogindatetime  , uloginchannel )  values  
( 10 , '12.23.12.98' , '2021-04-08 11:10:01' ,'手机')
;
```





### 七、查询语句



#### 1、基本语法

```sql
SELECT column_name,column_name
FROM table_name
[WHERE Clause]
;
```

#### 2、说明



- from 子句中可以是多个表 ，即join操作 ， 包括 cross join ， inner join , outer join 等
- 结果可能为0,1,2,3，... 条记录
- 可以使用 * 来代替选择的字段， 即选择所有字段 
- 如果只需要选择部分字段， 需要明确写出
- where 后面的条件可以省略，代表选择所有记录

 #### 3、准备数据

```sql

insert into t_user (user_id , uname , ubirthday  , uphone )  values  
( 10 , '江某' , '1986-11-08' ,'13900000010'),
( 11 , '李某' , '1987-12-03' ,'13900000011'),
( 12 , '徐某' , '1988-02-08' ,'13900000012'),
( 13 , '张某' , '1989-11-06' ,'13900000013'),
( 14 , '黄某' , '1987-01-07' ,'13900000014')
  ;
  
insert into t_login (user_id , uloginip , ulogindatetime  , uloginchannel )  values  
( 10 , '12.23.12.98' , '2021-04-08 11:10:01' ,'手机'),
( 10 , '12.23.12.98' , '2021-04-09 13:17:32' ,'手机'),
( 10 , '10.56.12.15' , '2021-05-01 10:24:01' ,'电脑'),
( 11 , '19.13.12.17' , '2021-04-01 09:31:01' ,'手机'),
( 11 , '19.13.12.17' , '2021-04-01 10:31:01' ,'手机'),
( 12 , '37.16.12.17' , '2021-04-01 11:31:01' ,'电脑'),
( 14 , '46.19.32.15' , '2021-04-01 12:15:01' ,'电脑'),
( 14 , '64.25.12.17' , '2021-04-01 13:15:01' ,'手机'),
( 14 , '64.25.12.17' , '2021-04-01 14:15:01' ,'手机')
  ;
  
```

#### 4、简单查询

```sql


# 全表查询
select * from t_user ; 
select * from t_user where 2>1 ; 
select user_id , uname , ubirthday  , uphone from t_user ; 
select   uname , ubirthday  , uphone from t_user where 1=1  ; 


# 过滤部分数据行

=   、   范围 >  <  、  IN   、 LIKE  、 BETWEEN AND


select user_id , uname , ubirthday  , uphone from t_user where  user_id = 10 ;  

select user_id , uname , ubirthday  , uphone from t_user where  uname = '李某' ; 

select * from t_login where  uloginchannel = '电脑' ; 


select user_id , uname , ubirthday  , uphone from t_user where  user_id IN (10 ,12,13) ;

select user_id , uname , ubirthday  , uphone from t_user where  uname like '%某' ;

select user_id , uname , ubirthday  , uphone from t_user where  ubirthday>'2021-04-01' and ubirthday<'2021-05-01' ;

select  uname , ubirthday   from t_user where  ubirthday BETWEEN '2021-04-01' AND  '2021-05-01' ;

#  逻辑复合

select * from t_login where  uloginchannel = '电脑' and ulogindatetime>'2021-05-01'; 
select * from t_login where  ulogindatetime like '2021-05-%' OR ulogindatetime like '2020-10-%'; 

#  OR 是在 AND的外层 

select * from t_login where user_id=10 AND user_id=11 OR user_id=12 AND user_id=13; 

select * from t_login where (user_id=10 AND user_id=11)  OR  ( user_id=12 AND user_id=13 );


```



#### 5、排序和分组



- 排序使用 order by  ， 后面可以接1个或者多个字段
- ASC 是升序 ， DESC 是降序
- 分组使用 group by  ， 后面可以接1个或者多个字段
- 分组涉及到聚合函数，count , sum , max , min , avg 等 
- 分组之后如需过滤 ，使用having 关键词
- 特殊的关键词 distinct 取唯一值

```sql
# 排序
select * from t_login where  uloginchannel = '电脑'  order by  ulogindatetime  ; 

select * from t_login where  uloginchannel = '电脑'  order by  ulogindatetime ASC ; 

select * from t_login where  uloginchannel = '电脑'  order by  ulogindatetime DESC ; 


# 分组
select uloginchannel , count(1) v from t_login group by  uloginchannel   ; 

select uloginchannel , count(1) v from t_login group by  uloginchannel having v > 1 ; 

# 分组和排序的组合
select uloginchannel , count(1) v from t_login group by  uloginchannel having v > 1  order by v desc ;
```



#### 6、连接查询



- 笛卡尔

![image-20210815171917566](C:\Users\jianghehui\AppData\Roaming\Typora\typora-user-images\image-20210815171917566.png)

 ```sql
 
 
 select t_user.user_id , uname  , t_login.user_id  ,  uloginip , ulogindatetime
 from t_user  ,  t_login 
 where  uloginchannel = '电脑' ;
 
 ```



- 内连接



![img](https://www.runoob.com/wp-content/uploads/2014/03/img_innerjoin.gif)



 ```sql
# 内连接

select t_user.user_id , uname  , t_login.user_id  ,  uloginip , ulogindatetime
from t_user  join  t_login on (t_user.user_id = t_login.user_id)
where  uloginchannel = '电脑' ;

select t_user.user_id , uname  , t_login.user_id  ,  uloginip , ulogindatetime
from t_user inner join  t_login on (t_user.user_id = t_login.user_id)
where  uloginchannel = '电脑' ;


# 注意条件放在 on 里面 和  where 里面的区别
# on的总体效果比where 好， 所以尽量放到on中
select t_user.user_id , uname  , t_login.user_id  ,  uloginip , ulogindatetime
from t_user inner join  t_login on (t_user.user_id = t_login.user_id and  t_login.uloginchannel = '电脑' )  ;

 ```

- 左外连接

![img](https://www.runoob.com/wp-content/uploads/2014/03/img_leftjoin.gif)

 ```sql
# 左外连接

select t_user.user_id , uname  , t_login.user_id  ,  uloginip , ulogindatetime
from t_user left join  t_login on (t_user.user_id = t_login.user_id)
where  uloginchannel = '电脑' ;

 

 ```



- 右外连接

![img](https://www.runoob.com/wp-content/uploads/2014/03/img_rightjoin.gif)



 ```sql
# 右外连接

select t_user.user_id , uname  , t_login.user_id  ,  uloginip , ulogindatetime
from t_user right join  t_login on (t_user.user_id = t_login.user_id)
where  uloginchannel = '电脑' ;


 ```



#### 如何求差集 ？



 ```sql
 

select t_user.user_id , uname  , t_login.user_id  ,  uloginip , ulogindatetime
from t_user left join  t_login on (t_user.user_id = t_login.user_id)
where  t_login.user_id is null   ;
 

 ```



#### 7、合并操作  UNION



- 多个SELECT语句组合起来， 并（Union）或者复合查询
- 合并多个结果集为一个结果集
- union 包含了去重
- union all 不包含去重

```sql


# 合并2个表中的用户id ， 包含了去重

select  user_id  from t_user
union 
select  user_id  from t_login
;

# 合并2个表中的用户id ，不去重

select  user_id  from t_user
union all
select  user_id  from t_login
;


# 多张表的合并 ， 如有 3张表 tab_1 , tab_2 , tab_3 ， 结构类似 , union all 的写法如下 

CREATE TABLE tab_1 (
  id int(11) NOT NULL COMMENT '用户id' auto_increment,
  create_time datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  update_time datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间' ,
  primary key(id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

select  id , create_time , update_time  from tab_1 [where id>10]
union all
select  id , create_time , update_time  from tab_2 [where id>20]
union all
select  id , create_time , update_time  from tab_3 [where id>30]
;


```



#### 8、子查询



子查询必须放在小括号中
子查询可出现在几乎所有的SELECT子句(如：SELECT子句、FROM子句、WHERE子句、ORDER BY子句)

包含了相关子查询和非相关的子查询 ， 相关的子查询 是指 内部子句和外部子句有关联管理，故比较复杂，不做涉及

一般需要对子查询取别名，方便理解SQL

```sql
select *
from tab_1
where id in (
　　select id
　　from tab_2 );
　　
select * from (　
select  id , create_time , update_time  from tab_1 [where id>10]
union all
select  id , create_time , update_time  from tab_2 [where id>20]
union all
select  id , create_time , update_time  from tab_3 [where id>30]
) t  order by id desc 
;

```



#### 9、limit和offset用法



- 查看前几条记录
- 查看一定偏移量之后的几条记录
- 分页查询

```sql
# 查看前10条
select  *  from t_user  where id>10 limit 10 ;
# 查看100条后面的 10条 
select  *  from t_user  where id>10 limit 100 ,10 ;
select * from t_user  LIMIT 10 OFFSET 100 ;


# 子查询合并结果集之后， 外层进行排查， 然后再查看前10条
select * from (　
select  id , create_time , update_time  from tab_1 [where id>10]
union all
select  id , create_time , update_time  from tab_2 [where id>20]
union all
select  id , create_time , update_time  from tab_3 [where id>30]
) t  order by id desc  limit 10
;

```



#### 10、select 语句的执行顺序：
```sql

1  from 子句
2  where 子句
3  group by 子句 
4  having 子句
5  order by 子句
6  limit 子句
7  select 选择列 子句

```



#### 11、别名

```sql

# 可以用在表和字段上

select t1.user_id , t1.uname  , t2.user_id  ,  uloginip , ulogindatetime
from t_user  t1  left join  t_login t2  on (t1.user_id = t2.user_id)
where  t2.user_id is null   ;

```





### 八、删除语句



#### 单表删除

```sql
# 语法
DELETE FROM table_name [WHERE xxx]

# 删除1988年之后出生的用户
delete from t_user where ubirthday > '1988-01-01' ;

```

#### 关联删除

```sql
# 语法
 DELETE  t1 FROM t1 , t2   [WHERE xxx]
 
 # 删除使用电脑登录过的用户信息
 delete t_user from t_user , t_login 
 where   t_user.user_id = t_login.user_id AND uloginchannel = '电脑' ;

```



### 九、更新语句



#### 单表更新

```sql
# 语法
UPDATE  table_name SET col=value , col=value [WHERE xxx]

# 例子
 UPDATE  t_user SET ubirthday ='1995-05-01' where user_id=11 ;
 UPDATE  t_user SET ubirthday ='1995-05-01' , uphone ='13900000015' where user_id=11 ;
```

#### 关联更新

```sql
# 语法
UPDATE table_references 
SET col_name1=expr1 [, col_name2=expr2 ...] [WHERE where_definition]

# 例子
update t_user , t_login
set t_login.uloginip='192.168.1.1',t_login.uloginchannel='PC'
where  t_login.user_id=t_user.user_id and t_user.ubirthday < '1988-01-01' ;

update t_user , t_login
set t_login.uloginip='192.168.1.1',t_login.uloginchannel='PC' , t_user.uphone='123'
where  t_login.user_id=t_user.user_id and t_user.ubirthday > '1988-01-01' ;

```



### 十、练习



#### 设计如下4张表 ，并且初始化一些数据



```sql

tab_student  学生表
tab_teacher  教师表
tab_course   课程表
tab_score    成绩表

```



```sql


create table tab_student(
student_id int not null , 
student_name varchar(10) comment '姓名'  ,
student_birthday date comment '生日',
student_sex char(1) comment '性别',
primary key (student_id)
) COMMENT='学生表';

insert into tab_student values(1 , '赵1' , '1990-01-01' , '男');
insert into tab_student values(2 , '赵2' , '1990-12-21' , '男');
insert into tab_student values(3 , '赵3' , '1990-05-20' , '女');
insert into tab_student values(4 , '周1' , '1990-08-06' , '男');
insert into tab_student values(5 , '周2' , '1991-12-01' , '男');
insert into tab_student values(6 , '周3' , '1992-03-01' , '女');
insert into tab_student values(7 , '郑1' , '1989-07-01' , '女');
insert into tab_student values(8 , '郑1' , '1989-07-01' , '男');
insert into tab_student values(9 , '张1' , '2017-12-20' , '男');
insert into tab_student values(10 , '李1' , '2017-12-25' , '女');
insert into tab_student values(11 , '李2' , '2017-12-30' , '男');

create table tab_teacher(
teacher_id int not null , 
teacher_name varchar(10) , 
teacher_level varchar(3)  not null comment '级别',
primary key (teacher_id)
) COMMENT='教师表';
insert into tab_teacher values(1 , '张老师' , '高级') ;
insert into tab_teacher values(2 , '李老师' , '中级') ;
insert into tab_teacher values(3 , '王老师' , '高级') ;

create table tab_course(
course_id int not null , 
course_name varchar(10),
teacher_id int not null , 
primary key (course_id)
) COMMENT='课程表';
insert into tab_course values(1 , '语文' , 1)  ;
insert into tab_course values(2 , '数学' , 2)  ;
insert into tab_course values(3 , '英语' , 3)  ;

create table tab_score(
student_id int not null , 
course_id int not null , 
score decimal(4,1) ,
primary key (student_id,course_id)
) COMMENT='成绩表';
insert into tab_score values(1 , 1 , 80.0)  ;
insert into tab_score values(1 , 2 , 90.0)  ;
insert into tab_score values(1 , 3 , 99.0)  ;
insert into tab_score values(2 , 1 , 70.0)  ;
insert into tab_score values(2 , 2 , 60.0)  ;
insert into tab_score values(2 , 3 , 80.0)  ;
insert into tab_score values(3 , 1 , 80.5)  ;
insert into tab_score values(3 , 2 , 80.5)  ;
insert into tab_score values(3 , 3 , 80.0)  ;
insert into tab_score values(4 , 1 , 50.0)  ;
insert into tab_score values(4 , 2 , 30.5)  ;
insert into tab_score values(4 , 3 , 20.5)  ;
insert into tab_score values(5 , 1 , 76.5)  ;
insert into tab_score values(5 , 2 , 87.0)  ;
insert into tab_score values(6 , 1 , 31.0)  ;
insert into tab_score values(6 , 3 , 34.0)  ;
insert into tab_score values(7 , 2 , 89.0)  ;
insert into tab_score values(7 , 3 , 98.0)  ;
```





#### 查询每个表的总记录数
```sql
select 'tab_student' tab_name , count(1) rc from tab_student  ;
select 'tab_teacher' tab_name , count(1) rc from tab_teacher  ;
select 'tab_course ' tab_name , count(1) rc from tab_course   ;
select 'tab_score  ' tab_name , count(1) rc from tab_score    ;
```



#### 查询每个表的样例数据， 前1-3条  ， 超过三条的表同时查询4-6条
```sql

select * from tab_student limit 3,3 ;
select * from tab_teacher limit 3 ;
select * from tab_course  limit 3 ;
select * from tab_score   limit 3,3 ;

```



#### 查询每个表的表结构
```sql

show create table tab_student ;
show create table tab_teacher ;
show create table tab_course  ;
show create table tab_score   ;

desc  tab_student ;
desc  tab_teacher ;
desc  tab_course  ;
desc  tab_score   ;

```



#### 查询姓李和 张的学生信息
```sql

select * from  tab_student where student_name like '李%' or student_name like '张%' ;

```



#### 查询及格的成绩
```sql

select * from  tab_score where score >= 60 ;

```



#### 查询90后的学生信息
```sql

select * from  tab_student where student_birthday >='1990-01-01' and student_birthday < '2000-01-01' ;


```



#### 查询每个课程的最高分,最低分,平均分

```sql

select course_id , min(score) min_score , max(score) max_score, avg(score) avg_score 
from tab_score 
group by course_id 


select course_id , min(score) min_score , max(score) max_score, round( avg(score) , 1 )    avg_score  from tab_score  group by course_id;

```



#### 查询课程的分数，按照分数从高到低进行排序

```sql

select *
from tab_score 
order by score desc  ;

```



#### 查询学生的总成绩排名
```sql

select t1.student_name , sum(t2.score) total_score 
from tab_student t1 join tab_score t2 
on (t1.student_id = t2.student_id ) 
group by t1.student_name 
order by total_score desc ;

```



#### 查询每个课程的最高分,最低分,平均分 , 课程使用课程名称显示

```sql

select course_name , min(score) , max(score) , avg(score)
from tab_score join tab_course on (tab_score.course_id = tab_course.course_id)
group by course_name 

```



#### 查询所有学生 的 成绩信息 , 没有成绩的显示为NULL
```sql

select t1.student_name , t2.score
from tab_student t1 left join tab_score t2 
on (t1.student_id = t2.student_id ) 

```



#### 查询所有学生 的 成绩信息 , 没有成绩的显示为 -1 
```sql

select t1.student_name , ifnull(t2.score , -1)
from tab_student t1 left join tab_score t2 
on (t1.student_id = t2.student_id ) 

```



#### 查询成绩异常的信息，即没有成绩的学生信息
```sql

select t1.student_name ,  t2.score 
from tab_student t1 left join tab_score t2 
on (t1.student_id = t2.student_id ) 
where t2.score is null 

```



### 十一、导出导入



#### mysqldump 导出

```sql
# 常用选项
-h                     数据库的主机
-P                     数据库的端口
-u                     数据库的账号
-p                     数据库的密码

--no-create-db         -n 取消创建数据库sql 
--no-create-info       -t 取消创建表sql 
--no-data              -d 不导出数据 
--single-transaction   导出操作在单个事务中
--master-data          导出更新的位点，配合--single-transaction使用， 可以备份数据库

--set-gtid-purged      设置对GTID的处理
--hex-blob             处理二进制数据
--skip-comments        取消注释
--skip-add-locks       取消锁
--skip-lock-tables     取消锁表
--skip-add-drop-table  取消添加drop语句
--extended-insert      使用多行insert

# 导出表结构和表数据 例子
mysqldump -h 192.168.10.1 -P 3306 -uroot -pmysql  -n --hex-blob --skip-add-locks --skip-lock-tables --skip-add-drop-table dbname >  dbname.sql

# 导出表结构 例子
mysqldump -h 192.168.10.1 -P 3306 -uroot -pmysql  -n -d --hex-blob --skip-add-locks --skip-lock-tables --skip-add-drop-table dbname > create.dbname.sql
 
# 导出表数据 例子
mysqldump -h 192.168.10.1 -P 3306 -uroot -pmysql  -n -t --hex-blob --skip-add-locks --skip-lock-tables --skip-add-drop-table dbname > insert.dbname.sql


# 导出表结构和表数据 例子
mysqldump -h rm-bp1ahr7vm2llfm29dbo.mysql.rds.aliyuncs.com -u jhh -pxxx  -n --hex-blob --skip-add-locks --skip-lock-tables --skip-add-drop-table --skip-comments db1 >  bak_db1.sql

# 导出一致性的备份，并且带有 change master 命令
mysqldump -h rm-bp1ahr7vm2llfm29dbo.mysql.rds.aliyuncs.com -u jhh -pxxx --single-transaction --master-data=2 -n --hex-blob --skip-add-locks --skip-lock-tables --skip-add-drop-table --skip-comments test >  bak_test.sql

# 导出表结构 例子
mysqldump -h rm-bp1ahr7vm2llfm29dbo.mysql.rds.aliyuncs.com -u jhh -pxxx  -n -d --hex-blob --skip-add-locks --skip-lock-tables --skip-add-drop-table db1 > create.db1.sql
 
# 导出表数据 例子
mysqldump -h rm-bp1ahr7vm2llfm29dbo.mysql.rds.aliyuncs.com -u jhh -pxxx  -n -t --hex-blob --skip-add-locks --skip-lock-tables --skip-add-drop-table db1 > insert.db1.sql


```



#### select...into outfile 导出

```sql


# 根据源数据库的信息， 生成查询某个库所有表的记录数

select 
concat('select \' ' , table_name , ' \'  tabname , count(1) rc from ' , table_name , ' ;' ) 
into outfile '/tmp/a.sql'  
from  information_schema.TABLES 
where table_schema='zabbix' ;

```



#### source导入

```sql

mysql -h 192.168.10.1 -P 3306 -uroot -pmysql dbname -e " source ./insert.dbname.sql ;" 

```



#### 标准输入文件导入



```sql

mysql-h 192.168.10.1 -P 3306 -uroot -pmysql dbname  < ./insert.dbname.sql  

```



#### load data 导入

```sql

LOAD DATA LOCAL INFILE 'data.txt' INTO TABLE table (col1,col2) ;

```





#### 一般工具中也有导出导入的功能



- dms
- navicat



### 十二、事务控制



#### 事务的特性



原子性：要么完全提交 ，要么完全回滚。
一致性：不会违反数据库的约束，总是从一个有效状态转换到另一个有效状态。
隔离性：不同事务中同时操作时候，有一些问题，需要搭配不同的隔离措施，即隔离级别隔离级别来定义。
持久性：事务完成之后，就会一直保持。



#### 准备SQL

```sql

CREATE TABLE t (
  user_id int(11) NOT NULL COMMENT '用户id' auto_increment,
  uname varchar(32) NOT NULL DEFAULT '' COMMENT '用户名',
  balance decimal(16,2) not null COMMENT '余额',
    
  PRIMARY KEY (user_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='用户余额表';

insert into t (user_id , uname , balance  )  values  
( 10 , '江某' , 1310),
( 11 , '李某' , 1311),
( 12 , '徐某' , 1312),
( 13 , '张某' , 1313),
( 14 , '黄某' , 1314)
  ;
  
```



#### 正常提交的例子

```sql
#### A 转账 200 到 B 

BEGIN;

# 查询
SELECT balance FROM t WHERE customer_id = A;

# 业务验证 余额是否足够 ， 如果不够 ，提示并停止转账
if ( balance < 200 ) {
  stop and output tip
}else{

# A 减少 200
UPDATE t SET balance = balance - 200.00 WHERE customer_id = A;

# B 增加 200
UPDATE t SET balance = balance + 200.00 WHERE customer_id = B;

}

COMMIT;


#### 操作SQL
BEGIN;
 
SELECT balance FROM t WHERE user_id = 10;

UPDATE t SET balance = balance - 200.00 WHERE user_id = 10;

UPDATE t SET balance = balance + 200.00 WHERE user_id = 11;

COMMIT;
```

#### 回滚的例子

```sql

#### A 转账 200 到 B  

BEGIN;

# 查询
SELECT balance FROM t WHERE customer_id = A;

# 业务验证 余额是否足够 ， 如果不够 ，提示并停止转账
if ( balance < 200 ) {
  stop and output tip
}else{

# A 减少 200
UPDATE t SET balance = balance - 200.00 WHERE customer_id = A;

# 需要撤销之前的变更
ROLLBACK ;

# B 增加 200 , 不会执行
# UPDATE t SET balance = balance + 200.00 WHERE customer_id = B;

}
 
 
#### 操作SQL
BEGIN;
 
SELECT balance FROM t WHERE user_id = 10;

UPDATE t SET balance = balance - 200.00 WHERE user_id = 10;

ROLLBACK ;
```







#### 隔离的问题



1 无隔离级别的情况下 ， 会产生 更新丢失 问题

更新丢失：当两个或多个事务基于最初选定的值更新同一数据时，最后事务的更新会覆盖了其他事务之前所做的更新。 



2 有隔离级别的情况下，会产生 脏读 不可重复读 幻读 问题

脏读：一个事务正在对一条记录做修改，在这个事务完成并提交前，另一个事务也来读取同一条记录，第二个事务读取了这些未提交的数据，并据此做进一步的处理。


不可重复读：一个事务在读取某些数据后的某个时间，再次读取以前读过的数据，却发现其读出的数据已经发生了改变、或某些记录已经被删除了。

幻读： 一个事务按相同的查询条件重新读取以前检索过的数据，却发现其他事务插入了满足其查询条件的新数据。



#### 一般数据库中的隔离级别



读未提交（READ UNCOMMITTED） ： 解决了 更新丢失 的问题
读提交 （READ COMMITTED） ： 解决了 脏读 的问题
可重复读 （REPEATABLE READ） ： 解决了 不可重复读 的问题
串行化 （SERIALIZABLE） ： 解决了 幻读 的问题

 

#### MySQL中的隔离级别



读未提交（READ UNCOMMITTED） ： 数据库有默认的隔离级别 ， 不存在更新丢失的问题 ，读未提交也不解决脏读的问题，所以不推荐使用 

读提交 （READ COMMITTED） ： 解决了 脏读 的问题

可重复读 （REPEATABLE READ） ： 解决了 不可重复读 的问题 ， 也解决了幻读的问题  ， 默认

串行化 （SERIALIZABLE） ：  可重复读 已经解决了 幻读的问题 ， 并且该隔离级别大大降低了并发性 ， 所以不推荐使用 



#### 常用命令

```sql

# 查询全局的 和 session 的隔离级别
select @@global.tx_isolation,@@tx_isolation;

#  设置 全局的 和 session 的隔离级别
set global transaction isolation level read committed;
set session transaction isolation level read committed; 

# 隔离级别的定义关键词
READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE
```



#### 演示



- 读未提交的隔离级别下 ， 读取其他事务未提交的数据
- 读提交的隔离级别下，读取其他事务提交的变更和删除
- 可重复读的隔离级别下 ， 验证多次读取数据的一致性



#### 实现原理介绍（不具体展开）

undo

redo

锁

readview





### 附录：补充内容



##### 测试的数据库连接命令

```sql

mysql  -h xxx -u jhh -pxxx db1 -A -c

```



##### 插入冲突的处理

```sql
insert 忽略冲突
 insert ignore into   tab  values () ;
insert 冲突覆盖
 replace into tab values () ;
 


```



##### 一些概念

```sql

DQL : Data Query Lang   Select 
DML : Data Maniplation Lang  Insert , Update , Delete
DDL : Data Definition Lang  Create  , Drop , Alter 


```

