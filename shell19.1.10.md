 # shell 编程
## 第一次整理
### 时间：2018年12月28日
### 主要内容：
创建shell脚本文件时，必须在文件的第一行指定使用的shell
```
#!/bin/bash
```
```
#!/bin/bash
echo This is a test file
```
查看文件权限
```
ls -l 或 ll
```
alias 设置别名
```
vi .bashrc
alias ll='ls -lh'
source .bashrc
```
###### 文件权限
总共十位，分为4段，第一段表示文件类型，有-、p、d、l、s、c、b，-表示普通文件，d表示目录文件，l表示符号链接文件，c表示字符设备文件，b表示块设备文件，p表示命名管道文件，s表示socket文件。
>1. linux一切皆文件   
>2. 字符设备：键盘，/dev中可以看到     
>3. 块设备：硬盘
>4. 命名管道文件：FIFO（First in, First out）文件，mkfifo可以生成。       
>5. 管道只能用于父进程和子进程之间，或者拥有相同祖先的两个子进程之间，本质是内核中的一块缓存，在文件系统中以特殊的设备文件(管道文件)存在      
>6. socket文件：网络传输

从左到右，1-3代表文件所有者(owner)权限，4-6代表同组用户(group)权限，7-9代表其他用户(other)权限
读取权限为4，写入权限为2，执行权限为1
7（读取、写入、执行），6（读取、写入），5（读取、执行），4（读取），0（无权限）
```
- rw- r-- r--
- --- --- ---
pts/0
# mkfifo scriptfifo
pts/1
# cat scriptfifo
pts/0
script -f scriptfifo
```
添加执行权限
```
chmod u+x file
```
单引号:
>可以说是所见即所得：即将单引号内的内容原样输出，或者描述为单引号里面看到的是什么就会输出什么。

双引号:
>把双引号内的内容输出出来；如果内容中有命令、变量等，会先把变量、命令解析出结果，然后在输出最终内容来。


```
#!/bin/bash
#This script displays the date and who's logged on
echo -n The time and date are:
date
echo "Let's see who's logged into the system:"
who
```
$取值
```
#!/bin/bash
#display user information from the system.
echo "User info for userid: $USER"
echo UID: $UID
echo HOME: $HOME
```
#### **securecrt crtl+s 停止输入  ctrl+q 恢复输入**
------
## 第二次整理
#### 时间：2018年12月29日
### 主要内容：
用户变量
由字母、数字、下划线组成，长度不超过20，且区分大小写
= 赋值，自动判断变量值的数据类型

```
var1=10
var2=-57
var3=testing
var4="still more testing"
```

命令替换：从命令输出中提取信息，并将其赋给变量。
- [ ] 反引号字符（\`），键盘tab键上方
- [ ] $( ) 格式

```
testing=`date`
testing=$(date)
```

生成 181229.log 文件

```
#!/bin/bash
#copy the /usr/bin directory listing to a log file
today=$(date +%y%m%d)
ls /usr/bin -al > $today.log
```

###### > 输入重定向
###### >> 追加
###### < 输出重定向
###### << 内联重定向：无须使用文件进行重定向，只需在命令行中指定用于输入重定向的数据即可。

wc 对数据中的文本进行计数，默认情况下输出3个值；
- [ ] 文本的行数
- [ ] 文本的词数
- [ ] 文本的字节数

```
wc << EOF
test string 1
test string 2
test string 3
EOF
输出 3   9   42
```

EOF是end of file的缩写，通常在文本的最后存在此字符表示资料结束。脚本中前面的cat >...<< EOF表示读取下面的字符直到遇到EOF为止，脚本最后的EOF后面不能有空行，否则会报错。

```
#向index.php中写入phpinfo信息。
cat >apache/htdocs/index.php << EOF
<?php
phpinfo();
?>
EOF
```

管道（|），将前面命令的输出作为后面命令的输入    
常用的如查看调用80端口的服务

```
netstat -tulnp | grep 80
```
------
## 第三次整理
#### 时间：2018年12月30日
### 主要内容：
各种括号的作用    
单小括号，常用与命令组和命令替换    
命令组：括号内的命令会新开一个shell顺序执行，括号内多个命令之间用分号分开

命令替换：$(cmd) 等同于 \`cmd\`   
在多层次的复合替换中，｀｀必须要额外的跳脱处理（反斜线），而$( )则不需要  
反引号(\`)需要用反斜线转义，$( )不需要转义
```
echo `echo '\\'`
输出 \
echo `echo '\\\'`
输出 \\
echo $(echo '\\')
输出 \\
```

双小括号，常用与算数运算比较。  
语法    
((表达式1,表达式2,...))
1. 双括号中可以直接使用a++,b--等表达式。
2. 双括号中可以进行for，while,if条件运算
3. 双括号中，所有变量可以不使用$符号。
4. 双括号中可以进行四则运算和逻辑运算。
5. 双括号中可以进行多个表达式运算，各个表达式之间用逗号(,S)分开
```
for((i=0;j<5;i++)) 等同于 for i in `seq 0 4`
a=1
echo((a=a+1))
```

单中括号
bash的内部命令，[ 和test是等同的，if/test结构中的左中括号是调用test的命令标识，右中括号是关闭条件判断的。
test/[ ]命令把它的参数作为比较表达式或者作为文件测试，并且根据比较的结果来返回一个退出状态码，结果为真则返回状态0
```
if [ "$(whoami)" == "root" ];then
	echo "当前用户为root，将进行部署"
注意：[ ] 两边和参数之间要有空格，否则报错。
```
[ ] 中可用的字符串比较运算符只有==和!=
[ ] 中可用的整数比较运算符有-eq,-gt等

双中括号    
[[是 bash 程序语言的关键字。，并不是一个命令。[[ ]] 结构比[ ]结构更加通用。在[[ ]]之间所有的字符都不会发生文件名扩展或者单词分割，但是会发生参数扩展和命令替换     
支持字符串的模式匹配，字符串比较时可以把右边的作为一个模式，而不仅仅是一个字符串，比如[[ hello == hell? ]]（注：问号为通配符，表示任1个字符），结果为真。[[ ]] 中匹配字符串或通配符，不需要引号。
&&、||、<和> 操作符能够正常存在于[[ ]]条件判断结构中，但是如果出现在[ ]结构中的话，会报错
```
if [[ $(whoami) == root ]];then
	echo "当前用户为root，将进行部署"
注意：[ ] 两边和参数之间要有空格，否则报错。
```

大括号
1. 对括号内的文件名做扩展，括号内不允许有空白，除非该空白被引用或转义。

```
touch {a,b}.txt
结果：生成a.txt和b.txt
touch {a..d}.txt
结果：生成a.txt、b.txt、c.txt、d.txt

文件名扩展
cp /etc/apt/sources.{list,list.bak}
cp /etc/apt/sources.list
cp /etc/apt/sources.list.bak

嵌套扩展
echo {a,b{1,2,3},c}
输出：a,b1,b2,b3,c

增量扩展
echo {1..10..2}
输出：1 3 5 7 9
```

2. 包含代码块，又被称为内部组，这个结构相当于创建了一个匿名函数,与小括号中的命令不同，大括号内的命令不会新开一个子shell运行，即脚本余下部分仍可使用括号内变量。
```
执行自动更新，将yes作为括号内部函数的参数输入
yes | {
    sudo apt-get update
}
一个比较危险的玩法
yes | {
    sudo rm -r /
    sudo dd if=/dev/zero of=/dev/sda
} 2>/dev/null
```
3. 变量替换
一般情况下，$var与${var}是没有区别的，但是用${ }会比较精确的界定变量名称的范围
```
A=B
echo ${A}B
输出：BB
```
常见通配符

字符 | 含义 | 实例
:--:| :--: | :--:
 * | 匹配0或多个字符 | a*b ab之间可以有任意长度的任意字符
 ？| 匹配任1个字符   | a?b ab之间只能有1个任意字符
 [list] | 匹配list中的任意单一字符 |  a[xyz]b ab之间只能有一个字符，且只能为x或y或z
 [!list] | 匹配除list中的任意单一字符 | a[!0-9]b ab之间只能有一个字符，且不能为阿拉伯数字
 [c1-c2] | 匹配c1-c2中任意单一字符 | a[0-9]b ab之间有且仅有一个阿拉伯数字
 {string1,string2,...} | 匹配string1或string2（或更多）其中一个字符串 | a{abc,123,xyz}b ab之间只能有个一字符串，且只能为abc或123或xyz

------
## 第四次整理
#### 时间：2018年12月31日
### 主要内容：
文件描述符
linux中一切皆文件，内核给每个访问的文件分配了文件描述符(File Descriptor)，它本质是一个非负整数，在打开或新建文件时返回，以后读写文件都要通过这个文件描述符。
文件描述符其实是一个索引值，指向每个进程打开文件的记录表，[POSIX](https://baike.baidu.com/item/POSIX)已经定义了STDIN_FILENO（标准输入文件）、STDOUT_FILENO（标准输出文件）和STDERR_FILENO（标准错误文件）三个常量，也就是0、1、2。这三个文件描述符是每个进程都有的，这也解释了为什么每个进程都有编号为0、1、2的文件而不会与其他进程冲突。

系统设定：
默认设备 | 标准文件 | 文件描述符 | 设备
:-------: | :--------: | :--------: | :--------:
默认输入设备 | 标准输入文件（STDIN_FILENO） | 0 | 键盘
默认输出设备 | 标准输出文件（STDOUT_FILENO） | 1 | 显示器
默认错误输出 | 标准错误文件（STDERR_FILENO）|  2 | 显示器

```
#列出/目录下的文件
ls /
输出：根目录下的文件和文件夹
ls / >/dev/null 2>&1 
输出：空
```
> /dev/null 文件可以理解为垃圾桶，信息到这里面就消失了，>/dev/null 相当于1 > /dev/null 即将标准输出上的信息重定向到垃圾桶，2>&1 即标准错误输出重定向（等同于）到标准输出，因为之前标准输出已经重定向到了空设备文件，所以标准错误输出也相当于重定向到空设备文件（垃圾桶）。

退出脚本
linux中的每个命令都是用退出状态码告诉shell它已经执行完毕。退出状态码是一个0-255的整数值。
linux中专门提供了变量（$?）来保存上个已执行命令的退出状态码，按照惯例，一个执行成功的命令的退出状态码为0，若一个命令退出时有错误发生，退出状态码就是一个正数值。
```
ls
输出：当前目录下的文件
echo $?
输出：0

asdfa
输出：command not found
echo $?
输出：127
```
linux退出状态码
状态码 | 描述
:----:|:----:
0 | 命令成功结束
1 | 一般性未知错误
2 | 不适合的shell命令
126 | 没有执行命令的正确权限
127 | 没找到命令
128 | 无效的退出参数
128+x | 与linux信号x相关的严重错误
130 | 通过ctrl+c退出的命令
255 | 正常范围之外的退出码

正常情况下，shell脚本会以脚本中最后一个命令的退出状态码退出，但可以改变这种默认行为，返回自己的状态码。
exit命令允许在脚本结束时指定一个退出状态码，退出状态码最大为255，若是超过255，则最终数值是指定退出码除以256后得到的余数。
```
#检测当前用户是否为root
if [[ $(whoami) == root ]];then
	echo "当前用户为root，将进行部署"
else
	echo "请更换root用户进行操作"
	exit 1
fi
```
exit允许使用变量作为退出状态码。
```
脚本内执行
#!/bin/bash
# testing the exit status
var1=10
var2=20
var3=$[var1 + var2]
exit $var3

echo $?
输出：30

```
小结

1. bash shell脚本允许将多个命令串起来放进脚本中，创建脚本的最基本的方式是将命令行中的多个命令通过分号分开来，shell会按顺序逐个执行命令，在显示器上显示每个命令的输出。也可以创建一个shell脚本文件，将多个命令放进同一个文件，让shell依次执行。
2. shell脚本文件必须定义用于运行脚本的shell。这个可以通过#!符号在脚本文件的第一行指定，后面跟上shell的完整路径，常用的为#!/bin/bash。
3. 在shell脚本内，可以通过在变量前使用美元符来引用环境变量，也可以定义自己的变量以便在脚本内使用，并对其赋值，甚至还可以通过反引号或$()捕获的某个命令的输出。在脚本中可以通过在变量名前放置一个美元符来使用变量的值。
4. bash shell允许更改命令的标准输入和输出，将其重定向到其他地方。可以通过 > 将命令输出从显示器屏幕重定向到一个文件中。也可以通过 >> 将输出数据追加到已有文件。< 用来将输入重定向到命令。可以将文件内容重定向到某个命令。
5. Linux管道命令（|）允许将命令的输出直接重定向到另一个命令的输入。Linux系统能够同时运行这两条命令，将第一个命令的输出发送给第二个命令的输入，不需要借助任何重定向文件。
6. 在bash shell中，可以通过将美元符号放在由方括号包围的表达式之前来执行基本的运算。
7. shell中运行的每个命令都会产生一个退出状态码,退出状态码是一个0～255的整数值，表明命令是否成功执行；如果没有成功，可能的原因是什么。退出状态码0表明命令成功执行了。你可以在shell脚本中用exit命令来声明一个脚本完成时的退出状态码。

------
## 第五次整理
#### 时间：2019年1月1日
### 主要内容：
结构化命令
许多程序要求对shell脚本中的命令施加一些逻辑流程控制，有一类命令会根据条件使脚本跳过某些命令，这样的命令通常称为结构化命令，结构化命令允许改变程序执行顺序。
if-then-else命令
```
if command
then
    commands
else
    commands 
fi
或
if command; then commands; else commands; fi
```
shell 的if语句会运行if后面的那个命令，如果该命令的退出状态码是0，则位于then部分的命令就会被执行；若该命令的退出状态码是其他值，then部分的命令就不会被执行，shell会继续执行else中的命令，fi语句表示if-then语句到此结束。
```
#!/bin/bash
#testing the if statement
if pwd
then
    echo "It worked"
else
    echo "Error"
fi
输出：/root(当前路径)
输出：It worked
```
布尔逻辑
shell中有两个常量（实际是两个内置命令），true和false，一个表示真（返回值为0），一个表示假（返回值为1）；对它们可以进行与、或、非运算等常规逻辑运算。
与运算用&&表示，或运算用||表示，非运算，即取反用!表示。
```
#与或非运算
if true;then echo "YES"; else echo "NO"; fi
输出：YES
if false; then echo "YES"; else echo "NO"; fi
输出：NO

#与运算用&&表示
if true && true; then echo "YES"; else echo "NO"; fi
输出：YES
if true && false; then echo "YES"; else echo "NO"; fi
输出：NO

#或运算用||表示
if true || true; then echo "YES"; else echo "NO"; fi
输出：YES
if true || false; then echo "YES"; else echo "NO"; fi
输出：YES

#非运算，即取反用!表示
if ! false; then echo "YES"; else echo "NO"; fi
输出：YES
if ! true; then echo "YES"; else echo "NO"; fi
输出：NO
```
条件测试

数值测试
运算符 | 命令 | 描述
:--------: | :-----: | :------:
-eq | n1 -eq n2 |检测n1和n2是否相等，相等返回true
-ne | n1 -ne n2 |检测n1和n2是否不相等，不相等返回true
-gt | n1 -gt n2 | 检测n1是否大于n2，是则返回true
-lt | n1 -lt n2 | 检测n1是否小于n2，是则返回true
-ge | n1 -ge n2 | 检测n1是否大于或等于n2，是则返回true
-le | n1 -le n2 | 检测n1是否小于或等于n2，是则返回true

```
#判断当前用户是否为root
if [ $UID -ne 0 ];then
    echo Not root user
else
    echo Root user
fi
输出：Root user

#判断两数是否相等
if [ 5 -eq 5 ];then echo "YES"; else echo "NO"; fi
输出：YES
```
字符串测试
运算符 | 命令 | 描述
:--------: | :-----: | :------:
= | str1 = str2 |检测str1和str2是否相等，相等返回true
!= | str1 != str2 |检测str1和str2是否不相等，不相等返回true
> | str1 > str2 | 检测str1是否大于str2，是则返回true
< | str1 < str2 | 检测str1是否小于str2，是则返回true
-n | -n str1 | 检测str1的长度是否非0，是则返回true
-z | -z str1 | 检测str1的长度是否为0，是则返回true

```
if [[ -n 'not empty' ]]; then echo "YES"; else echo "NO"; fi
输出：YES
```
文件测试
比较 | 描述
:---: | :---:
-d file | 检查file是否存在并是一个目录
-e file | 检查file是否存在
-f  file | 检查file是否存在并是一个文件
-r  file | 检查file是否存在并可读
-s file | 检查file是否存在并非空
-w file | 检查file是否存在并可写
-x file | 检查file是否存在并可执行
-o file | 检查file是否存在并属当前用户所有
-G file | 检查文件是否存在并且默认组合当前用户相同
file1 -nt file2 | 检查file1是否比file2新
file1 -ot file2 | 检查file1是否比file2旧
```
#!/bin/bash
# Look before you leap
#
jump_directory=/home/arthur
#
if [ -d $jump_directory ]
then
	echo "The $jump_directory directory exists"
	cd $jump_directory
	ls
else
	echo "The $jump_directory directory does not exist"
fi
#
输出：The /home/arthur directory does not exist

if [ -f /bin/bash ]; then echo "YES"; else echo "NO"; fi
输出：YES
```
复合条件测试
if-then语句允许使用布尔逻辑来组合测试
- [ ] [ condition1 ] && [ contional2 ]
- [ ] [ condition1 ] || [ condition2 ]

```
# 检查$HOME是否是一个目录，且该目录下testing文件是否存在并可写
#!/bin/bash
# testing compound comparisons
#
if [ -d $HOME ] && [ -w $HOME/testing ]
then
	echo "The file exists and you can write to it"
else
	echo "The file not exists"
fi
输出：The file not exists
```



## 第六次整理

#### 时间：2019年1月2日

### 主要内容：
if-then 的高级特性
shell提供了两项可在if-then语句中使用的高级特性：

- [ ] 用于数学表达式的双括号
- [ ] 用于高级字符串处理功能的双方括号
双括号命令允许在比较过程中使用高级数学表达式。
命令格式：(( expression ))
expression可以是任意的数学赋值或比较表达式。

双括号命令符号
符号 | 描述
:----: | :-----:
var++ | 后增
var-- | 后减
++var | 先增
--var | 先减
! | 逻辑求反
~ | 位求反
** | 幂运算
<< | 左位移
\>> | 右位移
& | 位布尔和
\| | 位布尔或
&& | 逻辑和
\|\| | 逻辑或

```
#!/bin/bash
# using double parenthesis
#
var1=10
#
if (( $var1 ** 2 > 90 ))
then
	(( var2 = $var1 **2 ))
	echo "The square of $var1 is $var2"
fi
输出：The square of 10 is 100
注：双括号内的大于号不需要转义。
```

双方括号命令提供了针对字符串比较高级的特性
格式：[[ expression ]]

```
#!/bin/bash
# using pattern mathing
#
if [[ $USER == r* ]]
then
	echo "Hello $USER"
else
	echo "Sorry,I do not know you"
fi
输出：Hello root
注：双等于号将右边的字符串(r*)视为一个模式，并应用模式匹配规则。双方括号命令$USER环境变量进行匹配，看它是否以字母r开头，若是，则比较通过，shell执行then部分的命令。
```

case命令会将指定的变量与不同模式进行比较，如果变量和模式是匹配的，则shell会执行为该模式指定的命令。可以通过竖线操作符在一行中分割出多个模式，星号会捕获所有与已知模式不匹配的值。以case开始，到esac结束。
语法
```
case variable in
pattern1 | pattern2) commands1;;
pattern3) commands2;;
*) default commands;;
esac
注：除了*)模式，各个分支中;;是必须的，相当于break。
```
```
#!/bin/bash
# looking for a possible value
#
if [ $user = "root" ]
then 
	echo "Welcome $USER"
	echo "Please enjoy you visit"
elif [ $USER = "barbara" ]
then 
	echo "Welcome $USER"
	echo "Please enjoy you visit"
elif [ $USER "testing" ]
then
	echo "Special testing account"
elif [ $USER = "jessica" ]
then
	echo "Do not forget to logout when you're done"
else
	echo "Sorroy, you are not allowed here"
fi
输出：Welcome Root
	 Please enjoy your visit
	 
#使用case
#!/bin/bash
# using the case command
#
case $USER in
root | barbara
	echo "Welcome, $USER"
	echo "Please enjoy you visit";;
testing)
	echo "Special testing account";;
jessica)
	echo "DO not forget to log off when you are done;;
*)
	echo "Sorry, you are not allowed here";;
esac
```
##### 小结
1. 结构化命令允许改变shell脚本的正常执行流。最基本的结构化命令是if-then语句。该语句允许执行一个命令并根据该命令的输出来执行其他命令。
2. 也可以扩展if-then语句为if-then-else，加入一组当指定命令失败后shell执行的命令(else)，仅在测试命令返回非零退出状态码时，if-then-else语句才允许执行命令。
3. 可以将if-then-else语句通过elif语句连接起来。elif等同于使用else if语句，会在测试命令失败时提供额外的检查。
4. 在很多脚本中，你可能希望测试一种条件而不是一个命令，比如数值、字符串内容、文件或目录的状态。test命令提供了测试这些条件的简单方法。如果条件为TRUE，test命令会为if-then语句产生退出状态码0。如果条件为FALSE，test命令会为if-then语句产生一个非零的退出状态码。
5. 方括号是与test命令同义的特殊shell命令。可以在if-then语句中将测试条件放在方括号中来测试数值、字符串和文件条件。
6. 双括号使用另一种操作符进行高级数学运算。双方括号命令允许高级字符串模式匹配运算
7. case命令是执行多个if-then-else命令的简便方式，它会参照一个值列表来检查单个变量的值。



## 第七次整理

#### 时间：2019年1月3日

### 主要内容：

for命令运行创建一个遍历一系列值的循环，每次迭代都使用其中一个值来执行已定义好的一组命令。

基本格式：

```
for var in list
do
	commands
done
```
do和done语句之间输入的命令可以是一条或多条标准的shell命令，每次迭代，$var会包含列表中当前值。
for读取列表中复杂值，如单引号，空格等需要转义的值，默认情况下，shell会将空格、制表符和换行符作为字段分割符
- [ ] 使用转义符（\）将单引号转义
- [ ] 使用双引号来定义用到单引号的值
```
#!/bin/bash
for test in I don't know if this'll work
do
	echo "word:$test"
done
输出：I
	 dont know if thisll
	 work
#转义符
#!/bin/bash
for test in I don\'t know if this\'ll work
do
	echo "word:$test"
done
输出：I
	 don't
	 know
	 if
	 thisll
	 work
#双引号
#！/bin/bash
for test in I "don't" know if "this'll" work
do
	echo "word:$test"
done
输出：I
	 don't
	 know
	 if
	 thisll
	 work
```
for从变量读取列表
```
#!/bin/bash
list="Alabama Alasks Arizona"
list=$list" Connecticut"
for state in $list
do
	echo "Have you ever visited $state?"
done
输出：Have you ever visited Alabama?
	 Have you ever visited Alasks?
	 Have you ever visited Arizona?
	 Have you ever visited Connecticut?
注：list=$list" Connecticut"为向list列表字符串尾部添加一个Connecticut字符串，从而list="Alabama Alasks Arizona Connecticut"
```
for读取命令输出
```
#!/bin/bash
# 将下面三个字符串写入到state文件
cat >>/root/states <<EOF
Alabama
Alaska
Arizona
EOF
# 开始for循环
file="/root/states"
for state in $(cat $file)
do
	echo "Visit beautiful $state"
done
输出：Visit beautiful Alabama
	 Visit beautiful Alaska
	 Visit beautiful Arizona
```
for用通配符读取目录，可以一次查找多个目录
```
#列出/dev目录下的普通文件和文件夹
#!/bin/bash
for file in /dev/*
do
	if [[ -d $file ]]
	then
		echo "$file is a directory"
	elif [[ -f $file ]]
	then 
		echo "$file is a file"
	fi
done
输出：/dev/block is a directory
	 /dev/core is a file
	 ......
#注：for语句首先使用了文件扩展匹配来遍历通配符生成的文件列表，该列表包含/dev文件夹下所有文件，然后它会遍历列表中的每一个文件，然后找出普通文件和文件夹。
```
C语言风格的for循环
基本格式
for (( variable assignment ; condition ; iteration process ))
for (( a = 1; a < 10; a++ ))
- [ ] 变量赋值可以有空格
- [ ] 条件中的变量不以$开头
- [ ] 可以使用多个变量

```
#!/bin/bash
for (( a=1, b=10; a <= 10; a++, b-- ))
do
	echo "$a -$b"
done
输出：1 - 10
	 2 - 9
	 ......
```
## 第八次整理
### 时间：2019年1月4日
### 主要内容：
while
while命令某种意义上是if-then语句和for的混杂体，while允许定义一个要测试的命令，只要定义的测试命令返回的退出状态码是0时，循环就执行一组命令，若测试命令返回非0退出状态码时，while命令停止执行。
基本语法
```
while test command
do
	other commands
done
```
while 命令的关键在于所指定的test command的退出状态码必须随着循环中运行的命令而改变，如果退出状态码不发生改变，while循环将一直不停的进行下去。如果在while语句行定义多个测试命令，每个测试命令都出现在单独的一行上，则只有最后一个测试命令的退出状态码会被用来决定什么时候结束循环。
```
#!/bin/bash
var1=10
while echo $var1
	[ $var1 -ge 0 ] 
	#$var1为0时，[ $var1 -ge 0 ]成立，返回值为0，循环继续，执行[ $var1 -1 ]，此时$var1为-1，[ $var1 -ge 0 ]不成立，返回值非0，循环终止。
do
	echo "This is inside the loop"
	var1=$[ $var1 -1 ]
done
输出：10
	 This is inside the loop
	 ......
注：第一个命令为读取$var1的值，第二个命令为判断$var1是否大于等于0，当第二个命令的退出状态码为非0时，第二条命令停止执行，循环终止。
```
until命令
until命令和while命令工作方式完全相反，until命令要求指定一个通常返回非0退出状态码的测试命令。只有退出状态码不为0时，shell才会执行循环中列出的命令。如果在until语句行定义多个测试命令，每个测试命令都出现在单独的一行上，则只有最后一个测试命令的退出状态码会被用来决定什么时候结束循环。
基本语法
```
until test commands
do
	other commands
done
```
```
#!/bin/bash
var1=100
until echo $var1
	[ $var1 -eq 0 ]
	#当var1=25时，[ $var1 -eq 0 ]不成立，返回值不为0，执行[ $var1 -25 ]，当var1=0时，[ $var1 -eq 0 ]成立，返回值为0，命令退出，循环终止。
do
	echo Inside the loop: $var1
	var1=$[ $var1 -25 ]
done
输出：100
	 Inside the loop: 100
	 ......
```
echo输出字符串总结
符号 |能否引用常规字符串 | 能否引用转义符 | 能否引用文本格式符（换行符、制表符）
:-----: | :-----: | :-----: | :------:
单引号 |能 | 否 | 否 | 否
双引号 | 能 | 能 | 能 | 能
无引号 | 能 | 能 | 能 | 否

嵌套循环
循环语句可以在循环中使用任意命令，包括其他循环命令，当循环中包含其他循环命令时，改循环为嵌套循环，需要注意的是，嵌套循环中，是在迭代中使用迭代，与命令运行的次数是乘积关系。
被嵌套的循环称为内部循环，会在外部循环的每次迭代中遍历一次它所有的值，需要注意的是，两个循环中do和done命令没有任何区别，shell会把第一个done命令作为内部循环的结束。
```
#!/bin/bash
var1=5
while [ $var1 -ge 0 ]
do
	echo "output loop: $var1"
	for (( var2 = 1; $var2 < 3; var2++ ))
	do
		var3=$[ $var1 * $var2 ]
		echo "Inner loop: $var1 * $var2 = $var3"
	done
	var1=$[ $var1 - 1 ]
done
输出：output loop: 5
	 Inner loop: 5 * 1 = 5
	 Inner loop: 5 * 2 = 10
	 ......
```
## 第九次整理
### 时间：2019年1月5日
### 主要内容：
环境变量
Shell的环境变量分为set和env两种，其中set变量可以通过export工具导入到env变量中，set 显示本地shell变量，仅在本 shell 中有效；env 显示设置用户环境变量，仅在当前会话中有效。这两种变量不同之处在于变量的作用域不同，env 变量的作用域要大些。

按生命周期划分：
永久的：需要用户修改/etc/profile配置文件，变量永久生效
临时的：用户利用export命令，在当前终端下声明环境变量，关闭shell终端失效

按作用域划分：
系统环境变量：系统环境变量对该系统中所有用户都有效
用户环境变量：环境变量只对特定的用户有效

设置方法（添加apache变量）：
添加系统环境变量
vim编辑profile文件，然后在export PATH后另起一行输入export PATH=$PATH:/usr/local/apache/bin，保存退出，然后执行source /etc/profile使变量立即生效。
```
vim /etc/profile
#以下为profile文件内容
# /etc/profile: system-wide .profile file for the Bourne shell (sh(1))
# and Bourne compatible shells (bash(1), ksh(1), ash(1), ...).

if [ "`id -u`" -eq 0 ]; then
  PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
else
  PATH="/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games"
fi
export PATH
......
#以下为需要添加和执行的内容
export PATH=$PATH:/usr/local/apache/bin
source /etc/profile
```
添加用户环境变量
```
vim ~/.bash_profile
#以下为.bash_profile文件内容
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/bin

export PATH
#以下为需要添加和执行的内容
export PATH=$PATH:/usr/local/apache/bin
#或者直接添加到PATH后面，中间有分号隔开
PATH=$PATH:$HOME/bin:/usr/local/apache/bin
source ~/.bash_profile
```
直接运行export定义变量，只对当前shell有效，shell关闭，变量失效
```
export PATH=$PATH:/usr/local/apache/bin
#注：$PATH是读取当前环境变量，故export PATH=$PATH:/usr/local/apache/bin等同于PATH=$PATH:$HOME/bin:/usr/local/apache/bin
```
其他命令
echo $PATH显示当前环境变量
env 显示所有环境变量
set 显示本地定义的shell变量
unset HELLO 清除环境变量HELLO
readonly HELLO 设置只读环境变量HELLO

IFS环境变量（内部域分隔符）
默认情况下，shell将空格符、制表符(TAB)和换行符作为字段分隔符，遇到这些字符时，shell会认为字符后是一个新的字段，当需要处理包含空格符、制表符(TAB)和换行符的字段时就需要通过改变IFS的值来改变分隔符。
IFS 是一种set变量，当shell处理 处理包含空格符、制表符(TAB)和换行符的字段时，shell根据 IFS的值来拆解读入的变量，然后对特殊字符进行处理，最后重新组合赋值给该变量。
```
#!/bin/bash
list="we are anonymoys"
for str1 in $list
do
	echo $str1
done
输出：we
	 are
	 anonymous

#!/bin/bash
list="we are anonymoys"
ifs_old=$IFS
IFS=$'\n'
for str1 in $list
do
	echo $str1
done
IFS=$ifs_old
输出：we are anonymoys
#注：每次设置IFS前，保存一下当前IFS，用完之后，恢复原来的值。
```
修改IFS环境变量，循环处理文件数据
如处理/etc/passwd文件中的数据，要求逐行遍历/etc/passwd文件，并将IFS变量的值改成冒号，这样才能分开每行中的各个数据段。
```
#!/bin/bash
ifs_old=$IFS
IFS=$'\n'
for str1 in $(cat /etc/passwd)
do
	echo "Values in $str1 -"
	IFS=$':'
	for str2 in $str1
	do
		echo "$str2"
	done
done
IFS=$ifs_old
输出：Values in root:x:0:0:root:/root:/bi -
	 root
	 x
	 0
	 0
	 root
	 /root
	 /bi
	......
```
## 第十次整理
### 时间：2019年1月6日
### 主要内容：
循环控制
break命令可以退出任意类型的循环，包括while和until循环
跳出单个循环
```
#!/bin/bash
for var1 in 1 2 3 4 5 6 7
do
	if [ $var1 -eq 5 ]
	then
		break
	fi
	echo "number: $var1"
done
echo "The for loop is completed"
输出：number: 1
	 ......
	 number: 5
	 The for loop is completed
```
跳出内部循环
当变量b的值等于5时，执行break命令终止内部循环。break接受单个命令行参数值，break n ，其中n指定了要跳出循环层级，默认情况下，n=1，表明跳出当前循环，n=2时，break命令会停止下一级的外部循环。
```
#!/bin/bash
for (( a = 1; a < 4; a++ ))
do
	echo "outer loop: $a"
	for (( b = 1; b < 100; b++ ))
	do
		if [ $b -eq 5 ]
		then
			break
			#break 2
		fi
		echo "Inner loop: $b"
	done
done
输出：outer loop: 1
		Inner loop: 1
		......
		Inner loop: 4
	......
```
continue命令可以提前终止某次循环，但并不会完全终止整个循环，只会终止shell循环内部符合条件的部分。
```
#!/bin/bash
for (( var1 = 1; var1 < 10; var1++ ))
do
	if [ $var1 -gt 2 ] && [ $var1 -lt 9 ]
	#判断var1是否大于2且小于9，是则执行continue
	then
		continue
	fi
	echo "Interation number：$var1"
done
输出：Interation number:1
	 Interation number:2
	 Interation number:9
```
continue接受单个命令行参数值，continue n ，其中n指定了要跳出循环层级，默认情况下，n=1，表明跳出当前循环，n=2时，break命令会停止下一级的外部循环。
```
#!/bin/bash
for (( a = 1; a <= 5; a++ ))
do
	echo "Internation $a:"
	for (( b = 1; b < 3; b++ ))
	do
		if [ $a -gt 2 ] && [ $a -lt 4 ]
		then
			continue 2
        fi
		var3=$[ $a * $b ]
		echo "The result of $a * $b = $var3"
	done
done
输出：Internation 1:
	 The result of 1 * 1 = 1
	 The result of 1 * 2 = 2
	 Internation 2:
	 The result of 2 * 1 = 2
	 The result of 2 * 2 = 4
	 Internation 3:
	 Internation 4:
	 The result of 4 * 1 = 4
	 The result of 4 * 2 = 8
#注：只有值为3的那次迭代没有处理任何内部循环语句，尽管continue命令停止了处理过程，但外部循环依然会继续
	 ......
```
处理循环的输出
shell脚本中，可以对循环的输出使用管道或进行重定向，可以通过done命令之后添加处理命令来实现
```
#将for循环输出的结果重定向到output.txt文件中
#!/bin/bash
for file in /dev/*
do
	if [[ -d $file ]]
	then
		echo "$file is a directory"
	elif
		echo "$file is a file"
	fi
done > output.txt
```
小结
1. for命令允许你遍历一系列的值，不管是在命令行里提供好的、包含在变量中的还是通过文件扩展匹配获得的文件名和目录名。
2. while命令使用普通命令或测试命令提供了基于命令条件的循环。只有在命令（或条件）产生退出状态码0时，while循环才会继续迭代指定的一组命令。
3. until命令也提供了迭代命令的一种方法，但它的迭代是建立在命令（或条件）产生非零退出状态码的基础上。这个特性允许你设置一个迭代结束前都必须满足的条件。
4. 可以在shell脚本中对循环进行组合，生成多层循环。bash shell提供了continue和break命令，允许你根据循环内的不同值改变循环的正常流程。
5. shell还允许使用标准的命令重定向和管道来改变循环的输出。你可以使用重定向来将循环的输出重定向到一个文件或是另一个命令。


## 第十一次整理
### 时间：2019年1月7日
### 主要内容：

处理用户输入
前面以及初步了解了如何编写脚本，处理数据、变量和Linux系统上的文件。有时编写的脚本还得能够与使用者进行交互。bash shell提供了一些不同的方法来从用户处获得数据，包括命令行参数（添加在命令后的数据）、命令行选项（可修改命令行为的单个字母）以及直接从键盘读取输入的能力
命令行参数
读取参数
shell会将一些称为位置参数的特殊变量分配给输入到命令行中的所有参数，位置参数变量是标准的数字：$0是程序名，$1是第一个参数，$2是第二个参数，以此类推，如果脚本需要的命令行参数不止9个，你仍然可以处理，但是需要稍微修改一下变量名。在第9个变量之后，你必须在变量数字周围加上花括号，比如${10}。
可以在shell脚本中像使用其他变量一样使用$1变量。shell脚本会自动将命令行参数的值分配给变量，不需要作任何处理。如果需要输入更多的命令行参数，则每个参数都必须用空格分开
```
#!/bin/bash
factorial=1
for (( number = 1; number <= $1; number++ ))
do
	factorial=$[ $factorial * $number ]
done
echo The factorial of $1 is $factorial
#注：将以上代码保存到test.sh文件中然后执行：./test.sh 5
输出：The factorial of 5 is 120

#!/bin/bash
total=$[ $1 * $2 ]
echo The first parameter is $1.
echo The second parameter is $2.
echo The total value is $total.
#注：将以上代码保存到test2.sh中，然后执行./test2.sh 2 5
输出：The The first parameter is 2.
	 The second parameter is 5.
	 The total value is 10.
```
在命令行上用文本字符串作为参数，若参数中有空格，则必须使用引号（单引号双引号均可）
```
#!/bin/bash
echo Hello $1, glad to meet you.
#注：将以上字符串保存到test3.sh，然后执行./test3.sh 'tom jobs'
输出：Hello tom jobs,glad to meet you.
```
读取脚本名
可以使用$0参数获取shell在命令行启动的脚本名，当传给$0变量的实际字符串不仅仅是脚本名，而是完整的脚本路径时，变量$0就会使用整个路径。basename命令会返回不包含路径的脚本名
```
#!/bin/bash
echo The zero parameter is set to: $0
输出：The zero parameter is set to: ./test4.sh

#!/bin/bash
name=$(basename $0)
echo The script name is: $name
输出：The script name is: test4.sh
```
测试参数
在shell脚本中使用命令行参数时要小心些。如果脚本不加参数运行，可能会出问题
```
#!/bin/bash
if [[ -n $1 ]]
#使用-n测试来检查命令行参数$1中是否有数据
then
	echo Hello $1, glad to meet you.
else
	echo "sorry, you did not identify yourselt."
#注：将以上代码保存到test5.sh
执行./test5.sh jobs
输出：Hello jobs, glad to meet you.
执行./test5.sh
输出：sorry, you did not identify yourselt.
```

## 第十二次整理
### 时间：2019年1月8日
### 主要内容：

获取用户输入
read命令从标准输入（键盘）或另一个文件描述符中接收输入，在收到输入后，read命令会将数据放进一个变量。

```
#!/bin/bash
echo -n "Enter your name: "
#read -n选项不会在字符末尾输出换行符，允许脚本用户紧跟其后输入数据，而不是下一行。
read name
echo "Hello $name, welcome to my program."
输出：Enter your name: 
输入：jobs
输出：Hello jobs, welcome to my program.
```
read -p允许直接在read命令行指定提示符，read命令行可以指定多个变量，若不指定变量，则read接收的所有数据都存放到特殊环境变量REPLY中，调用时直接使用$REPLY即可。
```
#!/bin/bash
read -p "Please enter your age: " age
days=[ $age * 365 ]
echo "That makes you over $days days old!"
输出：Please enter your age: 
输入：10
输出：That makes you over 3650 dyys old!
```
设置超时
使用read命令时，若用户没有及时输入数据，则脚本会一直处于等待状态，此时可以用-t选项来指定一个计时器。-t选项指定了read命令等待输入的秒数，当计时器过期后，read命令会返回一个非零退出状态码。
也可以不对输入过程计时，而是让read命令来统计输入的字符数。当输入的字符达到预设的字符数时，就自动退出，将输入的数据赋给变量。
```
#!/bin/bash
read -n1 -p "Do you Want to continue [Y/N]?" answer
case $answer in
y | Y ) echo
		echo "fine, continue on ...";;
n | N ) echo
		echo OK， goodbye
		exit;;
esac
echo "This is the end of the script"
```
隐藏方式读取（如输入密码）
-s选项可以避免在read命令中输入的数据出现在显示器上（实际上，数据会被显示，只是read命令会将文本颜色设成背景色一样）
```
#!/bin/bash
read -s -P "Enter your password:" pass
echo
echo "Is your password really $pass?"
#执行该脚本，会输出前面输入隐藏的密码
```
read从文件中读取参数
read命令来读取Linux系统上文件里保存的数据。每次调用read命令，它都会从文件中读取一行文本。当文件中再没有内容时，read命令会退出并返回非零退出状态码。其中最难的部分是将文件中的数据传给read命令，最常见的方法是对文件使用cat命令，将结果通过管道直接传给含有read命令的while命令，或者使用重定向读取文件内容。
```
#cat方法
#!/bin/bash
count=1
cat test | while read line
do
	echo "Line $count: $line"
	count=$[ $count + 1 ]
done
echo "Finished processing the file"

#重定向方法
#!/bin/bash
count=1
while read line
do
	echo "Line $count: $line"
	count=$[ $count + 1 ]
done < test.sh
echo "Finished processing the file"
```
调试脚本
```
#bash -x script.sh
```
使用-x选项，启动追踪调试shell脚本，运行带有-x标志的脚本能打印出所执行的每一行命令及当前的状态。
-x标志的脚本执行过程中的每一行都输出到stdout，若只需关注脚本某些部分的命令及参数打印输出，可以在脚本中使用set built-in来启动或禁止调试打印
- [ ] set -x： 在执行时显示参数和命令
- [ ] set +x：禁止调试
- [ ] set -v：当命令进行读取时显示输入
- [ ] set +v：禁止打印输出

```
#!/bin/bash
for i in {1..6}
do
set -x
	echo $i
set +x
done
echo "script executed"
输出：+ echo 1
	 1
	 + set +x
	 + echo 2
	 2
```
以上代码，仅在-x和+x所限制的区域内，echo $i 的调试信息才会被打印出来
补充内容
调试脚本是可以巧妙的利用shebang来进行调试，将#!/bin/bash改成#!/bin/bash -xv，如此不用任何选项就可以启用调试功能。

## 第十三次整理
### 时间：2019年1月9日
### 主要内容：

函数
创建函数，函数不一定非在脚本的开始，但必须先定义后引用。
- [ ] 采用关键字function
function name {
    commands
}
name属性定义了赋予函数的唯一名称，脚本中定义的每个函数都必须有一个唯一的名称。
commands是构成函数的一条或多条shell命令，调用改函数时，shell会按命令在函数中出现的顺序依次执行。
- [ ] 函数名后跟空括号
name () {
    commands
}
```
#!/bin/bash
function func1 { 
	echo "This is an example of a function"
}

count=1
while [ $count -le 2 ]
do
	func1
	count=$[ $count + 1 ]
done
echo "This is the end of the script"
输出：This is an example of a function
	 This is an example of a function
	 This is the end of the script
```
函数返回值
shell会把函数当做一个小脚本，运行结束时会返回一个退出状态码
默认情况下，函数的退出状态码是函数最后一条命令返回的退出状态码，在函数执行结束时后，可以用标准变量$?来确定函数的退出状态码。
```
#!/bin/bash

func1() {
    echo "trying to display a non-existent file"
    ls -l badfile
}

echo "testing the function: "
func1
echo "The exit status is: $?"
输出：trying to display a non-existent file
	 ls: badfile: No such file or directory
	 The exit status is: 1
```
函数的退出状态码是1，因为函数中的最后一条命令没有运行成功，但无法知道函数中其他命令是否成功，所以使用函数默认退出状态码是危险的。
使用return设置特定的退出状态码
- [ ] 函数一结束就取返回值
- [ ] 退出状态码必须是0-255，否则会返回错误值。
```
#!/bin/bash

function db1 {
    read -p "Enter a value: " value
    echo "doubling the value"
    return $[ $value * 2 ]
    
}

db1
echo "The new value is $?"
```
使用函数输出值
正如可以命令的输出保存到shell变量一样，也可以对函数的输出采用同样的处理办法，可以用此获取任何类型的函数输出，并将其保存到变量中。
将db1函数的输出赋值给$result变量
```
#!/bin/bash
function db1 {
    read -p "Enter a value: " value
    echo $[ $value * 2 ]
}

result=$(db1)
echo "The new value is $result"
输出：Enter a value：200
	 The new  value is $result
```
在函数中使用变量
向函数传递参数，函数可以用参数变量来获取参数值。
```
#!/bin/bash
function addem {
    if [ $# -eq 0 ] || [ $# -gt 2 ]
    # $#是特殊变量，用于统计命令行携带参数的个数
    then
    	echo -1
    elif [ $# -eq 1 ]
    then
    	echo $[ $1 + $1 ]
    else
    	echo $[ $1 + $2 ]
    fi
}
echo -n "Adding 10 and 15:"
value=$(addem 10 15)
echo $value
echo -n "Let's try addingg just one number:"
value=$(addem 10)
echo -n "Now trying adding no number:"
value=$(addem)
echo $value
echo -n "Finally, try adding tree numbers:"
value=$(addem 10 15 20)
echo $value
```
脚本中的addem函数首先会检测脚本传给它的参数数目，若没有任何参数，或者参数多于两个，addem会返回值-1，若只有一个参数，addem会将参数与自身相加，若有两个参数，addem会将他们进行相加。
补充：
$#为统计命令行携带参数的个数，$\*和$@可以用来所有的参数,这两个变量都能够在单个变量中存储所有的命令行参数。
$\*变量会将命令行上提供的所有参数当作一个单词保。这个单词包含了命令行中出现的每一个参数值，基本上$\*变量会将这些参数视为一个整体，而不是多个个体。
$@变量会将命令行上提供的所有参数当作同一字符串中的多个独立的单词。这样就能够遍历所有的参数值，得到每个参数。

```
#!/bin/bash
# script name: test.sh
# testing $* and $#
echo
count=1
for patam in "$*"
do
	echo "\$* Parameter #$count = $param"
	count=$[ $count + 1 ]
done
echo
count=1
for param in "$@"
do
	echo "\$@ Parameter #$count = $param"
	count=$[ $count + 1 ]
done
运行./test.sh root jobs andy
输出：$* Parameter #1 = root jobs andy
	 $@ Parameter #1 = root
	 $@ Parameter #1 = jobs
	 $@ Parameter #1 = andy
#注：$*变量会将所有参数当成单个参数，而$@变量会单独处理每个参数
```
## 第十四次整理
### 时间：2019年1月10日
### 主要内容：

在函数中处理变量
shell脚本麻烦之一是变量的作用域，作用域是变量可见的区域，函数中定义的变量与普通变量的作用域不同，简单说就是，对脚本的其他部分而言，它们是隐藏的。
函数中的两种类型变量：
- [ ] 全局变量
- [ ] 局部变量

全局变量
全局变量是在shell脚本中任何地方都有效的变量，若在脚本的主体部分定义了一个全局变量，那么可以在函数内读取它的值，类似的，若在函数内定义一个全局变量，则可以在脚本的主体部分读取它的值。
默认情况下，在脚本中定义的任何变量都是全局变量，函数外定义的变量，函数内可以正常访问。

```
#!/bin/bash
function db1 {
    value=$[ $value * 2 ]
}
read -p "Enter a value:" value
db1
echo "The new value is: $value"
输出：Enter a value: 450
	 The new value is: 900
```
$value变量在函数外定义并被赋值。当dbl函数被调用时，该变量及其值在函数中都依然有
效。如果变量在函数内被赋予了新值，那么在脚本中引用该变量时，新值也依然有效。

局部变量
无须在函数中使用全局变量，函数内部使用的任何变量都可以被声明为局部变量，只需在变量声明的前面加上local关键字即可，也可以在变量赋值语句中使用local关键字（local temp=$[ $value + 5 ]）。
local关键字保证了变量只局限在该函数中，若脚本中该函数之外有同样名字的变量，shell会保持这两个变量的值是分离的，可以将函数变量和脚本变量隔离开。
```
#!/bin/bash
#定义函数局部变量temp和全局变量result
function func1 {
    local temp=$[ $value + 5 ]
    #此时temp为局部变量值
    result=$[ $temp * 2 ]
}

temp=4
value=6

func1
echo "The result is $result"
if [ $temp -gt $value ]
#此时temp为全局变量值
then
	echo "temp is larger"
else
	echo "temp is smaller"
fi
输出：The result is 22
	 temp is smaller
```
数组
数组中可以存放多个值，shell只支持一维数组，初始化时无需定义数组大小，数组元素的下标由0开始。
数组用括号来表示，元素用空格符号分隔开
定义数组
```
my_array=(A B "C" D)
或
array_name[0]=value0
array_name[1]=value1
array_name[2]=value2
```
读取数组
```
${array_name[index]}
#使用@或*获取数组中所有元素
${array_name[*]}
${array_name[@]}
```
使用#获取数组长度
```
${#array_name[*]}
${#array_name[@]}
```
使用!获取数组下标
```
array=('a' 'b' 'c')
for i in ${!array[@]}
do
	echo $1
done
输出：0
	 1
	 2
```
数组变量和函数
将数组变量作为单个参数传递的话不会起作用，若将数组变量作为函数参数，函数只会去数组变量的第一个值
```
#!/bin/bash
function testit {
 	#把获取到的变量全部输出
    echo "The parameters are: $@"
    #获取第一个参数，即将整个数组作为一个参数传递给函数
    thisarrary=$1
    #读取thisarrary数组中的所有值
    echo "The received arrary is ${thisarrary[*]}"
}

myarrary=(1 2 3 4 5)
echo "The original arrary is: ${myarrary[*]}"
#将数组作为参数传递给函数
testit $myarrary
输出：The original arrary is: 1 2 3 4 5
	 The parameters are: 1
	 The received arrary is 1
```
要解决这个问题，必须将该数组变量的值分解成单个的值，然后将这些值作为函数参数使用。在函数内部，可以将所有的参数重新组合成一个新的变量
```
#!/bin/bash
function testit {
	#定义局部变量
    local newarray
    #读取数组中的所有数值，并保存到数组中
    newarray=($(echo "$@"))
    echo "The new array value is: ${newarray[*]}
}
myarray=(1 2 3 4 5)
echo "The original array is ${myarray[*]}"
testit ${myarray[*]}
输出：The original array is 1 2 3 4 5 
	 The new array value is: 1 2 3 4 5
#注：该脚本用$myarray变量来保存所有的数组元素，然后将它们都放在函数的命令行上。该函数随后从命令行参数中重建数组变量。在函数内部，数组仍然可以像其他数组一样使用。
```
从函数返回数组
函数用echo语句来按正确顺序输出单个数组值，然后脚本再将它们重新放进一个新的数组变量中
```
#!/bin/bash
function arrarydblr {
    local origarray
    local newarray
    local elements
    local i
    origarray=($(echo "$@"))
    newarray=($(echo "$@"))
    #获取数组的长度，因为数字下标是从0开始，所以要减1
    elements=$[ $# -1 ]
    for (( i = 0; i <= $elements; i++ ))
    {
    	#将origarray数组的值单个取出来进行运算，然后添加到newarray数组中
        newarray[$i]=$[ ${origarray[$i]} * 2 ]
    }
    echo ${newarray[*]}
}

myarray=(1 2 3 4 5)
echo "The original array is: ${myarray[*]}"
#将myarray数组中的值取出，放入arg1变量
arg1=$(echo ${myarray[*]})
#arg1变量将参数传递给arraydblr函数
result=($(arraydblr $arg1))
echo "The new array is: ${result[*]}"
输出：The original array is: 1 2 3 4 5
	 The new array is: 2 4 6 8 10
```
该脚本用$arg1变量将数组值传给arraydblr函数。arraydblr函数将该数组重组到新的数组变量中，生成该输出数组变量的一个副本。然后对数据元素进行遍历，将每个元素值翻倍，并将结果存入函数中该数组变量的副本。
arraydblr函数使用echo语句来输出每个数组元素的值。脚本用arraydblr函数的输出来重新生成一个新的数组变量。

# **Markdown操作手册**
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
[百度链接](https://www.baidu.com)

>Github 为软件开发者提供项目管理，代码托管

> 这是第一级引用
>
> > 这是第二级引用

> ## 这是第一个标题
> 1.这是第一行列表项   
> 2.这是第二行列表项   
>retuen shell_exec(`echo $input | $markdown_script`);

- Red
- Green
- Blue


1. Red
2. Green
3. Blue


* Reg
* Green
* Blue

* Github 有以下功能
  > 代码托管平台    
  > 在线运行环境

- [ ] 不勾选
- [x] 勾选

```
require 'redcarpet'
markdown = redcarpet.new("Hello World")
```

*斜体*   
**加粗**        
***加粗斜体***   
~~删除线~~   
***

----

First Header | Second Header | Third Header
:------------| :-----------: | -----------:
Left         | Center        | Right
Left         | Center        | Right

![结束语](http://pic1.win4000.com/mobile/2019-01-10/5c36e1a2875e1.jpg "美女镇楼")

```

```