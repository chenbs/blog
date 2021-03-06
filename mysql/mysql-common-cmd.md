title: mysql 常见操作
date: 2016-07-28 11:44:00
tags:
- mysql

# mysql 常见操作

## 查看用户权限

	show grants;
	show grants for 'root'
	select * from mysql.user;
	select * from information_schema.user_privileges;
	
### 权限设置


	grant all privileges on database.table to 'user'@'host' identified by 'password';
	flush privileges;	
	
	mysql> CREATE USER 'migration'@'%' IDENTIFIED BY 'migration@20160810';
	mysql> GRANT ALL PRIVILEGES ON *.* TO 'migration'@'%';

## 查看表结构

	desc tablename;
	
## 设置只读

将MySQL设置为只读状态的命令：

	# mysql -uroot -p
	mysql> show global variables like "%read_only%";
	mysql> flush tables with read lock;
	mysql> set global read_only=1;
	mysql> show global variables like "%read_only%";


将MySQL从只读设置为读写状态的命令：

	mysql> unlock tables;
	mysql> set global read_only=0;
	
* 参考：[mysql只读模式的设置方法与实验](http://blog.csdn.net/yumushui/article/details/41645469)	

##  命令cheetsheet

	　　show databases; 显示数据库 
	　　create database name; 创建数据库 
	　　use databasename; 选择数据库 
	　　drop database name 直接删除数据库，不提醒 
	　　show tables; 显示表 
	　　describe tablename; 显示具体的表结构 
	　　select 中加上distinct去除重复字段 
	　　mysqladmin drop databasename 删除数据库前，有提示。 
	　　显示当前mysql版本和当前日期 
	　　select version(),current_date; 
	　　alter table t1 rename t2; 
	　　
### 修改mysql中root的密码： 
	　
	　　shell>mysql -h localhost -u root -p //登录 
	　　mysql> update user set password=password("xueok654123") where user='root'; 
	　　mysql> flush privileges //刷新数据库 
	　　mysql>use dbname; 打开数据库： 
	　　mysql>show databases; 显示所有数据库 
	　　mysql>show tables; 显示数据库mysql中所有的表：先use mysql;然后 
	　　mysql>describe user; 显示表mysql数据库中user表的列信息); 
	　　
### grant 

	　　创建用户firstdb(密码firstdb)和数据库，并赋予权限于firstdb数据库 
	　　mysql> create database firstdb; 
	　　mysql> grant all on firstdb.* to firstdb identified by 'firstdb' 
	　　
* grant 与on 之间是各种权限，例如:insert,select,update等 
* on 之后是数据库名和表名,第一个*表示所有的数据库，第二个*表示所有的表

### 备份数据库 

	备份
	mysqldump -h host -u root -p dbname >dbname_backup.sql 
	恢复数据库 
	mysqladmin -h myhost -u root -p create dbname
	mysqldump -h host -u root -p dbname < dbname_backup.sql 
	导出建表指令
	mysqladmin -u root -p -d databasename > a.sql 
	导出插入数据指令
	mysqladmin -u root -p -d databasename > a.sql 
	导出整个数据库 
	mysqldump -u 用户名 -p --default-character-set=latin1 数据库名 > 导出的文件名(数据库默认编码是latin1) 
	导出一个表
	mysqldump -u 用户名 -p 数据库名 表名> 导出的文件名
	导出一个数据库结构
	mysqldump -u wcnc -p -d -add-drop-table smgp_apps_wcnc >d:wcnc_db.sql 
	导入数据库
	1.
	mysql -u root -p 
	source wcnc_db.sql 
	2.
	mysqldump -u username -p dbname < filename.sql
	3.
	mysql -u username -p -D dbname < filename.sql
	
### 库操作 

	create database <数据库名>
	show databases
	drop database <数据库名> 
	use <数据库名> 
	select database(); 
	show tables; 
	
### 表操作

	create table <表名> ( <字段名1> <类型1> [,..<字段名n> <类型n>]);
	
	desc 表名，或者show columns from 表名 
	
	drop table <表名> 
	
	insert into <表名> [( <字段名1>[,..<字段名n > ])] values ( 值1 )[, ( 值n )]  
		INSERT INTO Websites (name, url, alexa, country) VALUES ('百度','https://www.baidu.com/','4','CN');
		
	
	select <字段1，字段2，...> from < 表名 > where < 表达式 > 
		select * from MyClass order by id limit 0,2; 
		
	delete from 表名 where 表达式 
		delete from MyClass where id=1; 
		
		
	update 表名 set 字段=新值,… where 条件 
		update MyClass set name='Mary' where id=1; 
		
	alter table 表名 add 字段 类型 其他; 
		alter table MyClass add passtest int(4) default '0'
		
	rename table 原表名 to 新表名; 
		rename table MyClass to YouClass; 
	
	CREATE INDEX index_name ON table_name (column_name,...)
		CREATE INDEX PIndex ON Persons (LastName)
			

### 字符问题

客户端查询显示乱码

	SHOW VARIABLES LIKE 'character%';
	SET NAMES 'utf8';			
	
或者：

	my.cnf
	default-character-set = utf8
	character_set_server = utf8
	service mysql restart

	
### select 顺序

1. FROM 子句, 组装来自不同`数据源`的数据

	FROM, ON, JOIN
	
1. WHERE 子句, 基于指定的条件对记录进行`筛选`
1. GROUP BY 子句, 将数据划分为`多个分组`
1. 使用`聚合`函数进行计算
1. 使用 HAVING 子句`筛选分组`
1. 处理SELECT列表
2. DISTINCT:将重复的行
1. 使用 ORDER BY 对结果集进行排序	
2. TOP

* [SQL语句中SELECT语句的执行顺序](http://database.51cto.com/art/201009/223936.htm)
* [SQL 中 SELECT 语句的执行顺序](http://www.cnblogs.com/ziyiFly/archive/2008/09/12/1289614.html)
	
	
### 触发器

查看：

    show triggers;
    
创建：

```
CREATE TRIGGER trigger_name trigger_time trigger_event
    ON tbl_name FOR EACH ROW trigger_stmt
```

创建一个触发器，名为：`updatename`

```
delimiter ||      //mysql 默认结束符号是分号，当你在写触发器或者存储过程时有分号出现，会中止转而执行  
drop trigger if exists updatename||    //删除同名的触发器，  
create trigger updatename after update on user for each row   //建立触发器，  
begin  
//old,new都是代表当前操作的记录行，你把它当成表名，也行;  
if new.name!=old.name then   //当表中用户名称发生变化时,执行  
update comment set comment.name=new.name where comment.u_id=old.id;  
end if;  
end||  
```

* 详见： [MySQL_TRIGGER学习](MySQL_TRIGGER学习.md)


### table status

    show table status;
    show table status like '%tablename%';
    show table status where name='tablename';


### 复制表

结构：

    create table a like users;         //复制表结构  
    create table b select * from users limit 0;   //复制表结构 
    show create table users\G;          //显示创表的sql 
    
    
结构和数据：

    create table c select * from users;      //复制表的sql  
    
    create table d select user_name,user_pass from users where id=1;  

	
	