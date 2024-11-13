---
title: "Linux Shell编程学习"
date: 2024-11-8
---

# 2024 年 11 月 8 日

## 第 1 章 Shell 概述

Linux 提供的 Shell 解析器有

```
[root@localhost ~]# cat /etc/shells
/bin/sh
/bin/bash
/usr/bin/sh
/usr/bin/bash
```

查看系统默认 Shell 解析器

```
[root@localhost ~]# echo $SHELL
/bin/bash
```

## 第 2 章 Shell 脚本入门

1）脚本格式

脚本以`#!/bin/bash`开头（指定解析器）

2）脚本常用的执行方式

第一种：`sh 脚本路径`或`bash 脚本路径`

```
sh ./hello.sh
bash ./hello.sh
```

第二种：直接输入脚本路径

此方式需要给脚本添加`可执行`权限：chmod +x hello.sh

```
./hello.sh
```

第三种【了解】：在脚本的路径前加上“.”或者 source

```
[root@localhost scripts]# . hello.sh
hello, world
[root@localhost scripts]# source hello.sh
hello, world
```

总结：第一种和第二种会启用子 shell 执行命令，第三种不会启用子 shell，直接在当前 shell 执行命令。

## 第 3 章 变量

### 3.1 系统预定义变量

1）常用系统变量

$HOME、$PWD、$SHELL、$USER 等

```
[root@localhost scripts]# echo $HOME
/root
[root@localhost scripts]# echo $PWD
/root/scripts
[root@localhost scripts]# echo $USER
root
[root@localhost scripts]# echo $SHELL
/bin/bash
```

查看所有的系统全局变量：env

```
[root@localhost scripts]# env
```

### 3.2 自定义变量

1）基本语法：

（1）定义变量：变量名=变量值，==注意：=号前后不能有空格==

定义局部变量：

```
###########################################数字类型
#定义变量
[root@localhost scripts]# a=2

#查看变量
[root@localhost scripts]# echo $a
2

###########################################字符串类型
[root@localhost scripts]# b="123 123"
[root@localhost scripts]# echo $b
123 123
```

定义全局变量：

```
[root@localhost scripts]# export my_var
[root@localhost scripts]# echo $my_var
```

数字运算：

```
#第一种
[root@localhost scripts]# a=$((1+5))
[root@localhost scripts]# echo $a
6

#第二种
[root@localhost scripts]# a=$[5+9]
[root@localhost scripts]# echo $a
14
```

定义只读变量：

只能读不能改，不能被撤销

```
[root@localhost scripts]# readonly b=5
[root@localhost scripts]# b=10
-bash: b: readonly variable
[root@localhost scripts]# echo $b
5
```

撤销变量：

```
[root@localhost scripts]# echo $a
```

2）变量定义规则

（1）变量名称可以有字符、数字和下划线组成，但是不能以数字开头，环境变量名建议大写。

（2）等号两侧不能有空格

（3）在 bash 中，变量默认类型都是字符串类型，无法直接进行数值运算。

（4）变量的值如果有空格，需要使用双引号或单引号括起来。

### 3.3 特殊变量

#### 3.3.1 $n

1）基本语法

$n（功能描述：n为数字，$0代表脚本名称，$1-$9代表第一到第九个参数，十以上的参数需要使用大括号包含，如${10}）

2）案例实操

定义脚本：parameter.sh

```
#!/bin/bash
echo '===============$n============'
echo script name: $0
echo lst parameter: $1
echo 2nd parameter: $2
```

执行脚本：

```
[root@localhost scripts]# ./parameter.sh abc def
===============$n============
script name: ./parameter.sh
lst parameter: abc
2nd parameter: def
```

#### 3.3.2 $#

1）基本语法

$#（功能描述：获取所有==输入参数个数==，常用于循环，判断参数的个数是否正确以及加强脚本的健壮性）。

2）案例实操

定义脚本：

```
echo '===============$#============'
echo parameter numbers: $#
```

执行脚本：

```
./parameter.sh abc def
```

#### 3.3.3 $*、$@

1）基本语法

$*（功能描述：这个变量代表命令行中所有的参数，==$\*把所有的参数看成一个整体==）

$@（功能描述：这个变量也代表命令行中所有的参数，不过==$@把每个参数区分对待==）

2）案例实操

定义脚本：

```
echo '===============$*============'
echo $*
echo '===============$@============'
echo $@
```

执行脚本：

```
./parameter.sh abc def
```

#### 3.3.4 $?

1）基本语法

$?（功能描述：最后一次执行的命令的返回状态。如果这个变量的值为 0，证明上一个命令正确执行；如果这个变量的值为非 0（具体是哪个数，有命令自己来决定），则证明上一个命令执行不正确了。）

2）案例实操

```
[root@localhost scripts]# ./parameter.sh abc def
===============$n============
script name: ./parameter.sh
lst parameter: abc
2nd parameter: def
===============$#============
parameter numbers: 2
===============$*============
abc def
===============$@============
abc def
[root@localhost scripts]# echo $?
0
[root@localhost scripts]
```

## 第 4 章 运算符

1）基本语法

“$((运算式))” 或 “$[运算式]”

2）案例实操

```
#第一种
[root@localhost scripts]# a=$((1+5))
[root@localhost scripts]# echo $a
6

#第二种
[root@localhost scripts]# a=$[5+9]
[root@localhost scripts]# echo $a
14

[root@localhost scripts]# s=$[(2+3)*4]
[root@localhost scripts]# echo $s
20
```

（实战）计算两个数的和

定义脚本：add.sh

```
#!/bin/bash
sum=$[$1+$2]
echo sum=$sum
```

执行脚本

```
#添加执行权限
[root@localhost scripts]# chmod +x add.sh
#执行脚本
[root@localhost scripts]# ./add.sh 1 2
sum=3
```

命令替换：

把执行的命令替换成值，然后再进一步的使用

```
#第一种方式
[root@localhost scripts]# a=$(expr 5 \* 2)
[root@localhost scripts]# echo $a
10

#第二种方式
[root@localhost scripts]# a=`expr 5 \* 2`
[root@localhost scripts]# echo $a
10
```

## 第 5 章 条件判断

1）基本语法

（1）test condition

（2）[ condition ]（==注意 condition 前后要有空格==）

注意：条件非空即为 true，[atguigu]返回 true，[ ]返回 false

2）常用判断条件

（1）两个整数之间比较

-eq 等于（equal） -ne 不等于（not equal）

-lt 小于（less than） -le 小于等于（less equal）

-gt 小于（greater than） -ge 小于等于（greater equal）

注：如果是字符串之间的比较，用等号“=”判断相等；用“!=”判断不等。

（2）按照文件权限进行判断

-r 有读的权限（read）

-w 有写的权限（write）

-x 有执行的权限（execute）

（3）按照文件类型进行判断

-e 文件存在（existence）

-f 文件存在并且是一个常规的文件（file）

-d 文件存在并且是一个目录（directory）

3）案例实操

实操 1：

使用标准方式：test $a=hello

```
[root@localhost scripts]# a=hello
[root@localhost scripts]# echo a
a
[root@localhost scripts]# echo $a
hello
[root@localhost scripts]# test $a=hello
[root@localhost scripts]# echo $?
0
```

实操 2：

使用简写方式：[ $a = hello ]

```
[root@localhost scripts]# [ $a = hello ]
[root@localhost scripts]# echo $?
0
```

实操 3：

数字之间比较

```
#等于比较
[root@localhost scripts]# [ 2 = 8 ]
[root@localhost scripts]# echo $?
1
#小于比较
[root@localhost scripts]# [ 2 -lt 8 ]
[root@localhost scripts]# echo $?
0
```

实操 4：

判断文件是否有可读的权限：

```
[root@localhost scripts]# touch test
[root@localhost scripts]# [ -r hello.sh ]
[root@localhost scripts]# echo $?
0
```

实操 5：

判断某一个文件是否存在：

```
[root@localhost scripts]# [ -e aaa ]
[root@localhost scripts]# echo $?
1
```

实操 6：

多条件判断（&&表示前一条命令执行成功时，才执行后一条命令，||表示上一条命令执行失败后，才执行下一条命令），有点类似 java 中的三元运算符。

```
[root@localhost scripts]# a=15
[root@localhost scripts]# [ $a -lt 20 ] && echo "$a < 20" || echo "$a >= 20"
15 < 20
[root@localhost scripts]# a=27
[root@localhost scripts]# [ $a -lt 20 ] && echo "$a < 20" || echo "$a >= 20"
27 >= 20
```

## 第 6 章 流程控制（重点）

### 6.1 if 判断

1）基础语法

（1）单分支

```
if [ 条件表达式 ];then
	程序
fi
```

或者

```
if [ 条件表达式 ]
then
	程序
fi
```

（2）多分支

```
if [ 条件表达式 ]
then
	程序
elif [ 条件表达式 ]
then
	程序
else
	程序
fi
```

（3）案例实操

案例 1：

命令行执行，单分支判断

```
[root@localhost scripts]# a=25
[root@localhost scripts]# if [ $a -gt 18 ]; then echo OK; fi
OK
[root@localhost scripts]# a=15
[root@localhost scripts]# if [ $a -gt 18 ]; then echo OK; fi
```

案例 2：

脚本执行，单分支判断

定义脚本：if_test.sh

```
#!/bin/bash

if [ "$1"x = "atguigu"x ]
then
        echo "welcome, atguigu"
fi
```

为了防止参数为空所以将参数改为了字符串对比并且添加了 x，至少保证参数有一个 x 值

执行脚本：

```
[root@localhost scripts]# chmod +x if_test.sh
[root@localhost scripts]# ./if_test.sh
[root@localhost scripts]# ./if_test.sh atguigu
welcome, atguigu
```

案例 3：

逻辑运算

```
[root@localhost scripts]# a=25
[root@localhost scripts]# if [ $a -gt 18 ] && [ $a -lt 35 ]; then echo OK; fi
OK
```

-a 代表逻辑与 &&

-o 代表逻辑或 ||

[ $a -gt 18 ] && [ $a -lt 35 ] 改写：[ $a -gt 18 -a $a -lt 35 ]

```
[root@localhost scripts]# if [ $a -gt 18 -a $a -lt 35 ]; then echo OK; fi
OK
```

案例 4：

if-else 语法

```
#输入第二个参数，表示年龄，判断属于哪个年龄段
if [ $2 -lt 18 ]
then
        echo "未成年人"
else
        echo "成年人"
fi
```

案例 5：

多分支

```
if [ $2 -lt 18 ]
then
        echo "未成年人"
elif [ $2 -lt 35 ]
then
        echo "青年人"
elif [ $2 -lt 60 ]
then
        echo "中年人"
else
        echo "老年人"
fi
```

### 6.2 case 语句

1）基本语法

```
case $变量名 in
"值1")
	如果变量的值等于值1，则执行程序1
;;
"值2")
	如果变量的值等于值2，则执行程序2
;;
	...省略其他分支...
*)
	如果变量的值都不是以上的值，则执行此程序
;;
esac
```

注意事项：

（1）case 行尾必须为单词”in“，每一个模式匹配必须以右括号”)“结束。

（2）双分号”;;“表示命令序列结束，相当于 java 中的 break。

（3）最后的"\*)"表示默认模式，相当于 java 中的 default。

2）案例实操

案例 1：

基础用法

```
#!/bin/bash

case $1 in
1)
        echo "one"
;;
2)
        echo "two"
;;
3)
        echo "three"
;;
*)
        echo "number else"
;;
esac
```

### 6.3 for 循环

1）基本语法 1

```
for (( 初始值;循环控制条件;变量变化 ))
do
	程序
done
```

2）案例实操

基础用法：从 1 加到 100

```
#!/bin/bash

for (( i=1; i <= $1; i++ ))
do
        sum=$[ $sum + $i ]
done
echo $sum
```

执行脚本：./sum_to.sh 100

3）基本语法 2

```
for 变量 in 值1 值2 值 3
do
	程序
done
```

4）案例实操

案例 1（命令行写法）：

基础用法

```
for os in linux windows macos; do echo $os; done
```

案例 2（命令行写法）：

从 1 加到 100

```
for i in {1..100}; do sum=$[$sum+i]; done; echo $sum
```

比较$*和$@区别：

$*和$@都表示传递给函数或脚本的所有参数，不被双引号“”包含时。

```
#################准备脚本
#!/bin/bash

echo '================$*==============='
for para in $*
do
        echo $para
done

echo '================$@==============='
for para in $@
do
        echo $para
done

#################执行命令
[root@localhost scripts]# ./parameter_for_test.sh a b c d e
================$*===============
a
b
c
d
e
================$@===============
a
b
c
d
e
```

被双引号”“包含时：

```
#################准备脚本
#!/bin/bash

echo '================$*==============='
for para in "$*"
do
        echo $para
done

echo '================$@==============='
for para in "$@"
do
        echo $para
done

#################执行命令
[root@localhost scripts]# ./parameter_for_test.sh a b c d e
================$*===============
a b c d e
================$@===============
a
b
c
d
e
```

### 6.4 while 循环

1）基本语法

```
while [ 条件判断式 ]
do
	程序
done
```

2）案例实操

从 1 加到 100

```
#用while做一个实现
a=1
while [ $a -le $1 ]
do
#       sum2=$[ $sum2 + $a ]
#       a=$[ $a+1 ]
        let sum2+=a
        let a++
done
echo $sum2
```

## 第 7 章 read 读取控制台输入

1）基本语法

read （选项)） （参数）

① 选项：

-p：指定读取值时的提示符；

-t：指定读取值时等待的时间（秒）如果-t 不加表示一直等待

② 参数

变量：指定读取值的变量名

2）案例实操

基础用法：提示 710 秒内，读取控制台输入的名称

```
#!/bin/bash

read -t 10 -p "请输入您的芳名：" name
echo "welcome, $name"
```

## 第 8 章 函数

### 8.1 系统函数

#### 8.1.1 basename

1）基础语法

basename [string / pathname] [suffix] （功能描述：basename 命令会删掉所有的前缀包括最后一个（‘/’）字符，然后将字符串显示出来。

basename 可以理解为取路径里的文件名称

选项： suffix 为后缀，如果 suffix 被指定了，basename 会将 pathname 或 string 中的 suffix 去掉。

2）案例实操

案例 1：

脚本中使用系统函数

```
#!/bin/bash

filename="$1"_log_$(date +%s)
echo $filename
```

案例 2：

去掉执行文件的前缀路径和后缀扩展名

```
#!/bin/bash
echo '===============$n============'
echo script name: $(basename $0 .sh)
```

#### 8.1.2 dirname

1）基本语法

dirname 文件绝对路径 （功能描述：从给定的包含绝对路径的文件名中去除文件名（非目录的部分），然后返回剩下的路径（目录的部分））

dirname 可以理解为取文件路径的绝对路径名称

2）案例实操

案例 1：

获取文件的路径

```
################脚本
#!/bin/bash
echo '===============$n============'
echo script name: $(basename $0 .sh)
echo script path: $(cd $(dirname $0); pwd)

################输出结果
[root@localhost scripts]# ./parameter.sh
===============$n============
script name: parameter
script path: /root/scripts
```

### 8.2 自定义函数

1）基本语法

```
[ function ] funname[()]
{
	Action;
	[return int;]
}
```

方括号的内容可选：可有可无

2）经验技巧

（1）必须在调用函数地方之前，先声明函数，shell 脚本是逐行运行。不会像其它语言一样先编译。

（2）函数返回值，只能通过$?系统变量获得，可以显示加：return 返回，如果不加，将以最后一条命令运行结果，作为返回值。return 后跟数值 n(0-255)

3）案例实操

实操 1：

计算两个输入参数的和。

```
#####################脚本
#!/bin/bash

function add(){
        s=$[$1 + $2]
        echo "和："$s
}

read -p "请输入第一个整数：" a
read -p "请输入第二个整数：" b

add $a $b

#####################执行
[root@localhost scripts]# chmod +x fun_test.sh
[root@localhost scripts]# ./fun_test.sh
请输入第一个整数：1
请输入第二个整数：2
和：3
```

实操 2：

获取函数返回值

```
#!/bin/bash

function add(){
        s=$[$1 + $2]
        echo $s
}

read -p "请输入第一个整数：" a
read -p "请输入第二个整数：" b

sum=$(add $a $b)
echo "和："$sum
echo "和的平方："$[$sum*$sum]
```

## 第 9 章 正则表达式入门

正则表达式使用单个字符串来描述、匹配一系列符合某个语法规则的字符串。在很多文本编辑器里，正则表达式通常被用来检索、替换那些符合某个模式的文本。在 Linux 中，grep，sed，awk 等文本处理工具都支持通过正则表达式进行模式匹配。

### 9.1 常规匹配

匹配文本中包含 atguigu 的行

```
cat /etc/passwd | grep atguigu
```

### 9.2 常用特殊字符

1）特殊字符：`^`

`^` 匹配一行的开头，例如：

```
cat /etc/passwd | grep ^a
```

会匹配出所有以 a 开头的行

2）特殊字符：`$`

$ 匹配一行的结束，例如

```
cat /etc/passwd | grep t$
```

会匹配出所有以 t 结尾的行。

注：`^$` 匹配空行

3）特殊字符：`. `

`.` 匹配一个任意的字符，例如

```
cat /etc/passwd | grep r..t
```

会匹配 r 和 t 之间有两个两个字符的行，包含 rabt,rbbt,rxdt,root 等的所有行

4）特殊字符：`* `

`*` 不单独使用，他和上一个字符连用，表示匹配上一个字符 0 次或多次，例如

```
cat /etc/passwd | grep ro*t
```

会匹配 rt, rot, root, rooot, roooot 等所有行

5）字符区间（中括号）：`[ ] `

`[ ]` 表示匹配某个范围内的一个字符，例如

`[6,8]`------匹配 6 或者 8

`[0-9]`------匹配一个 0-9 的数字

`[0-9]*`------匹配任意长度的数字字符串

[a-z]------匹配一个 a-z 之间的字符

[a-z]\* ------匹配任意长度的字母字符串

[a-c, e-f]-匹配 a-c 或者 e-f 之间的任意字符

```
cat /etc/passwd | grep r[a,b,c]*t
```

会匹配 rt,rat, rbt, rabt, rbact,rabccbaaacbt 等等所有行

6）特殊字符：`\`

`\` 表示转义，并不会单独使用。由于所有特殊字符都有其特定匹配模式，当我们想匹配某一特殊字符本身时（例如，我想找出所有包含 '$' 的行），就会碰到困难。此时我们就要将转义字符和特殊字符连用，来表示特殊字符本身，例如

```
cat /etc/passwd | grep 'a\$b'
```

就会匹配所有包含 a$b 的行。注意需要使用单引号将表达式引起来。

## 第 10 章 文本处理工具

### 10.1 cut

cut 的工作就是“剪”，具体的说就是在文件中负责剪切数据用的。cut 命令从文件的每一行剪切字节、字符和字段并将这些字节、字符和字段输出。

1）基本用法

cut [选项参数] filename

说明：默认分隔符是制表符

2）选项参数说明

| 选项参数 | 功能                                           |
| :------- | ---------------------------------------------- |
| -f       | 列号，提取第几列                               |
| -d       | 分隔符，按照指定分隔符分割列，默认是制表符“\t” |
| -c       | 按字符进行切割 后加加 n 表示取第几列 比如-c 1  |

3）案例实操

（1）数据准备

```
vim cut.txt
#数据内容
dong shen
guan zhen
wo wo
lai lai
le le
```

（2）切割 cut.txt 第一列

```
[root@localhost scripts]# cut -d " " -f 1 cut.txt
dong
guan
wo
lai
le
```

（3）切割 cut.txt 第二、三列

```
[root@localhost scripts]# cut -d " " -f 2,3 cut.txt
shen
zhen
wo
lai
le
```

（4）在 cut.txt 文件中切割出 guan

```
[root@localhost scripts]# cat cut.txt |grep guan | cut -d " " -f 1
guan
```

（5）选取系统 PATH 变量值，第 2 个“：”开始后的所有路径：

```
[root@localhost scripts]# echo $PATH
/opt/jdk17/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin

[root@localhost scripts]# echo $PATH | cut -d ":" -f 3-
/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```

获取第四列之前：

```
[root@localhost scripts]# echo $PATH | cut -d ":" -f -3
/opt/jdk17/bin:/usr/local/sbin:/usr/local/bin
```

（6）切割 ifconfig 后打印的 IP 地址

```
[root@localhost scripts]# ifconfig br-34d9a5144451 | grep netmask | cut -d " " -f 10
172.19.0.1
```

### 10.2 awk

一个强大的文本分析工具，把文件逐行的读入，以空格为默认分隔符将每行切片，切开的部分再进行分析处理。

1）基本用法

awk [选项参数] ‘/pattern1/{action1} /pattern2/{action2}...’ filename

pattern：表示 awk 在数据中查找的内容，就是匹配模式

action：在找到匹配内容时所执行的一系列命令

2）选项参数说明

| 选项参数 | 功能                 |
| -------- | -------------------- |
| -F       | 指定输入文件分隔符   |
| -v       | 赋值一个用户定义变量 |

3）案例实操

（1）数据准备

```
[root@localhost scripts]# cp /etc/passwd ./
passwd 数据的含义
用户名:密码(加密过后的):用户 id:组 id:注释:用户家目录:shell 解析器
```

（2）搜索 passwd 文件以 root 关键字开头的所有行，并输出该行的第 7 列。

```
[root@localhost scripts]# awk -F : '/^root/{print $7}' passwd
/bin/bash
```

（3）搜索 passwd 文件以 root 关键字开头的所有行，并输出该行的第 1 列和第 7 列，中间以“，”号分割。

```
[root@localhost scripts]# awk -F : '/^root/{print $1","$7}' passwd
root,/bin/bash
```

注意：只有匹配了 pattern 的行才会执行 action。

（4）只显示/etc/passwd 的第一列和第七列，以逗号分割，且在所有行前面添加列名 user，shell 在最后一行添加"dahaige，/bin/zuishuai"。

```
[atguigu@hadoop101 shells]$ cat /etc/passwd | awk -F : 'BEGIN{print "user, shell"} {print $1","$7}END{print "dahaige,/bin/zuishuai"}'
user, shell
root,/bin/bash
bin,/sbin/nologin 。。。
atguigu,/bin/bash
dahaige,/bin/zuishuai
```

注意：BEGIN 在所有数据读取行之前执行；END 在所有数据执行之后执行。

（5）将 passwd 文件中的用户 id 增加数值 1 并输出

```
[root@localhost scripts]# cat /etc/passwd | awk -v i=1 -F ":" '{print $3+i}'
1
2
3
4
```

4）awk 的内置变量

| 变量     | 说明                                   |
| -------- | -------------------------------------- |
| FILENAME | 文件名                                 |
| NR       | 已读的记录数（行号）                   |
| NF       | 浏览记录的域的个数（切割后，列的个数） |

5）案例实操

（1）统计 passwd 文件名，每行的行号，每行的列数

```
[root@localhost scripts]# awk -F ":" '{print "文件名："FILENAME " 行号："NR " 列数："NF}' /etc/passwd
文件名：/etc/passwd 行号：1 列数：7
文件名：/etc/passwd 行号：2 列数：7
文件名：/etc/passwd 行号：3 列数：7
文件名：/etc/passwd 行号：4 列数：7
```

（2）查询 ifconfig 命令输出结果中的空行所在的行号

```
[root@localhost scripts]# ifconfig | awk '/^$/ {print "空行："NR}'
空行：9
空行：18
空行：27
空行：36
空行：44
```

（3）切割 IP

```
[root@localhost scripts]# ifconfig | awk '/netmask/ {print $2}'
172.19.0.1
172.18.0.1
172.17.226.187
127.0.0.1
```

## 第 11 章 综合应用案例

### 11.1 归档文件

实际生产应用中，往往需要对重要数据进行归档备份。

需求：实现一个每天对指定目录归档备份的脚本，输入一个目录名称（末尾不带/），将目录下所有文件按天归档保存，并将归档日期附加在归档文件名上，放在/root/archive 下。

这里用到了归档命令：tar

后面可以加上-c 选项表示归档，加上-z 选项表示同时进行压缩，得到的文件后缀名为.tar.gz。

脚本实现如下：

```
#!/bin/bash

#首先判断输入参数个数是否为1
if [ $# -ne 1 ]
then
        echo "参数个数错误！应该输入一个参数，作为归档目录名"
        exit
fi

#从参数总获取目录名称
if [ -d $1 ]
then
        echo
else
        echo
        echo "目录不存在！"
        echo
        exit
fi

DIR_NAME=$(basename $1)
DIR_PATH=$(cd $(dirname $1); pwd)

#获取当前日期
DATE=$(date +%y%m%d)

#定义生成的归档文件名称
FILE=archive_${DIR_NAME}_$DATE.tar.gz
DEST=/root/archive/$FILE

#开始归档目录文件

echo "开始归档..."
echo

tar -czf $DEST $DIR_PATH/$DIR_NAME

if [ $? -eq 0 ]
then
        echo
        echo "归档成功！"
        echo "归档文件为：$DEST"
        echo
else
        echo "归档出现问题！"
        echo
fi

exit
```

执行脚本：

```
chmod +x daily_archive.sh
mkdir /root/archive
[root@localhost scripts]# ./daily_archive.sh ../scripts

开始归档...

tar: Removing leading `/' from member names

归档成功！
归档文件为：/root/archive/archive_scripts_241110.tar.gz
```

定时执行脚本：

```
crontab -e
0 2 * * *  /root/scripts/daily_archive.sh /root/scripts
```

### 11.2 发送消息

我们可以利用 Linux 自带的 mesg 和 write 工具，向其它用户发送消息。

需求：实现一个向某个用户快速发送消息的脚本，输入用户名作为第一个参数，后面直接跟要发送的消息。脚本需要检测用户是否登录在系统中、是否打开消息功能，以及当前发送消息是否为空。

脚本实现如下：

```
#!/bin/bash

#查看用户是否登录
login_user=$(who | grep -i -m $1 | awk '{print $1}')

if [ -z $login_user ]
then
        echo "$1 不在线！"
        echo "脚本退出..."
        exit
fi

#查看用户是否开启消息功能
is_allowed=$(who -T | grep -i -m $1 | awk '{print $2}')

if [ $is_allowed != "+" ]
then
        echo "$1 没有开启消息功能"
        echo "脚本退出..."
        exit
fi

#确认是否有消息发送
if [ -z $2 ]
then
        echo "没有消息发送"
        echo "脚本退出..."
        exit
fi

#从参数中获取要发送的消息
whole_msg=$(echo $* | cut -d " " -f 2-)

#获取用户登录的终端
user_terminal=$(who | grep -i -m $1 | awk '{print $2}')

#写入要发送的消息
echo $whole_msg | write $login_user $user_terminal

if [ $? != 0 ]
then
        echo "发送失败！"
else
        echo "发送成功！"
fi

exit
```
