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


			
