# Shell脚本编程第一课：构成程序的7个基本元素

![Shell脚本编程第一课：构成程序的7个基本元素](readme.assets/2304c7a4-062f-487d-9c95-a51849b700d6)

此系列课程的 Shell 特指Bourne Again shell（bash）

```
$ bash --version
bash --version
GNU bash, version 5.0.3(1)-release (x86_64-pc-linux-gnu
```

# 1.数学表达式

学习编程，我们本能的起点自然是如何进行数学运算，如何处理数学表达式。在bash中，实现这一点稍微有些繁琐，我们需要将表达式放入到 $((...))中。

```
echo $(( 60*60*24 )) #计算一天有多少秒
86400
```

先暂且不理会$((...))的具体含义，先记着需要将我们所熟知的数学表达式嵌入其中即可，因为如果直接键入数学表达式，会当场报错。

```
$ 56 + 72
56 + 72
56: command not found
```

再看一个复合表达式：

```
$ echo $(( (3+5+7) * (2+(4*6)) ))
echo $(( (3+5+7) * (2+(4*6)) ))
390
```

作为shell-script的第一步，现在我们知道，终端就是一个便携的计算器。

# 2.命名与变量

我们的记忆力有限，不大容易记住一长串的数字或者一段程序。编程语言为此提供的核心解决途径就是命名。命名的方式简单直接。

```
$ radius=5　# 注意等号两侧不能有空格
radius=5
```

读取变量的时候，则需要前缀 $将其解索引：

```
$ echo The radius is $radius.
The radius is 5.
```

接下来，我们计算圆的面积：

```
$ radius=5; pi=3; area=$(( $pi*$radius**2 )); echo $area
75
```

于是我们可以直接用抽象的名字进行数学运算。

# 3.常量与环境变量

我们可以命名变量，但是变量的取名并非完全自由，不能与系统的环境变量相冲突。比如查看当前bash的进程号：

```
$ echo $BASHPID
15190
```

查阅完整的进程树：

```
$ pstree -asp $BASHPID
systemd,1 splash
  └─tmux: server,2628 new -s spirit
      └─bash,2629
          └─bash,15190 -v
              └─pstree,26568 -asp 15190
```

倘若好奇系统中有多少环境变量，应该求问env或者printenv命令：

```
$ env | nl | tail -n 5
    87  DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
    88  MAIL=/var/mail/gaowei
    89  LC_NUMERIC=en_US.UTF-8
    90  KONSOLE_DBUS_WINDOW=/Windows/1
    91  _=/usr/bin/env
```

如结果所示，当前系统中有91个环境变量。

# 4.函数

与变量相比较，函数是更为强大更具表现力的抽象工具。我们可以将 function 当作执行特定任务的‘黑匣子’。函数的的语法为：

```
function function_name {
command...
}  
或者
function_name () {
command...
} 
```

函数的应用，我们还是从熟悉的简单数学运算出发，求给定数的平方。

```
$ function square {
     echo $(( $1 ** 2 ))
}
```

函数体中$1是新鲜事物，乃是指代第一个参数的位置参数，应用这个函数：

```
$ square 11
121
$ square 121
14641
```

# 5.条件语句

到目前为止，我们定义的程序表达力expressive-power极为有限。因为我们尚且没有途径做出种种测试，并根据测试的不同结果来决定下一步的操作。

此时，我们需要引入条件判断，基本结构为：

```
    if commands; then
      commands
    [elif commands; then
      commands...]
    [else
      commands]
    fi
```

我们尝试求绝对值的函数：

```
function abs {
    if (( $1 > 0 )); then echo $1
    elif (( $1 = 0 )); then echo 0
    else echo $(( -$1 ))
    fi
}
```

测试新定义的abs函数：

```
abs -110
110 
```

除了三项简单的逻辑判断符号 <>=之外，还可以使用复合逻辑操作符：

```
    $ command1 && command2 # and 逻辑
    $ command1 || command2 # or 逻辑
    $ ! # not
```

# 6.循环结构

Looping结构，首先想到的当然是 for 语句：

```
$ for i in *; do echo $i; done
anaconda3
backup.doom.d
backup.gnus.el

$ for i in {A..E}; do echo $i; done
A
B
C
D
E
```

以及 while 语句

```
count=1
while [[ "$count" -le 5 ]]; do
          echo "$count"
          count=$((count + 1))
done
echo "Finished."
```

# 7.读取键盘输入

程序极为重要的一点是与用户的沟通与互动。我最爱用read取代xargs，例如这样的结构：

```
find . -iname "*.pdf" | while read line; do something
```

Read 有多个有意思的参数，首先是-p,prompt 

```
read -p "Enter one or more values.>"
echo "REPLY = '$REPLY'"
```

其次 -t 限定用户的反馈时间：

```
read  -t 10 -p  "Enter secret passphrase"
```

# 8.收尾总结

本文我们探讨了 bash-script 的七项基本元素：

1. 数学表达式
2. 变量与命名
3. 常量与环境变量
4. 函数
5. 条件语句
6. 循环语句
7. Read

以上，第一课完结。
