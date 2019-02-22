# shellNote
## day13
### sed编辑器

sed编辑器区别与一般认识的编辑器,叫做流编辑器;

可以根据命令来处理数据流中的数据;

一次操作,会像如下执行:

	1. 一次从输入中读取一行数据;

	2. 根据sed编辑器命令匹配数据;

	3. 按照命令修改流中的数据;

	4. 将新的数据输出到STDOUT;

* sed命令格式

	`sed options script file`

* sed命令选项

| 选项 | 描述 |
| ---- | ---- |
| -e script | 在处理输入时,将script中指定的命令添加到已有的命令中 | 
| -f script | 在处理输入时,将file中指定的命令添加到已有的命令中 |
| -n | 不产生命令输出,使用print命令来完成输出 | 

* 在命令行定义编译器命令

	```
	$ echo "This is a test" | sed 's/test/big test/' //用echo输出需要编辑的数据流,通过管道应用到sed中
	This is a big test
	$
	```
	用echo输出需要编辑的数据流,通过管道应用到sed中,这个例子用到了s命令,将test替换成big test;

	```
	$ cat data.txt
	it is a fat dog
	it is a fat dog
	it is a fat dog
	$
	$ sed 's/dog/cat/' data.txt
	it is a fat cat
	it is a fat cat
	it is a fat cat
	$
	```

	文件作为数据流输入,sed命令只将修改后的数据输出到STDOUT,并没有修改原始文件;

* 在命令行使用多个编辑器命令

	要在sed命令行上执行多个命令,需要使用-e选项;

	```
	$ sed -e 's/fat/small/; s/dog/cat/' data.txt
	it is a small cat
	it is a small cat
	it is a small cat
	```

	两个命令都作用到文件的每行数据上,需要用分号隔离开,并且命令末尾和分号不能有空格;	

	如果不用分号,可以用bash shell里面的次提示符来分隔,只要输入第一个引号表示出sed脚本的起始,bash就会继续提醒你输入更多的命令,直到输入了表示结束的另一个单引号;

	```
	$ sed -e '
	> s/fat/small/
	> s/dog/cat/
	> ' data.txt
	it is a small cat
	it is a small cat
	it is a small cat
	$
	```

* 从文件中读取编辑器命令

	如果有大量命令,可以将其放进单独的文件中,然后使用-f选项来指定文件;

	```
	$ cat script.sed
	s/fat/small/
	s/dog/cat/
	$ sed -f script.sed data.txt 
	it is a small cat
	it is a small cat
	it is a small cat
	```
* sed编辑器-s命令

	默认情况,替换命令只替换每行中出现的第一处;

	添加替换标记能够替换一行中不同地方的文本;

	`s/pattern/replacement/flags`

	* 替换标记

		* 数字,表明新文本将替换第几处模式匹配的地方;

			```
			$ cat data.txt
			This is a test of the test script.
			$ sed 's/test/game/2' data.txt
			This is a test of the game script.
			```

		2.g,表明新文本将会替换所有匹配的文本;

			```
			$ cat data.txt
			This is a test of the test script.
			$ sed 's/test/game/g' data.txt
			This is a game of the game script.
			```

		3.p,表明原先行的内容要打印出来;

			p标记,一般跟-n选项一起使用;

			-n选项会禁止sed编辑器输出;

			```
			$ cat data.txt
			This is a test line.
			This is a another line;
			$ sed -n 's/test/game/p' data.txt
			This is a game line.
			```

		4.w file,将替换的结果写到文件中;
			
			```
			$ cat data.txt
			This is a test line.
			This is a another line;
			$ sed 's/test/game/w test.txt' data.txt
			This is a game line.
			This is a another line;
			$ cat test.txt
			This is a game line;
			```

	* 替换字符

		有时在文本字符串有一些不方便在替换模式中使用的字符,比如正斜线/;

		这个时候需要用反斜杠来进行转义\;

		`sed 's/\/bin\/bash/\/bin\/csh/' /etc/passwd`

		另外sed编辑器还允许使用其他字符来作为替换命令分隔符,比如感叹号;

		`sed 's!/bin/bash!/bin/csh!' /etc/passwd`

* 使用地址

	默认情况下,sed编辑器的命令是针对文本数据的所有行;

	需要指定行,可以使用行寻址;

	1,以数字形式表示行区间;

	2,用文本模式来过率出行;

	```
	两种形式
	[address]command
	or
	address {
		command1
		command2
		command3
	}
	```
	
	* 数字方式行寻址

		sed编辑器将文本流的第一行编号为1,依次排序,最后一行可以使用特殊地址(\$);

		命令中指定,可以是单行号:

		```
		$ cat data.txt
		it is a fat dog
		it is a fat dog
		it is a fat dog
		$ sed '2s/dog/cat/' data.txt
		it is a fat dog
		it is a fat cat
		it is a fat dog

		```

		可以使用行区间,使用起始行号+逗号+结尾行号:
		
		```
		$ cat data.txt
		it is a fat dog
		it is a fat dog
		it is a fat dog
		$ sed '2,$s/dog/cat/' data.txt
		it is a fat dog
		it is a fat cat
		it is a fat cat

		```

	* 使用文本模式过滤指定行

		形式如:`/pattern/command`

		需要使用正斜线将指定pattern封起来;

		```
		$ cat data.txt
		it is a fat dog
		it is a fat cat
		it is a fat fish
		$ sed '/fish/s/fat/small/' data.txt
		it is a fat dog
		it is a fat cat
		it is a small fish
		```

	* 命令组合

		需要在单行执行多个命令,可以用花括号将多条命令组合在一起;

		```
		$ cat data.txt
		it is a fat dog
		it is a fat dog
		it is a fat dog
		$ sed '2,${
		> s/dog/cat/
		> s/fat/small/
		> }' data.txt
		it is a fat dog
		it is a small cat
		it is a small cat

		```

* 删除行-d命令

	d命令会删除匹配指定寻址模式的所有行;

	注意:如果没写地址寻址,将删除所有行;

	```
	$ cat data.txt
	This is line number 1.
	This is line number 2.
	This is line number 3.
	This is line number 4.
	$
	$ sed '3d' data.txt
	This is line number 1.
	This is line number 2.
	This is line number 4.
	$
	$ sed '2,3d' data.txt
	This is line number 1.
	This is line number 4.
	$
	$ sed '3,$d' data.txt
	This is line number 1.
	This is line number 2.
	$
	$ sed '/number 1/d' data.txt
	This is line number 2.
	This is line number 3.
	This is line number 4.
	$
	```
	
	也可以使用两个文本模式来删除某个行区间的行;

	```
	$ sed '/1/,/3/d' data.txt
	This is line number 4.
	$
	```

	不过这样使用,需要注意,在匹配了第一个模式后,删除将开启,如果没有匹配到第二个模式,将删除第一处匹配直到数据的结尾;

* 插入和附加-i命令和a命令

	插入命令i,会在指定行前增加一个新行;

	附加命令a,会在指定行后增加一个新行;

	格式如下:

	```
	sed '[address]command\new line'
	```

	例子:
	
	```
	$ cat data.txt
	This is line number 1.
	This is line number 2.
	This is line number 3.
	This is line number 4.
	$ sed '3i\
	> This is an inserted line.' data.txt
	This is line number 1.
	This is line number 2.
	This is an inserted line.
	This is line number 3.
	This is line number 4.
	$
	$ sed '3a\
	> This is an appended line.' data.txt
	This is line number 1.
	This is line number 2.
	This is line number 3.
	This is an appended line.
	This is line number 4.
	$
	```	

* 修改行-c命令

	修改命令可以修改数据流的整行文本的内容;

	```	
	$ cat data.txt
	This is line number 1.
	This is line number 2.
	This is line number 3.
	This is line number 4.
	$ sed '3c\
	> This is a changed line of text.' data.txt
	This is line number 1.
	This is line number 2.
	This is a changed line of text.
	This is line number 4.
	$
	$ sed '/number 3/c\
	> This is a changed line of text.' data.txt
	This is line number 1.
	This is line number 2.
	This is a changed line of text.
	This is line number 4.
	$
	```

* 转换命令-y命令

	转换命令可以处理单个字符;

	`[address]y/inchars/outchars`

	转换命令会对inchars和outchars的值进行一一对应;

	inchars第一个字符对应转换为outchars的第一个字符,以此类推;

	```
	$ echo "This 1 is a test of 1 2 7 try" | sed 'y/123/456/'
	This 4 is a test of 4 5 7 try
	```

* 打印信息

	* 打印行信息-p命令

		p命令可以打印sed输出中的一行;

		一般与-n选项一起使用;

		```
		$ cat data.txt
		This is line number 1.
		This is line number 2.
		This is line number 3.
		This is line number 4.
		$ sed -n '2,3p' data.txt
		This is line number 2.
		This is line number 3.
		```

	* 打印行号-=命令

		=命令会打印行在数据流的当前行号;

		行号由数据流中的换行符决定;

		```
		$ cat data.txt
		This is line number 1.
		This is line number 2.
		This is line number 3.
		This is line number 4.
		$ sed '=' data.txt
		1	
		This is line number 1.
		2
		This is line number 2.
		3
		This is line number 3.
		4
		This is line number 4.
		```	

	* 打印不可打印的ASCII字符-l命令

		l命令会打印数据流中的文本和不可打印的ASCII字符,比如制表符,会输出为\t;

		```
		$ cat data.txt
		This	line	contains	tabs
		$ sed -n 'l' data.txt
		This\tline\tcontains\ttabs$
		```
		
		输出的\t表示制表符,$表示换行符;

* 处理文件

	* 写入文件-w命令

		w命令用来向文件写入行;

		`[address]w filename`

		filename可以使用相对路径或绝对路径;

		```
		$ sed '1,2w test.txt' data.txt
		This is line number 1.
		This is line number 2.
		This is line number 3.
		This is line number 4.
		$
		$ cat test.txt
		This is line number 1.
		This is line number 2.
		$
		```		

	* 从文件读取数据-r命令

		r命令用来读取文件中的数据插入到数据流中;

		`[address]r filename`

		```
		$ cat test.txt
		added a line
		added two lines
		$ cat data.txt
		This is a line .
		This is a line .
		This is a line .
		$ sed '2r test.txt' data.txt
		This is a line .
		This is a line .
		added a line
		added two lines
		This is a line .
		```


