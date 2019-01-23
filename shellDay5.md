# shellNote
## day5

* if-then

	```
	if command
	then
		conmmands
	fi
	```

	if后面的命令,退出状态码是0,表示运行成功,然后then部分的命令被执行;

* if-then-else

	```
	if command
	then
		commands
	else
		commands
	fi
	```

	if中的命令返回退出状态码0时,then中的命令被执行;

	if中的命令返回退出状态码非0时,else中的命令被执行;

* 嵌套if

	```
	if command1
	then
		commands
	elif command2
	then
		commands
	fi

	```
	elif相当于在else if;

* test命令

	提供在if-then中测试不同条件,条件成立,test退出返回0;

	`test condition`

	condition是test要测试的一系列参数和值;

	```
	if test condition
	then
		commands
	fi
	```

	另外一种条件测试方式,无需声明test命令,利用方括号来进行测试条件;

	```
	if [ condition ]
	then
		commands
	fi
	```

	*注意*: 第一个方括号之后和第二个方括号之前必须加上一个空格;

	test命令可以判断三类条件:数值比较,字符串比较,文件比较;

* 数值比较

	数值条件测试可以用于数值和变量上;

	不支持浮点型比较;

| 比较 | 描述 | 
| ---- | ---- |
| n1 -eq n2 | 检查n1是否与n2相等 |
| n1 -ge n2 | 检查n1是否大于或等于n2 | 
| n1 -gt n2 | 检查n1是否大于n2 | 
| n1 -le n2 | 检查n1是否小于或等于n2 | 
| n1 -lt n2 | 检查n1是否小于n2 | 
| n1 -ne n2 | 检查n1是否不等于n2 | 
	
* 字符串比较

| 比较 | 描述 | 
| ---- | ---- |
| str1 = str2 | 检查str1是否和str2相同 |
| str1 != str2 | 检查str1是否和str2不同 |
| str1 < str2 |　检查str1是否比str2小(根据ASCII顺序比较) |
| str1 > str2 | 检查str1是否比str2大 |
| -n str1 | 检查str1的长度是否非0 |
| -z str1 | 检查str1的长度是否为0 | 

* 文件比较

| 比较 | 描述 | 
| ---- | ---- |
| -d file | 检查file是否存在并是一个目录 |
| -e file | 检查file是否存在 | 
| -f file | 检查file是否存在并是一个文件 |
| -r file | 检查file是否存在并可读 | 
| -s file | 检查file是否存在并非空 | 
| -w file | 检查file是否存在并可写 | 
| -x file | 检查file是否存在并可执行 |
| -O file | 检查file是否存在并属当前用户所有 |
| -G file | 检查file是否存在并默认组与当前用户相同 | 
| file1 -nt file2 | 检查file1是否比file2新 |
| file1 -ot file2 | 检查file1是否比file2旧 |

* 复合条件测试

	* [ condition1 ] && [ condition2 ]

		两个条件满足,则then部分执行;

	* [ condition1 ] || [ condition2 ]

		任意条件满足,则then部分执行;

* 使用双括号

	`(( expression ))`
	
	双括号允许比较过程中使用高级数学表达式;

	除了test中的标准数学运算符,还可以使用:
	
		++, --,!(逻辑求反),~(位求反),**(幂运算),<<(左位移),>>(有位移),&(按位与),|(按位或),&&(逻辑与),||(逻辑或);

	双括号可以在if中使用,也可以在普通命令中来使用赋值;

* 使用双方括号

	` [[ expression ]] `

	双方括号针对字符串提供高级特性;

	除了test标准比较外,还可以使用模式匹配,可以使用正则表达式来匹配字符串;

* case

	```
	case variable in
	pattern1 | pattern2) command1;;
	pattern3) command2;;
	*) default commands;;
	esac
	```
