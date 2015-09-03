title: "Shell编程<1>---控制语句"
date: 2014-10-29 10:24:22
categories: Linux
tags: Shell
---
最近在看 Linux 程序设计，准备写成一个系列。把每天学到的记录下来。
以实际的例子为主。
<!--more-->
##一、条件语句
### if 语句
语法
> ```sh
#用法 1
if condition1
then
  statement
elif condition2
then
  statement
fi
#用法 2
if condition1； then
  statement
elif condition2; then
  statement
else
  statement
fi
```

例子
+ case1:
```sh
#!/bin/sh
echo "Is it moring? Please answer yes or no"
read timeofday
if [ "$timeofday" = "yes" ]; then
    echo "Good morning"
elif [ "$timeofday" = "no" ]; then
    echo "Good afternoon"
else
    echo "sorry, $timeofday not recognized. Enter yes or no"
    exit 1
fi
exit 0
```

### case 语句
+ case1:
```sh
echo "Is it morning? Please input yes or no?"
read timeofday
case $timeofday in
    yes) echo "Good morning";; #双分号
    no) echo "Good afternoon";;
    y) echo "Good morning";;
    n) echo "Good afternoon";;
    *) echo "Sorry, answer not recognized";;
esac
```
+ case2: **合并匹配模式**
```sh
echo "Is it morning? Please input yes or no?"
read timeofday
case $timeofday in
    yes | y | Yes | YES) echo "Good morning";; #双分号
    n* | N*) echo "Good afternoon";;
    *) echo "Sorry, answer not recognized";;
esac
```
+ case3: 多条语句
```sh
read timeofday
case $timeofday in
  yes | y | Yes | YES)
  echo "Good morning"
  echo "Up bright and early in the morning"
  ;; #双分号
  [nN]*)
  echo "Good afternoon"
  ;;
  *)
  echo "Sorry, answer not recognized"
  exit 1
  ;;
esac
exit 0
```

##二、循环语句
### for 语句
+ case1: 固定字符串的for循环
```sh
for file in bar fud 43; do
    echo $file
done
```
+ case2: 使用通配符的for循环
```sh
for file in $(ls f*.sh); do
    echo $file
done
```
输出:
```sh
bar
fud
43
```
+ case3: 以 test1.txt test2.txt ...运行 a.out
```sh
for ((i=0; i<200; i++)); do #必须有两个括号
   ./a.out < test$i.txt
done
```
+case4: 将当前目录下的所有的.txt改名为 .gif
```sh
for file in *.gif
do
  mv $file  ${file%%gif}txt  //${file%%gif}表示从尾部开始删除与gif匹配的最长部分
done
```

### while 语句
+ case1: 密码输入程序
```sh
echo "Enter Password"
read trythis
while [ "$trythis" != "secret" ]; do
    echo "Sorry, try again"
    read trythis
done
echo "Congrats, password correct"
```

### until 语句
与while 相反，当条件不满足时一直运行，直到条件为真
+ case1:
```sh
read trythis
until [ $trythis = "secret" ]; do
    echo "Sorry, try again"
    read trythis
done
```

##二、其他
### And 和 Or
```sh
statement && statement
statement || statement
```
### 语句块
```sh
statement && {
  statement1
  statement2
  statement3
  ...
}
```
