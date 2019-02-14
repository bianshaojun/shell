## shellNote
# day9

* 终端进程

	Ctrl+C组合键会生成SIGINT信号,并将其发送给当前在shell中运行的所有进程,终止其进程;

* 暂停进程

	Ctrl+Z组合键会生成一个SIGTSTP信号,停止shell中运行的任何进程;

	停止进程的意思不是终止进程,可以理解为暂停,进程还在内存中,需要时可以继续运行;

	```
	$ sleep 100
	^Z
	[1]+ Stopped sleep 100
	$ exit
	exit
	There are stopped jobs.
	$
	```

	暂停后,会提示该进程stopped,提示中的方括号的数字是shell分配的作业号;

	shell会给每一个进程分配唯一作业号,1,2,3以此类推;

* 捕获信号--trap

	`trap command signals`

	trap可以指定shell脚本要监看并从shell中拦截的linux信号;

	脚本收到了信号,该信号不再由shell处理;

	```
	#!/bin/bash
	# Testing signal trapping
	#
	trap "echo ' Sorry! I have trapped Ctrl-C'" SIGINT
	#
	echo This is a test script
	#
	count=1
	while [ $count -le 10 ]
	do
	echo "Loop #$count"
	sleep 1
	count=$[ $count + 1 ]
	done
	#
	echo "This is the end of the test script"
	#
	```

* 捕获退出信号--EXIT

	`trap "echo Goodbye..." EXIT`	

* 修改捕获信号

	再一次使用trap即可;

	```
	trap "echo ' Sorry... Ctrl-C is trapped.'" SIGINT
	trap "echo ' I modified the trap!'" SIGINT
	```

* 删除捕获信号

	只需要在trap和信号列表之间加上两个波折号;

	`trap -- SIGINT`

* 在非控制台下运行脚本

	如果只是想在终端中启动shell脚本,并让其在后台模式运行到结束,退出终端也继续运行;

	nohup命令,可以运行另一个命令来阻断所有发送给该进行的SIGHUP信号;

	`nohup ./test.sh &`

	nohup运行的程序的输出会追加到nohup.out文件中;
