---
layout: post
title: Shell 脚本
tags: [shell, 脚本, ping, count]
color: brown
---

# 好用的shell工具脚本!

日常积累小脚本 [dxb/shell/参考](https://www.cnblogs.com/handsomecui/p/5869361.html) 
（不积跬步，无以至千里；不积小流，无以成江海.水滴石穿；不以恶小而为之，不以善小而不为之；冰冻三尺，非一日之寒）

你可以对此进行参照! 

## 下列为测试的shell脚本：

我们可以 通过ping命令测试192.168.0.2到192.168.0.254之间的所有主机是否在线:

```yml
---
#!/bin/bash
for i in `seq 2 254`
do
    ping -c1 -w1 192.168.51.$i > /dev/null 2>&1
    #-c可以指定ping的次数，-w测试的时间 -w 1就是1秒中无论成功失败都结束
    #-c -w后面都要输入参数所以都要带上l
    #$?代表最后命令退出状态，0代表没错，其他代表有错
    [ $? -eq 0 ] && echo "192.168.51.$i IP is UP!" || echo "192.168.51.$i IP is down!"
done
---
```

1.在linux下会写shell脚本是非常重要的，下面我参照例子给大家展示几个脚本，顺带这学习shell 的语法：什么时候helloworld是必不可少的，第一个脚本肯定与helloworld是离不开的：

```yml
---
#!/bin/sh
a="hello world!"
num=2
echo "a is : $a num is : ${num}nd"

运行结果： a is : hello world! num is : 2nd
---
```
2.下面带着大家学学if语句：先抛出一个问题：写一个脚本，判断当前所用的shell

```yml
---
#!/bin/sh
#注意if的空格以及[]里面的空格这里错了半天啊
if [ "$SHELL" = "/bin/bash" ];then
echo "your login shell is the bash \n"
echo "SHELL is : $SHELL"
else 
echo "your login shell is not bash but $SHELL"
fi
#[ -f "somefile" ] : 判断是否是一个文件
#[ -x "/bin/ls" ] : 判断/bin/ls是否存在并有可执行权限
#{ -n "$var" } : 判断$var变量是否有值
#[ "&a" = "$b" ] : 判断$a和$b是否相等
[ -f "/etc/shadow" ] && echo "This computer uses shadow passwords"
if [ -f "/etc/shadow" ];then
echo "电脑密码使用隐藏字符"
else
echo "并没有"
fi
---
```

3.为了让大家更加熟练的运用shell，那么练习下吧，问题：写一个脚本使其从一个文件里面读入有echo的语句，并把其写在本文件末尾;

```yml
---
#!/bin/sh
#-r代表此文件是否可读，具体见man test
mailfolder=/home/handsome/work/linux_learn/shell_learn/readme.txt
#[ -r "$mailfolder" ] || { echo "Can not read $mailfolder" ; exit 1;}
#echo "$mailfolder has mail from : "
#grep "^echo " $mailfolder

if [ -r "$mailfolder" ];then
    echo "$mailfolder has massage from : "
    echo | grep '^echo ' $mailfolder >> readme2.txt
    chmod +r readme2.txt
    cat readme2.txt >> $mailfolder
    rm -f readme2.txt
else
    echo "Can not read $mailfolder"
    touch $mailfolder
    chmod +rw $mailfolder
    echo "echo 人生自古谁无死，六区蛋清找旱情! " >> $mailfolder
fi
---
```

4.接下来，我们练习下while的用法，老规矩具体问题来分析;
问题：写一个脚本（不建议使用for变量--for是根据空格取值）
1.设定变量FILE的值为/etc/passwd
2.依次向/etc/passwd中的每个用户问好，并且说出对方的ID什么（Hello,root，your UID is0.）
3.统计一个有多少个用户

```yml
---
#!/bin/bash
fl=/etc/passwd
count=`cat $fl | wc -l`
#下面是一个管道，下面循环读文件中的每一行
cat $fl |
while read line
do
    user=`echo $line|awk -F ':' '{print $1}'`
    #代表以 ：分段$1就是取第1段
    uid=`echo $line|awk -F ':' '{print $3}'`
    echo "hello, $user Your UID is $uid"
done
echo "====User_count:$count===="
#前面求得的用户数
---
```

5.练习一下for语句吧
问题：写个脚本;
1.切换工作目录至/tmp
2.依次向/tmp目录中的每个文件或子目录问好（Hello,log）
3.统计/tmp目录下共有多个文件，并显示出来
```yml
---
#!/bin/bash
cd /tmp
for i in /tmp/*
do
    echo "Hello , $i"
done
count=`ls -l|grep '^-'|wc -l`
echo "====file_count:$count===="
---
```

6.那么问题来了：
question：
传递两个整数给脚本，让脚本分别计算并显示这两个整数的和，差，积，商

```yml
---
#!/bin/bash
a=$1
b=$2
[ -z $a ] && echo "please \$1 number1" && exit 1
[ -z $b ] && echo "please \$2 number2" && exit 2
[ ! $# -eq 2 ] && echo "--please num1 num2" && exit 3
# $#传递到脚本的参数个数
# -eq相当于=, -ne相当于不等于， -ge >= , -gt >, -le <=, -lt <
echo "$a + $b = $(($a+$b))"
echo "$a - $b = $(($a - $b))"
echo "$a * $b = $(($a*$b))"
echo "$a / $b = $(($a/$b))"
---
```

7.写一个脚本;求1到100的和？

```yml
---
#!/bin/bash
sum=0
i=1
while [ $i -le 100 ]
do
    sum=$(($sum + $i))
    i=$(($i+1))
done
echo "$sum"
---
```
