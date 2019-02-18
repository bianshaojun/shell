# shellNote
## day12

* echo命令

	* 参数-e

		有时需要在echo时,输出制表符或换行符;

		这时需要加入选项-e;

	* 参数-n
		
		默认情况下,echo命令会在打印完毕换行;

		-n选项,可以去掉echo命令末尾的换行符;

* clear命令

	清理出现在屏幕上的文本;

* select命令

	select命令可以创建菜单,并获取输入再自动处理;

	```
	select var in list
	do 
		commands
	done
	```

	* list参数
		
		由空格分隔的文本选项列表,这些列表构成了菜单;

		select命令会给列表中的每一项显示为一个带编号的选项;

	* PS3环境变量

		设置PS3环境变量,可以设置菜单输入的提示符;

		`PS3="enter your option:"`

	* var

		输入的选项会存入到var变量中,当然var的名字可以任意命名;

* dialog命令

	可以使用dialog创建窗口,可以生成输入框,消息框等部件;

	由于种类较多,不记录太多,需要时在翻阅相关资料;

	* kdialog命令

		对于KDE桌面来说,可以使用kdialog来生成图形化的交互脚本;

	* gdialog命令和zenity命令

		对于GNOME桌面来说,可以使用gdialog和zenity来生成图形化的交互脚本;
