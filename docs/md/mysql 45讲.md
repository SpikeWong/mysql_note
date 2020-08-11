# mysql 45讲

## 如何判断一个数据库是不是出问题了?

### 外部检测

- select 1

	- 方法: 定期执行 select 1
	- 优点: 说明 mysql 进程还在
	- 缺点: 不能证明 mysql 完全没有问题，比如并发查询数达到 innodb_thread_concurrency

- 查表判断

	- 方法: 新建 health_check 表，定期执行查询语句
	- 优点: 说明此时查询没有问题
	- 缺点: 不能说明 update 和 insert 没有问题，比如磁盘空间占满

- 更新判断

	- 方法: 1.新建 health_check 表，定期写入更新时间 mysql> update mysql.health_check set t_modified=now();                                             
 2.主从同步结构时: insert into mysql.health_check(id, t_modified) values (@@server_id, now()) on duplicate key update t_modified=now();
	- 优点: 数据库检测时 update, insert, select 和 delete 等都无问题
	- 缺点:  1.因为定时轮询的因素，所以在两次检测的间隔时间内数据库已经不可能，导致问题暴露迟滞。

### 内部检测

- 方法: 通过 mysql performance_schema 观察 redo log 和 bin log 写入情况的统计数据。     update setup_instruments set ENABLED='YES', Timed='YES' where name like '%wait/io/file/innodb/innodb_log_file%';
- 优点: 
- 缺点: 会有 10% 左右的性能损耗

## 误删数据后除了跑路，还能怎么办

*XMind - Trial Version*