# 什么是shell

shell就是一块包裹着系统核心的壳，处于操作系统的最外层，与用户直接对话，将用户的输入解释给操作系统，然后处理操作系统的结果，输出到屏幕，给用户看

shell的作用

* 解释执行用户输入的命令或程序

* 用户输入一条命令，shell就解释一条命令

* 键盘输入命令，Linux给予响应的方式，称之为交互

  ![image-20240228152904086](D:\study-for-qiuzhao\Shell自动化开发\assets\image-20240228152904086.png)

# 什么是shell脚本

shell语言属于弱类型语言，它定义的变量，数据类型默认都是字符串类型

> 弱类型语言：在定义数据类型的时候，不需要主动的声明变量类型，如：shell，python
>
> 强类型语言，必须指明变量类型，如：C，GO

当命令或者程序写在文件中，我们执行文件，读取其中的代码，这个程序文件被称为shell脚本

再shell脚本中定义多条linux命令或者循环控制语句，来执行任务，被称为非交互方式

* linux中常使用`*.sh`脚本文件
* windows中常使用`*.bat`批处理脚本

## shell脚本规则

在Linux中，shell脚本或称为`bash shell 程序`,通常都是vim编辑，由linux命令，bash shell指令，逻辑控制语句，注释信息组成

## shebang

在计算机程序中，shebang指的是文本文件的第一行前两个字符`#!`

在unix系统中，程序会分析shebang后面的内容，作为解释器的指令，如：

* 以`#! /bin/sh`开头的文件，程序在执行的时候会调用`/bin/sh`,也就是bash解释器

* 以`#! /usr/bin/python`开头的文件，代表指定python解释器执行

* 以`#! /usr/bin/env 解释器名称`，是一种在不同平台上都能找到解释器的办法

  ```bash
  hello.sh 文件
  #! /bin/sh
  # 这是我的第一个shell程序
  echo "hello world~!"
  
  ---运行结果---
  [root@XUN ~]# /bin/bash ./hello.sh 
  hello world~!
  
  
  ---python程序---
  [root@XUN ~]# cat hello.sh 
  #! /usr/bin/python
  print("hello world by python!")
  ---运行结果---
  [root@XUN ~]# ./hello.sh 
  hello world by python!
  ```

* 注意：
  * 如果没有使用shebang，脚本执行的时候会默认使用当前的shell去解释脚本，即$SHELL
  * `#`后面的内容是注释

# 执行shell脚本的方式

`bash xxx.sh 或者 sh xxx.sh`，文件本身没有执行权限，或脚本未指定shebang，

使用绝对或者相对路径执行脚本，需要脚本文件有可执行权限`./xxx.sh`

`source xxx.sh 或者 . xxx.sh`方式，`source == .`

```bash
[root@XUN ~]# . hello.sh 
hello world ！！！
[root@XUN ~]# source hello.sh 
hello world ！！！
```

# bash基础

## 什么是bash

* bash是一个命令处理器，运行在文本窗口中，能执行用户输入的命令
* bash能从文件中读取linux命令，称之为脚本
* bash支持通配符，管道，命令替换，条件判断等逻辑控制语句

>1. history 显示历史指令
>
>   -c 清除指令
>
>   -r 恢复指令
>
>2. ！！ 执行上次执行的命令
>
>3. ！ 历史id 快速执行历史命令

# shell变量

shell中通过`echo $变量名`来得到变量的值

变量定义与赋值，变量与值之间不能有空格

bash默认所有的变量都是字符串

单引号变量，不识别特殊语法

双引号变量，识别特殊语法

```bash
[root@XUN ~]# name=zhangsan
[root@XUN ~]# echo $name
zhangsan
```

## 变量的作用域

* 本地变量：只针对当前的shell进程

  ```bash
  pstree #查看进程树
  ```

* 环境变量：也称为全局变量，针对当前shell及其任意子进程，环境变量也分为自定义和内置两种环境变量

* 局部变量：针对在shell函数或者shell脚本中定义

* 位置参数变量：用于shell脚本中传递的参数

* 特殊变量：shell内置的特殊变量

  * $?：判断上一条命令是否执行成功
    * 0:表示成功
    * 1-255：错误码

* 自定义变量：

  * 变量赋值 name=张三
  * 变量引用 ${name},$name
  * 双引号，变量名会被替换为变量值

  ```bash
  [root@XUN ~]# age=18
  [root@XUN ~]# dis="zhangsan is ${age}"
  [root@XUN ~]# dis2='zhangsan is ${age}'
  [root@XUN ~]# echo $dis
  zhangsan is 18
  [root@XUN ~]# echo $dis2
  zhangsan is ${age}
  ```

  