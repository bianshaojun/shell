# shellNote
## day14

###继续sed编辑器

* 移动下一行文本-next命令

	n命令可以使sed编辑器移动到数据流的下一行文本;

	```
	$ cat data.txt
	This is the header line.

	This is the second line.

	This is the last line.
	$ sed '/header/{n ; d}' data.txt
	This is the header line.
	This is the second line.

	This is the last line.
	$
	```

* 多行文本-N命令

	N命令会将下一文本行添加到现有的模式空间中的已有文本后;

	模式空间:当前sed编辑的工作空间;

	```
	$ cat data.txt
	This is the header line.
	This is the first line.
	This is the second line.
	This is the last line.
	$ sed '/first/{N ; s/\n/ / }' data.txt
	This is the header line.
	This is the first line.  This is the second line.
	This is the last line.
	$	
	```

* 多行删除命令-D命令

	在使用多行操作时,如果使用d命令来进行删除时,会将sed编辑器模式空间中的多行删除;

	D命令可以在多行操作时,只删除模式空间中的第一行,该命令会删除到换行符(包含换行符)为止的所有字符;

	```
	$ cat data.txt

	This is the header line.
	This is a data line.

	This is the last line.
	$ sed '/^$/{N ; /header/D}' data.txt
	This is the header line.
	This is a data line.

	This is the last line.
	$
	```

* 多行打印命令-P命令

	P命令可以在多行操作时,只打印模式空间中的第一行,该命令会打印到换行符(包含换行符)为止的所有字符;

	同样跟p命令一样,一般情况会配合-n选项来使用;

	```
	$ cat data.txt
	On Monday, the Linux System
	Administrator's group meeting will be held.
	All Sysyer Administrators should attend.
	Thank you for your attendance.
	$ sed -n {N ; /System\nAdministrator/P } data.txt
	On Mondat, the Linux System
	$
	```

* 保持空间

	sed编辑器有两块保存文本的空间,一个是模式空间,一个是保持空间;

	模式空间,是sed编辑器在工作时,执行命令时的一块缓冲空间;

	保持空间,是sed编辑器需要临时保存一些行的一块缓冲空间;

	保持空间操作命令:

| 命令 | 描述 |
| ---- | ---- |
| h | 将模式空间复制到保持空间 |
| H | 将模式空间附加到保持空间 |
| g | 将保持空间复制到模式空间 | 
| G | 将保持空间附加到模式空间 | 
| x | 交换模式空间和保持空间的内容 |

```
$ cat data.txt
This is the header line.
This is the first line.
This is the second line.
This is the last line.
$ sed -n '/first/ {h ; p ; n ; p ; g ; p}' data.txt
This is the first line.
This is the second line.
This is the first line.
$
```

* 排除命令-!命令

	!命令可以用来排除命令;

	```
	$ cat data.txt
	This is the header line.
	This is the first line.
	This is the second line.
	This is the last line.
	$ sed -n '/first/!p' data.txt
	$
	This is the header line.
	This is the second line.
	This is the last line.
	$
	```
* 分支命令-b命令

	可以根据address参数来决定哪些行的数据触发分支命令;

	label定义了跳转的位置;

	如果没有label,跳转命令会跳转到脚本的结尾;

	`[address]b [label]`

	```
	$ echo "This, is, a, test, to, remove, commas." | sed -n '{
	> :start
	> s/,//1p
	> /,/b start
	> }'
	This, is a, test, to, remove, commas.
	This, is a test, to, remove, commas.
	This, is a test to, remove, commas.
	This, is a test to remove, commas.
	This, is a test to remove commas.
	```

* 测试命令-t命令

	t命令会根据替换命令的结果跳转到某个标签;

	如果替换命令成功匹配并替换了一个模式,测试命令就会跳转到指定的标签,反之不会跳转;

	如果没有标签,就跳转到sed脚本的结尾;

	`[address]t [label]`

	```
	$ echo "This, is, a, test, to, remove, commas." | sed -n '{
	> :start
	> s/,//1p
	> t start
	> }'
	This, is a, test, to, remove, commas.
	This, is a test, to, remove, commas.
	This, is a test to, remove, commas.
	This, is a test to remove, commas.
	This, is a test to remove commas.
	```

* 模式替换

	* &符号

		&符号可以用来代表替换命令中匹配的模式;

		```
		$ echo "The cat sleeps in his hat." | sed 's/.at/"&"/g'
		The "cat" sleeps in his "hat".
		$
		```
	* 替换子模式

		sed编辑器可以使用圆括号来定义替换模式中的子模式;

		可以在替换模式中使用特殊字符来引用每个子模式;

		替换字符由反斜杠和数字组成;

		第一个子模式\1,第二个\2,以此类推;

		```
		$ echo "1234567" | sed '{
		> :start
		> s/\(.*[0-9]\)\([0-9]\{3\}\)/\1,\2/
		> t start
		> }'
		1,234,567
		$
		```		


