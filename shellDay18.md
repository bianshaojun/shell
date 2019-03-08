# shellNote

## day18

### mysql简单介绍

* mysql安装

	`sudo aptitude install mysql-server`

	安装过程中,会要求你设置mysql的root账户密码;

* 连接mysql服务器

	首先可以通过安装过程中的root账户和你设置的密码来连接;

	`$mysql -u root -p`

	* -u选项

		指定登入账户名

	* -p选项

		提示输入登入用户输入密码

* mysql命令

	在mysql程序中,分号表明命令的结束;

	所以每个命令后面我们需要添加一个分号;

	如果不用分号,mysql会提示输入更多的数据;

	* status或\s

		查看服务器状态;

		```
		mysql> status
		--------------
		mysql  Ver 14.14 Distrib 5.5.62, for debian-linux-gnu (x86_64) using readline 6.3

		Connection id:		51
		Current database:	
		Current user:		root@localhost
		SSL:			Not in use
		Current pager:		stdout
		Using outfile:		''
		Using delimiter:	;
		Server version:		5.5.62-0ubuntu0.14.04.1 (Ubuntu)
		Protocol version:	10
		Connection:		Localhost via UNIX socket
		Server characterset:	latin1
		Db     characterset:	latin1
		Client characterset:	utf8
		Conn.  characterset:	utf8
		UNIX socket:		/var/run/mysqld/mysqld.sock
		Uptime:			21 hours 57 min 18 sec

		Threads: 1  Questions: 641  Slow queries: 0  Opens: 189  Flush tables: 1  Open tables: 41  Queries per second avg: 0.008
		--------------

		```

	* SHOW命令

		* SHOW DATABASES;

			显示mysql的数据库;

			```
			mysql> SHOW DATABASES;
			+--------------------+
			| Database           |
			+--------------------+
			| information_schema |
			| mysql              |
			| mytest             |
			| performance_schema |
			+--------------------+
			4 rows in set (0.00 sec)

			```

		* SHOW TABLES;

			显示连接的数据库的表单; 前提需要连接其中的数据库;
		
			```
			mysql> SHOW TABLES;
			+---------------------------+
			| Tables_in_mysql           |
			+---------------------------+
			| columns_priv              |
			| db                        |
			| event                     |
			| func                      |
			| general_log               |
			| help_category             |
			| help_keyword              |
			| help_relation             |
			| help_topic                |
			| host                      |
			| ndb_binlog_index          |
			| plugin                    |
			| proc                      |
			| procs_priv                |
			| proxies_priv              |
			| servers                   |
			| slow_log                  |
			| tables_priv               |
			| time_zone                 |
			| time_zone_leap_second     |
			| time_zone_name            |
			| time_zone_transition      |
			| time_zone_transition_type |
			| user                      |
			+---------------------------+
			24 rows in set (0.00 sec)

			```

	* USE命令

		用来连接到某个数据库;

		连接后就可以用SHOW TABLES;命令来显示数据库的表单;

		```
		mysql> USE mysql
		Reading table information for completion of table and column names
		You can turn off this feature to get a quicker startup with -A

		Database changed

		```

	* CREATE命令

		创建数据库;

		`CREATE DATABASE name;`
		
		```
		mysql> CREATE DATABASE mytest;
		Query OK, 1 row affected (0.02 sec)
		```

	* 创建用户账户

		一般可以使用root用户来完成各种操作;

		不过最好的方式还是给每个数据库有独立权限的用户账户;

		用GRANT命令来完成;

		```
		mysql> GRANT SELECT,INSERT,DELETE,UPDATE ON mytest.* TO test IDENTIFIED by 'test';
		Query OK, 0 rows affected (0.35 sec)
		```

		* SELECT: 表示查询权限;
		* INSERT: 表示插入新数据权限;
		* DELETE: 表示删除已有数据权限;
		* UPDATE: 表示更新已有数据权限;
		* ON mytest.*: 表明前面的权限作用于mytest这个数据库和表上;
		* TO test: 表明mytest数据库和表上的这些权限赋予给那个账号用户,这里账号名为test;
		* IDENTIFIED by: 对账号用户设定默认密码;

		GRANT命令: 如果用户账户不存在,他会创建;

		之后,就可以用新的用户来连接mytest数据库:

		```
		$ mysql mytest -u test –p
		Enter password:
		Welcome to the MySQL monitor. Commands end with ; or \g.
		Your MySQL connection id is 42
		Server version: 5.5.38-0ubuntu0.14.04.1 (Ubuntu)
		Copyright (c) 2000, 2014, Oracle and/or its affiliates. All rights reserved.
		Oracle is a registered trademark of Oracle Corporation and/or its
		affiliates. Other names may be trademarks of their respective
		owners.
		Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
		mysql>
		```

		这里第一个参数直接指明需要连接的数据库;

	* 创建数据表

		`CREATE TABLE`

		```
		$ mysql mytest -u root -p
		Enter password:
		mysql> CREATE TABLE employees (
		-> empid int not null,
		-> lastname varchar(30),
		-> firstname varchar(30),
		-> salary float,
		-> primary key (empid));
		Query OK, 0 rows affected (0.14 sec)
		mysql>
		```
		
		前面的例子,并没有给test账户CREATE的权限,所以这里使用root来创建表;

		在这里定义了四个数据字段:empid,lastname,firstname,salary;

		* empid数据字段

			除了定义为int整数值外,还限制了not null的数据约束,指明每条记录必须有一个empid值;

		* not null

			数据约束,表明该字段必须有值;

		* primary key

			定义了可以唯一表示每条记录的数据字段;

			表明每条记录的empid是唯一的;

		* 创建后,就可以使用`SHOW TABLES;`命令来查看表单;

			```
			mysql> show tables;
			+----------------+
			| Tables_in_test |
			+----------------+
			| employees |
			+----------------+
			1 row in set (0.00 sec)
			mysql>
			```

	* 插入数据

		INSERT命令格式如下:

		`INSERT INFO table VALUES (...)`

		每个数字字段的值需要用逗号分开;

		```
		mysql> INSERT INFO employees VALUES (1, 'Bian', 'Damon', 10000.00);
		Query OK, 1 row affected (0.35 sec)
		```

	* 删除数据

		DELETE命令格式如下:

		`DELETE FROM table`

		指定要从中删除的表,不过他会删除所有的记录;

		要想删除一条或指定的多条,需要使用WHERE子句来进行限制;

		`DELETE FROM employees WHERE empid = 2;`

		这条命令指定删除empid=2的记录;

	* 查询数据

		SELECT命令格式如下:

		`SELECT datafields FROM table`

		datafields是一个用逗号分开的数据字段名称列表,指明查询返回的数据字段,如果想提取所有,可以使用通配符*;
		
		```
		mysql> SELECT * FROM employees;
		+-------+----------+-----------+--------+
		| empid | lastname | firstname | salary |
		+-------+----------+-----------+--------+
		|     2 | Bian     | damon     |  10000 |
		+-------+----------+-----------+--------+
		1 row in set (0.00 sec)
		```

		另外还可以配合一个或多个修饰符定义数据库返回查询数据:

		* WHERE:显示符合特定条件的数据行子集;
		* ORDER BY: 以指定顺序显示数据行;
		* LIMIT: 只显示数据行的一个子集;

		```
		mysql> SELECT * FROM employees WHERE empid = 2;
		+-------+----------+-----------+--------+
		| empid | lastname | firstname | salary |
		+-------+----------+-----------+--------+
		|     2 | Bian     | damon     |  10000 |
		+-------+----------+-----------+--------+
		1 row in set (0.00 sec)
		```
* 脚本中使用数据库

	* mysql配置文件

		mysql程序使用$HOME/.my.cnf文件来读取特定的启动命令和设置;

		这里我们可以设置启动mysql会话的默认密码;

		```
		$ cat .my.cnf
		password = test
		$ chmod 400 .my.cnf
		$
		```

		这里用chmod来设置.my.cnf文件限制为只能本人浏览;

		之后,我们就可以不使用-p选项来登入mysql了:

		`mysql mytest -u test`

	* 向服务器发送命令

		* 发送单个命令

			对于mysql命令,可以使用-e选项,来指定命令的内容;

			```
			damon@ubuntu:~$ cat mtest 
			#!/bin/bash

			MYSQL=$(which mysql)

			$MYSQL mytest -u test -e 'select * from employees'
			damon@ubuntu:~$ ./mtest 
			+-------+----------+-----------+--------+
			| empid | lastname | firstname | salary |
			+-------+----------+-----------+--------+
			|     2 | Bian     | damon     |  10000 |
			+-------+----------+-----------+--------+
			damon@ubuntu:~$ 

			```

		* 发送多个命令

			可以使用重定向来完成;

			定义一个结束字符串,结束字符串指明了重定向数据的开始和结尾;

			*注意:结尾的EOF,必须是该行唯一的内容,并且这一行必须以EOF开头,不要进行缩进或空格等等;*

			```
			damon@ubuntu:~$ cat mtest 
			#!/bin/bash

			MYSQL=$(which mysql)

			$MYSQL mytest -u test <<EOF
			show tables;
			select * from employees where salary > 5000;
			EOF
			damon@ubuntu:~$ ./mtest 
			Tables_in_mytest
			employees
			empid	lastname	firstname	salary
			2	Bian	damon	10000
			damon@ubuntu:~$ 

			```

	* 格式化数据

		mysql标准输出,不太适合脚本提取数据;

		先将mysql的输出重定向到一个变量中,然后在对其中的数据进行自定义的操作;

		```
		damon@ubuntu:~$ cat mtest 
		#!/bin/bash

		MYSQL=$(which mysql)

		dbs=$($MYSQL mytest -u test -Bse 'show databases')
		for db in $dbs
		do
			echo $db
		done
		damon@ubuntu:~$ ./mtest 
		information_schema
		mytest

		```

		* -B选项

			指定mysql程序工作在批处理模式运行;

		* -s选项

			用于禁止输出列标题和格式化符号;

		* -X选项

			可以输出XML格式;

			```
			damon@ubuntu:~$ mysql mytest -u test -X -e 'select * from employees'
			<?xml version="1.0"?>

			<resultset statement="select * from employees
			" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
			  <row>
				<field name="empid">2</field>
				<field name="lastname">Bian</field>
				<field name="firstname">damon</field>
				<field name="salary">10000</field>
			  </row>
			</resultset>
			```
