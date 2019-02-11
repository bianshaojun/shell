# shellNote
# day7

* 命令行参数

	位置参数变量是标准的数字:\$0是程序名,\$1是第一个参数,\$是第二个参数,以此类推直到\$9,在往后就是\${10},\${11}....

* 读取脚本名

	\$0可以获得shell在命令行启动的脚本名,但是可能会把路径或./等等符号带入到\$0中;

	使用basename命令可以过滤多余的东西;

	`name=$(basename $0)`--就可以得到单纯的脚本名;

* 测试参数

	使用参数前一定要检查是否存在数据;

	```
	if [ -n "$1"]
	then
		echo have $1;
	else
		echo no $1;
	```

* 参数统计

	特殊变量$#,含有脚本运行时携带的命令行参数的个数;

	判断参数总数可以:

	`if [ $# -ne 2 ]`

	*注意*\${\$#}并不能表示最后一个参数,因为花括号里不识别\$,要使用\${!#};

	如果不带参数时,\$#值为0,\${!#}返回的是命令行的脚本名;

* \$*和\$@变量

	两者都可以存储所有的参数;

	* \$*变量会将所有参数当成单个参数;

	* \$@变量会单独处理每个参数;
	
	```
	$ cat test11.sh
	#!/bin/bash
	# testing $* and $@
	#
	echo
	echo "Using the \$* method: $*"
	echo
	echo "Using the \$@ method: $@"
	$
	$ ./test11.sh rich barbara katie jessica
	Using the $* method: rich barbara katie jessica
	Using the $@ method: rich barbara katie jessica
	$
	```

	```
	$ cat test12.sh
	#!/bin/bash
	# testing $* and $@
	#
	echo
	count=1
	#
	for param in "$*"
	do
	echo "\$* Parameter #$count = $param"
	count=$[ $count + 1 ]
	done
	#
	echo
	count=1
	#
	for param in "$@"
	do
	echo "\$@ Parameter #$count = $param"
	count=$[ $count + 1 ]
	done
	$
	$ ./test12.sh rich barbara katie jessica
	$* Parameter #1 = rich barbara katie jessica
	$@ Parameter #1 = rich
	$@ Parameter #2 = barbara
	$@ Parameter #3 = katie
	$@ Parameter #4 = jessica
	```

* 移动变量--shift命令

	shift会根据参数的相对位置来移动命令行参数;

	默认情况下,会将每个变量向左移动一个位置,比如变量3到变量2,变量2到变量1,变量1删除,变量0不会改变;

* 查找选项

	* 简单选项

		一般用case来判断参数是否为选项;

		```
		while [ -n "$1" ]
		do
		case "$1" in
		-a) echo "Found the -a option" ;;
		-b) echo "Found the -b option" ;;
		-c) echo "Found the -c option" ;;
		*) echo "$1 is not an option" ;;
		esac
		shift
		done
		```

		* 分离参数和选项

			标准方式是用特殊字符来分开,双破折号(--),shell用双破折号来表明选项列表结束,之后的当做命令行参数来处理;

			```
			while [ -n "$1" ]
			do
			case "$1" in
			-a) echo "Found the -a option" ;;
			-b) echo "Found the -b option";;
			-c) echo "Found the -c option" ;;
			--) shift
			break ;;
			*) echo "$1 is not an option";;
			esac
			shift
			done
			#
			count=1
			for param in $@
			do
			echo "Parameter #$count: $param"
			count=$[ $count + 1 ]
			done
			```

		* 处理带值的选项

			有些选项会带上一个参数值;

			```
			while [ -n "$1" ]
			do
			case "$1" in
			-a) echo "Found the -a option";;
			-b) param="$2"
			echo "Found the -b option, with parameter value $param"
			shift ;;
			-c) echo "Found the -c option";;
			--) shift
			break ;;
			*) echo "$1 is not an option";;
			esac
			shift
			done
			#
			count=1
			for param in "$@"
			do
			echo "Parameter #$count: $param"
			count=$[ $count + 1 ]
			done
			```

	* 使用getopt命令

		是一个专门处理命令行选项和参数的工具,可以识别命令行参数,在脚本中解析他们;

		`getopt optstring parameters`

		optstring定义了命令行有效的选项字母,还定义了哪些选项字母需要参数值;

		首先在optstring中列出需要的每个命令行选项字母,然后在每个需要参数值的选项字母后加一个冒号;

		在命令行中:

		```
		$ getopt ab:cd -a -b test1 -cd test2 test3
		-a -b test1 -c -d -- test2 test3
		```

		在脚本中:

		用getopt命令生成格式化后的版本来替换已有的命令行选项和参数;

		set命令的选项双破折号,他可以将命令行参数换成set命令的命令行值;

		该方式先将原始脚本的命令行传给getopt,之后将getopt命令输出传给set,就将格式化后的命令行参数替换了原始的命令行参数:

		`set -- $(getopt -q ab:cd "$@")`
		
		-q参数可以忽略错误消息;

		```
		set -- $(getopt -q ab:cd "$@")
		#
		echo
		while [ -n "$1" ]
		do
		case "$1" in
		-a) echo "Found the -a option" ;;
		-b) param="$2"
		echo "Found the -b option, with parameter value $param"
		shift ;;
		-c) echo "Found the -c option" ;;
		--) shift
		break ;;
		*) echo "$1 is not an option";;
		esac
		shift
		done
		#
		count=1
		for param in "$@"
		do
		echo "Parameter #$count: $param"
		count=$[ $count + 1 ]
		done
		```

		*注意*该命令只会将空格当做参数分隔符;

	* 高级getopts命令

		推荐使用该命令替代上面的各种方式;

		每次调用时,只处理命令行上检测的一个参数,处理完所有参数后,他会退出并返回一个大于0的退出状态码;

		`getopts optstring variable`

		optstring跟getopt一样,有效选项字母会列在optstring中,如果字母要求有个参数,就加一个冒号,要去掉错误消息,可以在optstring之前加一个冒号;

		getopts命令将当前参数保存到命令行中定义的variable中;

		getopts命令会用到两个环境变量,如果选项需要跟一个参数值,OPTARG环境变量会保存这个值;

		OPTIND环境变量保存了参数列表中正在处理的参数位置;

		```
		$ cat test20.sh
		#!/bin/bash
		# Processing options & parameters with getopts
		#
		echo
		while getopts :ab:cd opt
		do
		case "$opt" in
		a) echo "Found the -a option" ;;
		b) echo "Found the -b option, with value $OPTARG" ;;
		c) echo "Found the -c option" ;;
		d) echo "Found the -d option" ;;
		*) echo "Unknown option: $opt" ;;
		esac
		done
		#
		shift $[ $OPTIND - 1 ]
		#
		echo
		count=1
		for param in "$@"
		do
		echo "Parameter $count: $param"
		count=$[ $count + 1 ]
		done
		#
		$
		$ ./test20.sh -a -b test1 -d test2 test3 test4
		Found the -a option
		Found the -b option, with value test1
		Found the -d option
		Parameter 1: test2
		Parameter 2: test3
		Parameter 3: test4
		$
		```

* 选项标准化

| 选项 | 描述 |
| ---- | ---- |
| -a | 显示所有对象 |
| -c | 生成一个计数 | 
| -d | 指定一个目录 | 
| -e | 扩展一个对象 | 
| -f | 指定读入数据的文件 |
| -h | 显示命令的帮助信息 |
| -i | 忽略文本大小写 | 
| -l | 产生输出的长格式版本 | 
| -n | 使用非交互模式(批处理) | 
| -o | 将所有输出重定向到指定的输出文件 | 
| -q | 以安静模式运行 | 
| -r | 递归地处理目录和文件 |
| -s | 以安静模式运行 | 
| -v | 生成详细输出 | 
| -x | 排除某个对象 | 
| -y | 对所有问题回答yes |

* 获取用户输入

	使用read命令;

	read命令从标准输入或另一个文件描述符中接受输入,得到输入后read会将数据放进一个变量中;

	`read name`

	* -p参数

		允许直接在read命令行指定提示符;

		`read -p "Please enter your name: " name`

	* 多个变量 

		需要多个变量存数据,可以在命令后指定多个变量;

		`read -p "Enter your name: " first last`

	* 不指定变量

		read会将数据都放入环境变量REPLY中;

	* -t参数

		-t参数可以指定超时等待的秒数,超时后,会返回一个非零退出状态码;

	* -n参数

		-n参数可以指定输入字符数;

		`read -n1 -p "Do you want to continue [Y/N]? " answer`	

		-n1表示接受一个字符;

	* -s参数

		隐藏读取,用于密码输入,实际上数据会被显示,只是read命令将文本设置为背景色一样;

		`read -s -p "Enter your password: " pass`

	* 从文件中读取

		一般使用cat命令,将结果通过管道传给含有read命令的while命令;

		read每次会读取一行文本;

		```
		$ cat test28.sh
		#!/bin/bash
		# reading data from a file
		#
		count=1
		cat test | while read line
		do
		echo "Line $count: $line"
		count=$[ $count + 1]
		done
		echo "Finished processing the file"
		$
		$ cat test
		The quick brown dog jumps over the lazy fox.
		This is a test, this is only a test.
		O Romeo, Romeo! Wherefore art thou Romeo?
		$
		$ ./test28.sh
		Line 1: The quick brown dog jumps over the lazy fox.
		Line 2: This is a test, this is only a test.
		Line 3: O Romeo, Romeo! Wherefore art thou Romeo?
		Finished processing the file
		$
		```

