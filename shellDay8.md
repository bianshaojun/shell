# shellNote

## day8

* linux标准文件描述符

| 文件描述符 | 缩写 | 描述 |
| -- | -- | -- |
| 0 | STDIN | 标准输入 |
| 1 | STDOUT | 标准输出 |
| 2 | STDERR | 标准错误 | 

* STDIN标准输入

	代表shell的标准输入,对于终端界面来说,标准输入是键盘;

	在使用输入重定向符号(<),linux会用重定向指定的文件来替换标准输入文件描述符;

* STDOUT标准输出

	代表shell的标准输出,在终端界面上,标准输出就是终端显示器;

	通过输出重定向符号(>),可以将显示到显示器的所有输出重定向到指定的重定向文件;

	也可以将数据追加到某个文件,>>符号来完成;

* STDERR标准错误

	特殊的STDERR文件描述符来处理错误消息;

	代表shell的标准错误输出,shell或shell中运行的程序和脚本出错时生成的错误消息都会发送到这个位置;

	默认情况下,STDOUT和STDERR指向同样的地方,也就是终端显示器;

	STDERR却并不会随着STDOUT重定向而发生改变;

* 重定向错误

	普通的重定向符号,只能重定向标准输出;

	STDERR文件描述符为2,可以将该文件描述符放在重定向符号前,来重定向错误;

	`ls -al badfile 2> test4`

* 重定向错误和数据

	如果需要重复定向错误和正常输出,需要用到两个重定向符号;

	在符号前面放上重定向数据所对应的文件描述符,然后指向用于保存数据的输出文件;

	`ls -al test test2 test3 badtest 2> test6 1> test7`

	* 1>
		
		重定向标准输出;

	* 2> 

		重定向标准错误;

	* &> 

		重定向标准输出和标准错误;

* 临时重定向

	在脚本中生成错误消息,可以将单独的一行输出重定向到STDERR;

	在重定向到文件描述符时,必须在文件描述符数字之前加一个&;

	`echo "This is an error message" >&2`

* 永久重定向

	exec命令可以在脚本执行期间重定向某个特定文件描述符;

	`exec 1>testout`

	`exec 2>testerr`

* 脚本中重定向输入

	exec命令可以将STDIN重定向到linux系统的文件中;

	`exec 0< testfile`

* 创建输出文件描述符

	脚本中不仅仅只有默认的0,1,2的文件描述符;

	最多可以有9个打开的文件描述符,其他6个3-8的文件描述符均可以用作输入或输出重定向;

	exec可以来给输出分配文件描述符;

	```
	exec 3>test3out

	echo "this is out in test3out" >&3
	```

	也可以追加到现有文件中

	`exec 3>>test3out`

* 恢复已重定向的文件描述符

	```
	exec 3>&1 //将3重定向到STDOUT

	exec 1>testOut //将1重定向到文件testOut

	exec 1>&3 //将1重定向到3,3目前指向STDOUT,1现在恢复重定向再次指向STDOUT
	```	

* 创建输入文件描述符

	在重定向到文件之前,先将STDIN文件描述符保存到另外一个文件描述符,然后读完之后再将STDIN恢复到原来的位置;

	```
	exec 6<&0 //先将0重定向输入到文件描述符6,此时6保存着STDIN的位置

	exec 0< testfile //将STDIN重定向到一个文件

	exec 0<&6 //将0重定向输入到6保存的STDIN
	```

* 创建读写文件描述符

	可以用一个文件描述符对同一个文件进行读写;

	```
	exec 3<> testfile //将文件描述符3分配给文件testfile进行文件读写

	read line <&3	//使用read命令读取文件的第一行

	echo "this is a test" >&3	//用echo将一行数据写入文件中
	
	```

* 关闭文件描述符

	默认在shell脚本退出时自动关闭打开的文件描述符;

	手动关闭文件描述符,将其重定向到特殊符号&-;

	`exec 3>&-`

* 列出打开的文件描述符--lsof

	`lsof -a -p $$ -d 0,1,2`

	* -p参数

		允许指定进程ID;

	* -d参数

		允许指定要显示的文件描述符编号;

	* $$

		特殊环境变量,当前PID;

	* -a参数

		可以对选项结果执行AND和运算;

	* 默认输出

| 列 | 描述 | 
| -- | ---- |
| COMMAND | 正在允许的命令名的前9个字符 |
| PID | 进程的PID |
| USER | 进程属主的登入名 |
| FD | 文件描述符号以及访问类型(r代表读,w代表写,u代表读写) |
| TYPE | 文件的类型(CHR代表字符型,BLK代表块型,DIR代表目录,REG代表常规文件) | 
| DEVICE | 设备的设备号(主设备号和从设备号) |
| SIZE | 如果有的话,表示文件的大小 | 
| NODE | 本地文件的节点号 | 
| NAME | 文件名 |

* 阻止命令输出

	将不想输出的内容重定向到/dev/null文件中;

	`ls -al > /dev/null`

	`ls -al badfile test16 2> /dev/null`

	也可以在输入重定向将/dev/null作为输入文件;

	`cat /dev/null >testfile`

* 创建临时文件

	linux使用/tmp目录来存放不需要永久保留的文件,系统在启动时会自动删除/tmp的所有文件;

	* 创建本地临时文件--mktemp

		mktemp会在本地目录创建一个文件,需要指定一个文件名模板;

		模板在文件名末尾加上6个X就可以了;

		`mktemp test.XXXXXX`

		mktemp会用6个字符来替换X来保证文件名是唯一的;

		在脚本中使用mktemp,需要将文件名保存到变量中,这样才能在脚本中使用;

		`tempfile=$(mktemp test.XXXXXX)`

	* 在/tmp目录创建临时文件

		-t选项会强制mktemp命令在系统的临时目录来创建该文件,mktemp命令会返回用来创建临时文件的全路径;

		`mktemp -t test.XXXXXX`

	* 创建临时目录

		-d选项会使mktemp命令来创建一个临时目录;

		`mktemp -d dir.XXXXXX`

* 记录消息

	将输出同时发送到显示器和日志文件;

	tee命令:

	该命令将从STDIN过来的数据同时发往两处一处是STDIN,另一处是tee命令行指定的文件名;

	`tee filename`

	```
	date | tee testfile

	who | tee testfile
	```

	如果想将数据追加到文件中,需要用-a选项;

	```
	date | tee -a testfile
	```

	```
	tempfile=test22file
	echo "This is the start of the test" | tee $tempfile
	echo "This is the second line of the test" | tee -a $tempfile
	echo "This is the end of the test" | tee -a $tempfile
	```
