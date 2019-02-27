# shellNote
## day15
### gawk程序编辑器

在gawk跟sed一样,可以进程流编辑,相比来说更加强大,他提供一种类编程的环境来修改和重组文件的数据;

命令格式:

`gawk options program file`

gawk选项:

| 选项 | 描述 |
| ---- | ---- |
| -F fs | 指定行中划分数据字段的字段分隔符 | 
| -f file | 从指定的文件中读取程序 |
| -v var=value | 定义gawk程序中的一个变量及其默认值 |
| -mf N | 指定要处理的数据文件中的最大字段数 | 
| -mr N | 指定数据文件中的最大数据行数 |
| -W keyword | 指定gawk的兼容模式或警告等级 | 

* 命令行读取程序脚本

	由于gawk命令行假定脚本是单个文本字符串,所以还需要将脚本放到单引号中;

	`$ gawk '{print "Hello World!"}'`	

	运行时,不会输出任何信息,由于没有给gawk程序输入数据流,

	这时gawk会等待STDIN接受数据,当输入一行并按下回车键,gawk程序会对该数据运行一遍程序脚本,

	这个例子中,就将想STDOUT输出Hello World!

	结束这个例子程序,需要在STDIN中输入EOF,组合键Ctrl-D可以产出EOF字符;

* 使用数据字段变量

	gawk会自动给一行中的数据元素分配一个变量,默认情况下:

	* $0代表整个文本行;
	* $1代表文本行中的第一个数据字段;
	* $2代表文本行中的第二个数据字段;
	* $n代表文本行中的第n个数据字段;

	gawk默认的字段分隔符是任意的空白符(空格或制表符);

	可以通过-F来指定其他分隔符;

	```
	$ gawk -F: '{print $1}' /etc/passed
	root
	bin
	daemon
	adm
	......
	```

* 脚本中使用多个命令

	gawk可以使用多个命令组合成一个正常的程序;

	只需在命令之间放个分号即可;

	```
	$ echo "My name is Rich" | gawk '{$4="Damon"; print $0}'
	My name is Damon
	$
	```

* 从文件中读取程序

	使用-f选项来指定gawk的脚本文件;

	在文件中指定多条命令的话,可以将没条命令放一行,这样就需要用分号来分隔;

	```
	$ cat script.gawk
	{
	text = "'s home directory is "
	print $1 text $6
	}
	$ gawk -F: -f script.gawk /etc/passwd
	root's home directory is /root
	bin's home directory is /bin
	......
	```

* 处理数据前运行脚本

	gawk可以在处理数据脚本执行前,运行一段脚本;

	BEGIN关键字来定义这段脚本;

	`gawk 'BEGIN {programs}'`

	```
	$ gawk 'BEGIN {print "Hello World"}'
	Hello World
	$
	```
	BEGIN脚本中,有一个特殊变量FS,可以用来代替-F选项来定义字段分隔符;

* 处理数据后运行脚本

	gawk可以在处理数据脚本执行后,运动一段脚本;

	END关键字来定义这段脚本;

	一段完整的gawk格式:

	```
	$ gawk 'BEGIN {program1s}
	> {program2s}
	> END {program3s}' dataInput.txt
	```

* 变量

	* 内建变量

		* 字段和记录分隔符变量

			数据字段变量是符号\$和字段的位置值来引用的;

			数据字段是由字段分隔符来划定的;

			默认情况下字段分隔符是空白符;

			* FILELDWIDTHS: 由空格分隔的一列数字,定义了每个数据字段确切宽度;

				可以不依靠字段分隔符来读取记录;

				一旦FILELDWIDTHS变量被设置,FS变量将被忽略;
					
				```
				$ cat data
				1005.3247596.37
				$ gawk 'BEGIN{FILELDWIDTHS="3 5 2 5"} {print $1,$2,$3,$4}' data
				100 5.324 75 96.37
				```				

			* FS: 输入字段分隔符;
			* OFS: 输出字段分隔符;

				默认情况下,OFS设成一个空格;

				`print $1,$2,$3`

				会输出为:
				
				`field1 field2 field3`

				```
				$ cat data
				data1,data2,data3
				$ gawk 'BEGIN{FS=","; OFS="-"} {print $1,$2,$3}' data
				data1-data2-data3
				```
			* RS: 输入记录分隔符;
			* ORS: 输出记录分隔符;
	
				RS和ORS定义了如何处理数据流的字段;

				默认情况下,RS和ORS设为换行符;

				```
				$ cat data
				Damon BIAN
				WUHAN HUBEI
				tel:123456

				Tony WANG
				RIZHAO SHANDONG
				tel:234567

				Yellow HUANG
				MACHENG HUBEI
				tel:345678
				$ gawk 'BEGIN{FS="\n"; RS=""} {print $1,$3}' data
				Damon BIAN tel:123456
				Tony WANG tel:234567
				Yellow HUANG tel:345678
				$
				```

		* 数据变量

			* ARGC: 当前命令行参数个数;
			
			* ARGIND: 当前文件在ARGV中的位置;
	
			* ARGV: 包含命令行参数的数组;

				ARGC和ARGV变量可以获得命令行参数的总数以及他们的值;

				```
				$ gawk 'BEGIN{print ARGC,ARGV[1]}' data
				2 data
				```

				gawk不会将程序脚本当做命令行参数,这里面包含gawk命令和data参数两个;

				ARGV数字从0开始;

			* CONVFMT: 数字的转换格式(参见printf语句),默认值为%.6 g
		
			* ENVIRON: 当前shell环境变量及其值组成的关联数组;

				使用ENVIRON来提取shell的环境变量,用文本来作为数组的索引值;

				```
				$ gawk '
				> BEGIN { 
				> print ENVIRON["HOME"]
				> print ENVIRON["PATH"]
				> } '
				/home/damon
				/usr/local/bin:/bin:......
				$
				```

			* ERRNO: 当读取或关闭输入文件发生错误时的系统错误号;

			* FILENAME: 用作gawk输入数据的数据文件的文件名;

			* IGNORECASE: 设成非零值时,忽略gawk命令中出现的字符串的字符大小写;

			* NF: 数据文件中的字段总数;
		
				可以使用其方便的指定记录的最后一个数据字段;

				```
				$ gawk 'BEGIN{FS=":"; OFS=":"} {print $1,$NF}' /etc/passwd
				damon:/bin/bash
				rich:/bin/bash
				......
				$
				```	
	
			* NR: 已处理的输入记录数;

			* FNR: 当前数据文件中的数据行数;

				NR和FNR在处理一个文件时,其实是一样的;

				当处理两个文件或以上时,FNR的值会在处理新文件时重置,NR的值则会继续计数;

				```
				$ cat data
				data11 data12 data13
				data21 data22 data23
				data31 data32 data33
				$ gawk '
				> {print $1, "FNR="FNR, "NR="NR}
				> ' data data
				data11 FNR=1 NR=1
				data21 FNR=2 NR=2
				data31 FNR=3 NR=3
				data11 FNR=1 NR=4
				data21 FNR=2 NR=5
				data31 FNR=3 NR=6
				$							
				```	

			* OFMT: 数字的输出格式,默认值为%.6 g

			* RLENGTH: 由match函数所匹配的子字符串的长度;

			* RSTART: 由match函数所匹配的子字符串的起始位置;

	* 自定义变量

		gawk可以自己定义表里,自定的表里可以是任意的字母,数字,下划线,但不能数字开头,并且区分大小写;

		* 脚本中赋值

			跟shell中赋值类似,直接使用赋值语句;

			```
			$ gawk 'BEGIN{x=4; x=x * 2 + 3; print x}'
			11
			$
			```

			gawk可以使用标准算数操作符,包括求余符号(%),幂运算符号(^或**);

		* 在命令行上赋值

			可以在gawk命令行来给程序的变量赋值;

			```
			$ cat data
			data11 data12 data13
			data21 data22 data23
			data31 data32 data33
			$ cat script
			{print $n}
			$ gawk -f script n=2 data
			data12
			data22
			data23
			```

			命令行参数定义的变量值,在BEGIN部分不可以使用;

			需要在BEGIN使用的话,需要在gawk命令加上-v选项,-v选项要放在脚本之前;

			```
			$ cat script
			BEGIN{print n}
			{print $n}
			$ gawk -v n=2 -f script data
			2
			data11 data12 data13
			data21 data22 data23
			data31 data32 data33
			$			
			```

* 数组

	gawk使用的是关联数组,他的索引值可以是任意的文本字符串,类似于字典一样;

	* 定义数组变量

		格式: `var[index] = element`
		
		var是变量名,index是索引值,element是数据元素值;

		```
		$ gawk 'BEGIN{
		> var["A"] = "apple"
		> var["B"] = "banana"
		> print var["A"]
		> }'
		apple
		$
		```

	* 遍历数组

		遍历数组,使用for语句的一种特殊形式;

		```
		for (var in array)
		{
			statements
		}
		```

		```
		$ gawk 'BEGIN{
		> var["a"] = 1
		> var["g"] = 2
		> var["m"] = 3
		> var["u"] = 4
		> for (test in var)
		> {
		> print "Index:",test," - Value:",var[test]
		> }
		> }'
		Index: u - Value: 4
		Index: m - Value: 3
		Index: a - Value: 1
		Index: g - Value: 2
		$
		```	

		*注意:关联数组,在遍历时,没有特定的顺序,只能保证索引值和元素数据是对应的;*

	* 删除数组变量

		`delete array[index]`

		```
		$ gawk 'BEGIN{
		> var["a"] = 1
		> var["g"] = 2
		> for (test in var)
		> {
		> print "Index:",test," - Value:",var[test]
		> }
		> delete var["g"]
		> print "---"
		> for (test in var)
		> print "Index:",test," - Value:",var[test]
		> }'
		Index: a - Value: 1
		Index: g - Value: 2
		---
		Index: a - Value: 1
		$
		```

* 模式

	* 正则表达式

		gawk在使用正则表达式时,需要出现在他要控制的程序脚本的左花括号前;

		```
		$ cat data
		1122 3344
		2233 4455
		$ gawk '/11/{print $1}' data
		1122
		```

	* 匹配操作符

		匹配操作符是波浪线(~)	,可以指定匹配操作符,数据字段变量以及要匹配的正则表达式;

		`$1 ~ /^data/`

		$1变量代表记录中的第一个字段,这个表达式会过滤第一个字段以data开头的文本的记录;

		```
		$ gawk -F: '$1 ~ /damon/{print $1,$NF}' /etc/passwd
		damon /bin/bash
		$
		```

		另外可以使用!符号来排除正则表达式的匹配

		`$1 !~ /expression/`

		```
		$ gawk -F: '$1 !~ /damon/{print $1,$NF}' /etc/passwd
		root /bin/bash
		bin /bin/sh
		......
		```

	* 数学表达式

		可以在匹配模式中使用数学表达式来过滤文本;

		可以使用的数学比较表达式:
	
		* x == y
		* x <= y
		* x < y
		* x >= y
		* x > y
		
		```
		$ gawk -F: '$4 == 0 {print $1}' /etc/passwd
		root
		sync
		shutdown
		......
		```

* 结构化命令

	* if语句

		格式:
	
		```
		if (condition)
			statement1

		if (condition)  
		{
			statements
		}
		else
		{
			statements
		}
		```

	* while语句

		格式:

		```
		while (condition)
		{
			statements 
		}
		```

		gawk同样支持break和continue语句;

	* do-while语句

		格式:

		```
		do
		{
			statements
		} while (condition)
		```

	* for语句

		格式:

		```
		for( variable assignment; condition ; iteration process) 
		{
			statements
		}
		```
* 格式化打印-printf

	printf命令格式:

	`printf "format string", var1, var2 ...`

	跟c语言编程用法一样;

* 数学函数

| 函数 | 描述 |
| ---- | ---- |
| atan2(x,y) | x/y的反正切,x和y以弧度为单位 |
| cos(x) | x的余弦,x为弧度单位 |
| exp(x) | x的指数函数 |
| int(x) | x的整数部分,取靠近零一侧的值 |
| log(x) | x的自然对数 |
| rand() | 0-1之间的随机浮点值 |
| sin(x) | x的正弦,x以弧度单位 |
| sqrt(x) | x的平方根 | 
| srand(x) | 为计算随机数指定一个种子值 |

* 位操作函数

| 函数 | 描述 |
| ---- | ---- |
| and(v1,v2) | 执行v1和v2的按位与运算 |
| compl(val) | 执行val的补运算 |
| lshift(val, count) | 将值val左移count位 |
| or(v1, v2) | 执行v1和v2的按位或运算 |
| rshift(val,count) | 将值val右移count位 |
| xor(v1,v2) | 执行v1和v2的按位异或运算 | 

* 字符串函数

| 函数 | 描述 |
| --- | --- |
| asort(s [,d]) | 将数组s按数据元素值排序,索引值会被连续的数字代替,<br>如果指定了d,排序后的数组会存放在数组d中 |
| asorti(s [,d]) | 将数组按索引值排序,生成的数组会将索引值作为数据元素值,<br>用连续数组索引来表明排序顺序,同样d可以存放新数组 |
| gensub(r, s, h [,t]) | 查找变量$0或目标字符串t（如果提供了的话）来匹配正则表达式r。<br>如果h是一个以g或G开头的字符串，就用s替换掉匹配的文本。<br>如果h是一个数字，它表示要替换掉第h处r匹配的地方 |
| gsub(r, s [,t]) | 查找变量$0或目标字符串t（如果提供了的话）来匹配正则表达式r。如果找到了，就全部替换成字符串s |
| index(s, t) | 返回字符串t在字符串s中的索引值，如果没找到的话返回0 |
| length([s]) | 返回字符串s的长度；如果没有指定的话，返回$0的长度 |
| match(s, r [,a]) | 返回字符串s中正则表达式r出现位置的索引。如果指定了数组a，它会存储s中匹配正则表达式的那部分|
| split(s, a [,r]) | 将s用 FS 字符或正则表达式r（如果指定了的话）分开放到数组a中。返回字段的总数 |
| sprintf(format,variables) | 用提供的format和variables返回一个类似于printf输出的字符串 |
| sub(r, s [,t]) | 在变量$0或目标字符串t中查找正则表达式r的匹配。如果找到了，就用字符串s替换掉第一处匹配 |
| substr(s, i [,n]) | 返回s中从索引值i开始的n个字符组成的子字符串。如果未提供n，则返回s剩下的部分 |
| tolower(s) | 将s中的所有字符转换成小写 |
| toupper(s) | 将s中的所有字符转换成大写 |

* 时间函数

| 函数 | 描述 |
| ---- | ---- |
| mktime(datespec) | 将一个按YYYY MM DD HH MM SS [DST]格式指定的日期转换成时间戳值 |
| strftime(format[,timestamp]) | 将当前时间的时间戳或timestamp（如果提供了的话）转化格式化日期（采用shell函数date()的格式）|
| systime( ) | 返回当前时间的时间戳 |

```
$ gawk 'BEGIN{
> date = systime()
> day = strftime("%A, %B %d, %Y", date)
> print day
> }'
Friday, December 26, 2014
$
```

* 自定义函数

	格式:

	```
	function name([vars])
	{
		statements
	}
	```	

	定义函数时,必须在出现的所有代码快之前(包含BEGIN代码块)

	使用函数库文件,可以在gawk命令时,使用-f选项来指定函数库文件;

	`gawk -f functionLIB -f script datafile`
