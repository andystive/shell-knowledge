# shell 编程
## 第一次整理
#### 时间：2018年12月28日
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

关系运算符：只支持数字不支持字符串
1. -eq：检测两个数是否相等，相等返回true
2. -ne：检测两个数是否不相等，不相等返回true
3. -gt：检测左边的数是否大于右边的，是则返回true
4. -lt：检测左边的数是否小于右边的，是则返回true
5. -ge：检测左边的数是否大于等于右边的，是则返回true
6. -le：检测左边的数是否小于等于右边的，是则返回true

```
#判断当前用户是否为root
if [ $UID -ne 0 ];then
    echo Not root user.
else
    echo Root user.
fi
```













# **Markdown操作手册**
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
[访问连接](https://www.baidu.com)
>Github 为软件开发者提供项目管理，代码托管

> 这是第一级引用
>> 这是第二级引用

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
---

First Header | Second Header | Third Header
:------------| :-----------: | -----------:
Left         | Center        | Right
Left         | Center        | Right

![结束语](http://pic1.win4000.com/mobile/2018-12-18/5c1884528d25c.jpg "美女镇楼")
