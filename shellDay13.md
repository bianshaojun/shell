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


