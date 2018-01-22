---
layout: post
title:  "MySQL information schema表记录"
date:   2018-01-22 +0800
categories: SQL
tags: mysql
author: Jimmy Lee
---

* content
{:toc}


### 说明
MySQL5.7  
information schema的一些表说明记录  


### innodb_trx 
在innodb存储引擎,包含了当前正在执行的事物
```sql
mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> update blog set amt=111 where id=1;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```
看结果
```sql
mysql> select * from innodb_trx \G;
*************************** 1. row ***************************
                    trx_id: 17263
                 trx_state: RUNNING
               trx_started: 2018-01-22 14:26:00
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 3
       trx_mysql_thread_id: 11
                 trx_query: NULL
       trx_operation_state: NULL
         trx_tables_in_use: 0
         trx_tables_locked: 1
          trx_lock_structs: 2
     trx_lock_memory_bytes: 1136
           trx_rows_locked: 1
         trx_rows_modified: 1
   trx_concurrency_tickets: 0
       trx_isolation_level: REPEATABLE READ
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
1 row in set (0.00 sec)
```


### innodb_locks  
记录了一个事物正在等待另一个事物的记录
1. 在sessionA执行上面的update语句
2. 在sessionB执行另一条update语句
```sql
mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> update blog set amt=333 where id=1;
```
看innodb_trx的结果.
```sql
mysql> select * from innodb_trx \G;
*************************** 1. row ***************************
                    trx_id: 17264
                 trx_state: LOCK WAIT
               trx_started: 2018-01-22 14:27:45
     trx_requested_lock_id: 17264:154:3:2
          trx_wait_started: 2018-01-22 14:27:45
                trx_weight: 2
       trx_mysql_thread_id: 12
                 trx_query: update blog set amt=333 where id=1
       trx_operation_state: starting index read
         trx_tables_in_use: 1
         trx_tables_locked: 1
          trx_lock_structs: 2
     trx_lock_memory_bytes: 1136
           trx_rows_locked: 1
         trx_rows_modified: 0
   trx_concurrency_tickets: 0
       trx_isolation_level: REPEATABLE READ
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
*************************** 2. row ***************************
                    trx_id: 17263
                 trx_state: RUNNING
               trx_started: 2018-01-22 14:26:00
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 3
       trx_mysql_thread_id: 11
                 trx_query: NULL
       trx_operation_state: NULL
         trx_tables_in_use: 0
         trx_tables_locked: 1
          trx_lock_structs: 2
     trx_lock_memory_bytes: 1136
           trx_rows_locked: 1
         trx_rows_modified: 1
   trx_concurrency_tickets: 0
       trx_isolation_level: REPEATABLE READ
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
2 rows in set (0.00 sec)
```
看到有2条记录, 第2条记录(trx_id: 17263)和第一部分的结果一致, 那么第1条记录(trx_id: 17264)就是这次sessionB执行的update语句.而且状态是lock wait.      


然后看innodb_locks的结果. 可以看到锁模式(X),锁类型(record). lock_trx_id就是上面表的记录id.  
```sql
mysql> select * from innodb_locks \G;
*************************** 1. row ***************************
    lock_id: 17264:154:3:2
lock_trx_id: 17264
  lock_mode: X
  lock_type: RECORD
 lock_table: `jimmy_slave`.`blog`
 lock_index: PRIMARY
 lock_space: 154
  lock_page: 3
   lock_rec: 2
  lock_data: 1
*************************** 2. row ***************************
    lock_id: 17263:154:3:2
lock_trx_id: 17263
  lock_mode: X
  lock_type: RECORD
 lock_table: `jimmy_slave`.`blog`
 lock_index: PRIMARY
 lock_space: 154
  lock_page: 3
   lock_rec: 2
  lock_data: 1
2 rows in set, 1 warning (0.00 sec)
```


### INNODB_LOCK_WAITS    
这个表记录的是哪个事物在等哪个锁.  


看下面的数据.
```sql
mysql> select * from INNODB_LOCK_WAITS \G;
*************************** 1. row ***************************
requesting_trx_id: 17264
requested_lock_id: 17264:154:3:2
  blocking_trx_id: 17263
 blocking_lock_id: 17263:154:3:2
1 row in set, 1 warning (0.00 sec)
```
意思就是说事物(17264)在等待事物(17263)上的锁.  



### 引用
[mysql doc](https://dev.mysql.com/doc/refman/5.7/en/innodb-information-schema-transactions.html)
