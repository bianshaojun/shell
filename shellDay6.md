# shellNote
## day6

* for命令

	```
	for var in list
	do
		commands
	done
	```

	list参数中,需要提供一系列值;

	每次迭代,var会包含list中的当前值,第一次迭代会使用第一个值,第二次会使用第二个值,以此类推,知道所有的值都过一遍;

	eg:

	```
	$ cat test1
	#!/bin/bash
	# basic for command
	for test in Alabama Alaska Arizona Arkansas California Colorado
	do
	echo The next state is $test
	done
	$ ./test1
	The next state is Alabama
	The next state is Alaska
	The next state is Arizona
	The next state is Arkansas
	The next state is California
	The next state is Colorado
	$
	```

	*注意*: 

	list中有单引号,需要特别处理,使用转义单引号或使用双引号来定义单引号的值;

	list中默认以空格来分割,如果有空格,需要使用双引号来定义含空格的值;

	可以从变量中读取值:

	```
	list="Alabama Alaska Arizona Arkansas Colorado"
	for state in $list
	do
	echo "Have you ever visited $state?"
	done
	```

	可以从文件中读取值:
	```
	file="states"
	for state in $(cat $file)
	do
	echo "Visit beautiful $state"
	done
	```

	* 更改字段分隔符

		环境变量IFS,叫做内部字段分隔符;

		默认情况下,空格,制表符,换行符,作为字段分隔符;

		临时修改IFS变量后,记得还原默认值:

		```
		IFS.OLD=$IFS
		IFS=$'\n'
		<在代码中使用新的IFS值>
		IFS=$IFS.OLD
		```

		也可以指定多个字段分隔符eg: `IFS=$'\n':;"`

	* 用通配符读取目录

		```
		for file in /home/rich/test/*
		do
		if [ -d "$file" ]
		then
		echo "$file is a directory"
		elif [ -f "$file" ]
		then
		echo "$file is a file"
		fi
		done
		```

* c语言风格for命令

	`for (( variable assignment ; condition ; iteration process ))`

	相比shell的标准for命令:
	
	变量赋值可以有空格;

	条件中的变量不以美元符开头;

	迭代过程的算式未用expr命令格式;

	```
	for (( a=1, b=10; a <= 10; a++, b-- ))
	do
	echo "$a - $b"
	done
	```

* while命令

	```
	while test command
	do 
		other commands
	done
	```

	测试命令返回退出状态码为0,就循环执行一遍do-done之间的命令;

	返回非0状态码时,就会停止循环;

	可以使用多个测试命令,但只判断最后一个测试命令的退出状态码,来决定是否结束循环;

* until命令

	```
	until test commands
	do
		other commands
	done
	```

	测试命令返回退出状态码为0,就会停止循环;

	返回非0状态码时,就会循环执行一遍do-done之间的命令;

	同样可以使用多个测试命令,但只判断最后一个测试命令的退出状态码,来决定是否结束循环;

* 控制循环

	* break命令

		break命令可以退出循环;

		`break n`命令,n可以指定跳出循环的层级;

	* continue命令

		continue命令可以提前中止本次循环,但不会终止整个循环;

		`continue n`命令,n可以指定要继续的循环层级;

* 处理循环的输出

	可以通过done命令之后添加管道或重定向;

	eg:

	```
	for (( a = 1; a < 10; a++ ))
	do
	echo "The number is $a"
	done > test23.txt
	```
	```
	for state in "North Dakota" Connecticut Illinois Alabama Tennessee
	do
	echo "$state is the next place to go"
	done | sort
	```
