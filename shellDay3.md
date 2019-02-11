# shellNote
## day3

* /etc/passwd文件

	该文件专门用来将用户的登录名匹配到对应的UID值;

	UID用户ID;

	root是linux系统的管理员,固定分配UID是0;

	另外还有其他的系统账户,运行系统上的各种服务进行的特殊账户;

	系统账户一般使用500以下的UID值;

	普通账户,大多数从500开始的UID值;

	文件字段信息如下:

	* 登入用户名

	* 用户密码--设置为了x,用户密码保存在另一个单独的文件中/etc/shadow

	* 用户账户的UID

	* 用户账户的组ID,GID

	* 用户账户的文本描述

	* 用户HOME目录的位置

	* 用户的默认shell

* /etc/shadow文件

	对linux系统密码管理提供控制;
	
	文件字段信息如下:

	* 登入名

	* 加密后的密码

	* 自上次修改密码后过去的天数密码

	* 多少天后才能更改密码

	* 多少天后必须更改密码

	* 密码过期前提多少天提醒用户更改密码

	* 密码过期后多少天禁用用户账户

	* 用户账户被禁用的日期

	* 预留字段

* 添加新用户--useradd

	useradd可以用来添加新用户;

	默认值存放在/etc/default/useradd文件中;

	可以使用`useradd -D`来查看默认值;

	默认HOME目录配置,/etc/skel/

	* useradd -m

		-m参数会为用户创建HOME目录,该目录就是/etc/skel目录的内容;

		默认情况下,不会创建HOME目录;

* 删除用户--userdel

	默认情况下,userdel只会删除/etc/passws文件中的用户信息,而不会删除系统中的任何文件;

	* -r参数

		会删除用户的HOME目录以及邮件目录;

* 修改用户

	* usermod

		修改用户账户的字段，还可以指定主要组以及附加组的所属关系；

		* -c参数

			修改备注字段；
		* -e参数

			修改过期日期；
		* -g参数

			修改默认的登入组；
		* -l参数

			修改用户账户的登入名；
		* -L参数

			锁定账户，使用户无法登入；
		* -p参数

			修改账户的密码；
		* -U参数

			解除锁定，使用户能够登入；
	* passwd

		修改已有用户的密码；

		* -e参数

			强制用户下次登入时修改密码；
	
	* chpasswd

		从文件中读取登入名密码对,并更新密码;

* chsh命令

	用来快速修改默认的用户登入shell;

	`chsh -s /bin/csh username`--shell完整路径作为参数;

* finger命令 

	查看用户信息;

* chfn命令

	提供在/etc/passwd文件的备注字段中存储信息的标准方法;

* chage命令

	用来帮忙管理用户账户的有效期;

	* -d参数

		设置上次修改密码到现在的天数;

	* -E参数

		设置密码过期的日期;

	* -I参数

		设置密码过期到锁定账户的天数;

	* -m参数

		设置修改密码之间最少要多少天;

	* -W参数

		设置密码过期前多久开始出现提醒信息;

	* 日期值可以选中格式:

		* YYYY-MM-DD格式的日期
	
		* 代表从1970年1月1日到该日期天数的数值;


* /etc/group文件

	保存组信息的文件;

	GID组ID,系统账户通常分配低于500的值.用户组从500开始分配;

	字段信息:

	* 组名

	* 组密码
	
	* GID

	* 属于该组的用户列表;

* 创建新组--groupadd

	`groupadd groupname`;

* 添加用户到组--usermod

	`useradd -G groupname username`

	-G参数会把用户添加到组里;

* 修改组--groupmod

	* -g参数

		修改已有组的GID;
	* -n参数

		修改已有组的组名;

* 文件权限符

	根据ls输出的权限为例;

	* 第一个字符代表了文件类型:

		* -代表文件
		* d代表目录
		* l代表链接
		* c代表字符型设备
		* n代表网络设备

	* 后三组分别为:
		* 对象的属主权限
		* 对象的属组权限
		* 其他用户权限
	* r代表对象是可读的
	* w代表对象是可写的
	* x代表对象是可执行的

* 默认文件权限--umask

	由于umask表示掩码,只是屏蔽掉不授予的安全级别权限;

	所以新建的文件,有权限全值减去umask掩码值就是文件的默认权限;

	eg:
	
	umask为0022;

	新建文件权限为,666-022=644(文件全权限为666,因为没有可执行权限)

	新建目录权限为,777-066=744(目录全权限为777)

* 改变权限--chmod

	chmod用来改变文件和目录的安全性设置;

	`chmod options mode file`

	mode可以直接用八进制模式`chmod 760 file`

	也可以用符号模式:

	[ugoa...][+-=][rwxXstugo...]

	* u代表用户
	* g代表组
	* o代表其他
	* a代表所有
	
	`chmod a+x file`--给所有加上可执行权限

	* -R参数

		可以递归的作用到文件和子目录上;

* 改变所属关系--chown,chgrp

	* chown命令

		改变文件的属主;

		`chown options owner[.group] file`

		可以用登入名或UID来指定文件的新属主;

		也可以一并改变属组;

		* -R参数递归改变目录和子目录

		* -h参数可以改变文件的所有符号链接文件的所属关系;

	* chgrp命令

		改变文件或目录的属组;

		`chmod groupname file`

* 创建分区--fdisk

	管理系统上存储设备分区;

* 创建文件系统--mkfs.ext4

	可以使用mkfs.ext4等等格式化工具来制作文件系统;

* 文件系统的检查和修复--fsck

	`fsck options filesystem`

	只能针对未被挂载的设备;

* 逻辑卷

	* 创建物理卷--pvcreate

		只是简单的将分区标记成linux LVM系统分区而已;

		在之前利用fdisk的t命令将分区类型改成linuc LVM;

		`pvcreate /dev/sdb1`
		
		* 查看物理卷--pvdisplay

			`pvdisplay /dev/sdb1`

	* 创建卷组--vgcreate

		`vgcreate Vol1 /dev/sdb1` -- 创建名为Vol1的卷组;

		* 查看卷组--vgdisplay

			`vgcreate Vol1`

	* 创建逻辑卷--lvcreate

		`lvcreate -l 100%FREE -n lvtest Vol1`

		-l参数--指定分配给新逻辑卷的逻辑区段数,或者要用的逻辑区段的百分比;

		-n参数--指定新逻辑卷的名称;

	* 创建逻辑卷文件系统

		利用mkfs工具即可;
	
	* 修改LVM

| 命令 | 功能 |
| ---- | ---- |
| vgchange | 激活和禁用卷组 |
| vgremove | 删除卷组 | 
| vgextend | 将物理卷加到卷组中 |
| vgreduce | 从卷组中删除物理卷 |
| lvextend |增加逻辑卷的大小|
| lvreduce |减小逻辑卷的大小 | 

* 基于Debian系统的软件管理

	* aptitude命令

		默认进入管理软件包界面;

	* 显示某个特定包的信息

		`aptitude show package_name`

	* 显示某个包相关的所有文件列表

		`dpkg -L package_name`

	* 反向搜索某个文件属于哪个包

		`dpkh --search absolute_file_name` -- *注意需要使用绝对文件路径*

	* 搜索软件包

		`aptitude search package_name`

		搜索出来的每个包前面会有一个p,v或i;

		p或v表示这个包可用,但还没安装;

		i表示这个包已经安装了;

	* 安装软件包

		`aptitude install package_name`

	* 更新软件

		`aptitude safe-upgrade`

		这个命令不需要指定软件包,会更新所有已安装的软件,并解决依赖关系;

		还有其他更新方式,full-upgrade和dist-upgrade,这两个不会检查依赖关系;

	* 卸载软件

		`aptitude purge package_name` -- 删除软件包和相关数据和配置文件;

		`aptitude remove package_name` -- 删除软件包不删除数据和配置文件;

		可以使用search查看是否删除,c表示已删除,但配置文件未删除,p表示,都已经删除;

	* aptitude仓库

		默认软件仓库存储在文件/etc/apt/sources.list中;

		`deb (or deb-src) address distribution_name package_type_list` -- 指定仓库源;

		deb说明是一个已编译的程序源;

		deb-src说明是一个源代码的源;
