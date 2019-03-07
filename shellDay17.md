# shellNote

## day17

* xargs命令

	可以用来构建执行来自标准输入或管道输入的命令;

	* 选项-d

		指定分隔符

	* 选项-n

		指定每次传递参数个数;

	例子:

	```
	COMMAND_1="ps -u $USER_ACCOUNT --no-heading"	
	\\USER_ACCOUNT表示用户账户的名字,--no-heading去掉信息头,该命令获取用户正在运行的进程信息;
	
	gawk '{print $1}' 
	\\输出上一条命令的第一个字段,也就是该用户的进程pid;
	
	COMMAND_2="xargs -d \\n /usr/bin/sudo /usr/bin/kill -9" 
	\\由于gawk输出的是\n分隔的参数,所以指定分隔符为\n,后一条命令为终止进程;

	\\完整命令:
	COMMAND_1 | gawk 'print $1' | COMMAND_2
	```

	可以把xargs理解为协助其他命令的中间角色,将其从STDIN读取的内容作为其他命令的参数;

	默认命令为echo,默认分隔符为空格;

	```
	$ cat data
	a b c
	d e f g
	h i j
	$ cat data | xargs
	a b c e f g h i j
	$
	$ cat data | xargs -n 3 //-n 指定参数个数
	a b c
	d e f
	g h i
	j
	$ echo "a*b*c*d*e" | xargs -d * -n 4 //-d 指定分隔符
	a b c d 
	d e 
	```

	* -I或-i选项

		将STDIN的每一行赋值给{},然后将{}作为参数传递给其他命令;

		```
		$ cat data
		a b c d 
		1 2 3 4 
		hello
		world
		$ cat data | xargs -I {} echo {}
		a b c d
		1 2 3 4
		hello
		world
		# cat | data | xargs echo
		a b c d 1 2 3 4 hello world
		# cat data | xargs -i echo {}
		a b c d
		1 2 3 4
		hello
		world
		$	
		```

	* -0选项

		一般是与find命令配合使用时使用-0选项;

		find命令查找文件时,会有文件名中带空格的文件,这样直接传递给xargs,会将一个文件当做两个参数来处理,肯定是不对的;

		那么这个时候,-0选项,指定null作为分隔符;

		find的-print0选项,指定输出以null作为分隔符;

		`find . -type f -name "*.txt" -print0 | xargs -0 rm -f`
