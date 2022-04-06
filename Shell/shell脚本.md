## shell脚本

##### shell脚本注意事项

- shell内容以`#!/bin/bash`开头
- 变量名称尽量使用大写字母，字母间不能使用`-`，但是可以使用`_`

##### 执行命令shell脚本的方法

- `./shellscript.sh`
- `/bin/sh shellscript.sh`

### shell变量

shell为弱类型语言，定义变量不需要声明类型。

可以使用declare指定类型。

shell编程中变量分为：系统变量、环境变量、用户变量。

- 系统变量在对参数判断和命令返回值判断时使用。
- 环境变量只要是在程序运行时需要设置。
- 用户变量又称为局部变量，多使用在脚本内部。

##### shell常见的系统变量：

- `$0`：当前脚本的名称。
- `$n`：当前脚本的第n个参数。
- `$*`：当前脚本的所有参数（不包含程序本身）。
- `$#`：当前脚本的参数个数（不包含程序本书）。
- `$?`：命令或程序执行完后返回的状态，返回0表示执行成功。
- `$$`：程序本身的PID号。

##### shell常见环境变量：

- PATH：命令所示路径，以冒号为分割。
- HOME：打印用户家目录。
- SHELL：显示当前shell类型。
- USER：打印。
- ID：打印当前用户ID。
- PWD：显示当前所在路径。
- TERM：打印当前中断类型。
- HOSTNAME：显示当前主机名。

```shell
 #!/bin/bash
echo $PATH：命令所示路径，以冒号为分割。
echo $HOME：打印用户家目录。
echo $SHELL：显示当前shell类型。
echo $USER：打印。
echo $ID：打印当前用户ID。
echo $PWD：显示当前所在路径。
echo $TERM：打印当前中断类型。
echo $HOSTNAME：显示当前主机名。
```

:star:使用到的三剑客

``` shell
awk '{print $0 "demo"}' file #在文本每一行前添加demo
awk '{print "demo" $0}' file #在文本每一行后添加demo
awk '{print "demo" $0 > "file"}' file #将修改后的文本添加到file文件中，文件名用双引号
```

### if条件语句

```
if(表达式);then
	语句1
else
	语句2
fi
```

```shell
if(表达式);then
	语句1
elif(表达式);
	语句2
elif(表达式);
	语句3
fi
```



##### 案例（比较两个整数大小）

```shell
#!/bin/bash
NUM=100
if(($NUM>4));then
	echo "The $NUM more than 4"
else
	echo "The $NUM less then 4"
fi
```

##### 案例（判断文件是否存在）

```shell
#!/bin/bash
if [ ! -f file1 -a ! -f file2 ];then
touch file1
fi
```

##### 常见判断逻辑运算符

```shell
-f:判断文件是否存在
-d:判断目录是否存在
-eq:等于，整形比较
-ne:不等于，整形比较
-lt:小于，整形比较
-gt:大于，整形比较
-le:小于或等于，整形比较
-ge:大于或等于，整形比较
-a:逻辑表达式and
-o:逻辑表达时or
-z:空字符串
||:单方成立
&&:双方成立
```

##### 案例（判断分数）

```shell
#!/bin/bash
scores=$1
if [[ $scores -eq 100 ]]; then
        echo "very good!";
elif [[ $scores -gt 85 ]]; then
        echo "good!";
elif [[ $scores -gt 60 ]];then
        echo "pass!"
elif [[ $scores -lt 60 ]];then
        echo "no pass!"
fi
```

### if判断括号区别

```shell
(): 用于多个命令组
(()): 整数扩展、运算符、重定义变量值、算数运算比较
[]: bash内部命令，与test相同。正则字符范围、引用数组元素编号，不支持“+”，“-，“*”，“/”数学运算符，逻辑测试使用-a、-o。
[[]]: bash程序语言关键字，不是一个命令，比[]更加通用，不支持“+”，“-，“*”，“/”数学运算符，逻辑测试使用&&、||。
{}： 主要用于命令集合或范围，例如mkdir -p /data/201{7,8}
```

:star:创建多个文件

```shell
touch file{0..5} # 创建file0~file5
```

##### 实战（MySQL数据库备份脚本）

```shell
#!/bin/bash
# auto backup mysql
# By author kevinjohn 2020/11/13
BAKDIR=/data/backup/mysql/`date +%Y-%m-%d`
MYSQLDB=test    #定义数据库变量
MYSQLPW=1       #数据库密码
MYSQLUSR=root   #登录数据库用户
if [ $UID -ne 0 ];then  #UID为0表示root用户
        echo This script must use the root user!!!
        sleep 2
        exit 0
fi
if [ ! -d $BAKDIR ];then
        mkdir -p $BAKDIR
fi
/usr/bin/mysqldump -u $MYSQLUSR -p$MYSQLPW -d $MYSQLDB > $BAKDIR/test_db.sql
echo "The mysql backup successfully"
```

:star:变量赋值=不能留空格

### for循环

```shell
for var in (表达式)
do
	语句1
done
```

##### 案例

```shell
# 循环打印bat官网地址
for website in www.baidu.com www.taobao.com www.qq.com
do
	echo $website
done

#循环打印1~100数字，seq表示列出数据范围
for i in `seq 1 100`
do
	echo "NUM is $1"
done

#for循环求1~100的总和
j=0
for((i=1;i<=100;i++))
do
	j=`expr $i + $j`
done
echo $j

# 批量远程主机执行命令
for i in `seq 100 200`
do
	ssh -l root 192.168.1.$i 'ls /tmp'
done
```

