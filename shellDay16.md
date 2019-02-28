# shellNote
## day16
### linux中的正则表达式

linux中有两种正则表达式引擎:

@ posix基础正则表达式引擎(BRE,basic regular expression)

@ posix扩展正则表达式引擎(ERE,extanded regular expression)

大多数linux工具至少符合BRE规范;

* BRE模式

	* 纯文本

		直接使用纯文本来匹配模式;

		正则表达式模式区分大小写;

		```
		$ echo "This is a test" | sed -n '/test/p'
		This is a test
		$ echo "This is a test" | sed -n '/Test/p'
		$
		```	

	* 特殊字符

		`.*[]^${}\+?|()`

		要用到以上特殊字符,需要对其转义,转义符号为反斜杠\;

		```
		$ echo "This is a $" | sed -n '/\$/p'
		This is a $
		```

	* 锚字符

		* 锁定在行首

			脱字符^定义从数据流中文本行的行首开始的模式;

			所以要用^字符,就必须放在正则表达式指定模式的前面;

			```
			$ echo "The book store" | sed -n '/book/p'
			$
			$ echo "The book store" | sed -n '/^Th/p'
			The book store
			$
			```

			如果正则表达式中只用了脱字符,就不需要对其转义,如果先指定了脱字符,另外还有^本文字符,就需要对其转义;

		* 锁定在行尾

			美元符\$定义了行尾锚点;

			必须放在文本模式之后来指明数据行以该文本模式结尾;

			```
			$ echo "The book store" | sed -n '/re$/p'
			The book store
			$ echo "The book store" | sed -n '/stor$/p'
			$
			```
		* 组合锚点

			```
			$ cat data
			This is a book
			This is a book too
			$ sed -n '/^This is a book$/p'
			This is a book
			$
			```
	
	* 点字符

		.字符用来匹配除换行符之外的任意单个字符;

		```
		$ cat data
		This is a test of a line.
		The cat is sleeping.
		That is a very nice hat.
		This test is at line four.
		at ten o'clock we'll go home.
		$ sed -n '/.at/p' data
		The cat is sleeping.
		That is a very nice hat.
		This test is at line four.
		$
		```
		
	* 字符组

		使用方括号来定义一个字符组;

		方括号中包含你希望出现的字符;

		```
		$ echo "Yes" | sed -n '/[Yy]es/p'
		Yes
		$ echo "yes" | sed -n '/[Yy]es/p'
		yes
		$
		```

	* 排除字符组

		可以寻找字符组中以外的字符;

		在字符组的开头加一个脱字符,就组成排除字符组;

		```
		$ cat data
		This is a test of a line.
		The cat is sleeping.
		That is a very nice hat.
		This test is at line four.
		at ten o'clock we'll go home.
		$ sed -n '/[^ch]at/p' data
		This test is at line four.
		$
		```

	* 字符区间

		可以使用单破折线来指定一个字符区间;

		`[0-9],[a-z]`
	* 星号字符

		在字符后放置*号表明字符必须在模式中出现0次或多次;

		```
		$ echo "ik" | sed -n '/ie*k/p' 
		ik
		$
		```

	* 特殊字符组

| 组 | 描述 |
| -- | ---- |
| [[:alpha:]] | 匹配任意字母字符,不区分大小写 |
| [[:alnum:]] | 匹配任意字母数字字符0-9a-zA-Z |
| [[:blank:]] | 匹配空格或制表符 |
| [[:digit:]] | 匹配数字 |
| [[:lower:]] | 匹配小写字母字符 |
| [[:print:]] | 譬如任意可打印字符 |
| [[:punct:]] | 匹配标点符号 |
| [[:space:]] | 匹配任意空白符:空格,制表符,NL,FF,VT,CR |
| [[:upper:]] | 匹配任意大写字母字符 |

```
$ echo "abc123" | sed -n '/[[:digit:]]/p' 
abc123
$
```

* ERE模式

	* 问号

		?字符表明前面的字符可以出现0次或1次;

		```
		$ echo "bt" | gawk '/be?t/{print $0}'
		bt
		$ echo "beeeet" gawk '/be?t/{print $0}'
		$
		```
	* 加号

		+字符表明前面的字符可以出现1次或多次;

		```
		$ echo "bt" | gawk '/be+t/{print $0}'
		$
		$ echo "beeeet" gawk '/be+t/{print $0}'
		beeeet
		$
		```
	* 使用花括号

		花括号可以指定重复次数或出现范围上下限:
	
		* m: 正则表达式准确出现m次;
		* m,n: 正则表达式至少出现m次,最多出现n次;

		```
		$ echo "bt" | gawk --re-interval '/b{1}t/{print $0}'
		$
		$ echo "bet" | gawk --re-interval '/b{1}t/{print $0}'
		bet
		$ echo "beet" | gawk --re-interval '/b{1}t/{print $0}'
		$
		```

	* 管道符号

		|符号允许使用逻辑或的方式指定正则表达式用两个或多个模式;

		`expr1|expr2`

		```
		$ echo "The cat is asleep" | gawk '/cat|dog/{print $0}'
		The cat is asleep
		$
		```

	* 表达式分组

		正则表达式可以用圆括号进行分组,当被分组后,该组会被看做一个标准字符;

		```
		$ echo "cat" | gawk '/(c|b)a(b|t)/{print $0}'
		cat
		$ echo "Sat" | gawk '/Sat(urday)?/{print $0}'
		Sat
		$ echo "Saturday" | gawk '/Sat(urday)?/{print $0}'
		Saturday
		$
		```
