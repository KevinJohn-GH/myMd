## shell编辑四剑客

### find

find工具主要用户操作系统文件、目录的查找

`find path -option [ -print] [ -exec -ok command] {} \ `

##### 常用参数

- `name filename`：查找名为filename的文件
- `-type b/d/c/p/l/f `：查找块设备、目录、字符设备、管道、符号链接、普通文件
- `-size n[c]`：查找长度为n块[或n字节]的文件。
- `-perm`：按执行权限来查找。
- `-user username`：按文件属主来查找。
- `-group groupname`：按组来查找。
- `-mtime -n +n`：按文件修改时间来查找，-n指n天以内，+n值n天以前。

- `-atime -n +n`：按照文件访问时间来查找文件。
- `-ctime -n +n`：按照文件创建时间来查找文件。
- `-mmin -n +n`
- `-amin -n +n`
- `-cmin -n +n`
- `-nouser`：查找无效属主的文件。

##### 案例

```shell
# -name
find /data/ -name "[A-Z]*" # 查找data目录以大写字母开头的文件


# -type
find /data/ ! -type d #查找data目录下的非文件目录
find /data/ -type d | xargs chmod 755 -R # 查找目录类型并将权限设置为755

# -size
find /data/ -size +10M # 文件大小大于10M的文件
find /data/ -size 10M # 文件大小为10M的文件
find /data/ -size -10M # 文件大小小于10M的文件

# -perm
find /data/ -perm 755 #查找文件权限为755的文件或目录

# -mitme
find /data/ -mtime +30-name "*.log" # 查找30天以前的log文件
find /data/ -mtime -30name "*.log" # 查找30天内的log文件

#查找/data目录以.log结尾，文件大于10kb的文件，同时cp到/tmp目录
find /data -name "*.log" -type f -size + 10k -exec cp {} /tmp/ \;
```

⭐`-exec`以`\;`结尾，`{}`相当于前面传来的文件



### sed

非交互式文本编辑器。

```shell
sed [- Optios] ['Commands'] filename;
```

##### 常用参数

- `x`: 指定行号。
- `x,y`: 指定从x到y的行号范围。
- `/pattern/`: 查询包含模式的行。
- `/pattern/pattern/`: 查询包含两个模式的行
- `/pattern/,x`: 从与pattern的匹配行到x号行之间的行。
- `x,/pattern/`: 从x号行到与pattern的匹配行之间的行。
- `x,y!`: 查询不包含x和y行号的行。
- `p`: 打印匹配行。
- `=`: 打印文件行号。
- `a\`: 在定位行号之后追加文本信息。
- `i\`: 在定位行号之前插入文本信息。
- `d`: 删除指定行。
- `c\`: 用新文本替换定位文本。
- `x`: 互换模式缓冲区和保持缓冲区的内容

##### 案例

```shell
# 打印yes文本1~3行
sed -n '1,3p' yes.txt

# 打印yes文本第一行与最后一行
sed -n '1p;$p' yes.txt

# 打印yes文本第一行至第三行、删除匹配行至最后一行
sed '1,3d' yes.txt
sed '/50/, $d' yes.txt

# 删除yes文本最后6行及删除最后一行
for i in `seq 1 6`;do sed  '$d' yes.txt; done

# 查找50所在的行，并在后面添加05字符串
sed '/50/a05' yes.txt

# 查找50所在的行，并在前面添加11111字符串
sed '/50/i11111' yes.txt

# 查找0结尾所在的行，并在结尾添加11111
sed 's/0$/&11111/g' yes.txt
sed '/0$/s/^/11111/g' yes.txt

# 查找0结尾所在的行，并在行首添加11111
sed '/0$/s/^/&11111/' yes.txt

# 多个sed命令组合，使用-e参数
sed -e 'pattern1' -e 'pattern2' yes.txt

# 多个sed命令祝贺，使用;分割
sed -e 'pattern1' ; 'pattern2'

# sed读取系统变量，进行变量替换
WEBSITE=WWW.BAIDU.COM
sed 's/WWW.GOOLGE.COM/$WEBSITE/g' yes.txt
```

⭐使用`s///g`，g表示全局搜索，不带g则是匹配每行的第一个

#### sed高级命令

- `n、N、D、P`: 处理多行模板空间的问题。

  ⭐N命令：将下一行添加到pattern space中。将当前读入行和用N命令添加的下一行看成一行，\n任然有效。

  ⭐n命令：提前读取下一行，覆盖模板空间前一行。（打印默认输出）。

  > ❓因为n是覆盖，所以会直接打印默认输出，不用等后面的命令执行完。不知道这样理解对不对。

  ⭐D命令：删除当前模式空间开端至\n的内容（不再传至标准输出），放弃之后的命令，但是**对剩余模式空间继续执行sed**。

  ⭐d命令：d 删除pattern中的所有行，并读入下一新行到pattern中，（不再传至标准输出）

  ⭐P命令： 打印模板空间的第一行，追加到默认输出之前

  ⭐p命令：打印当前模式空间内容，追加到默认输出之后

- `H、h、G、g、x`: 将模式空间的内容放入存储空间以便接下来的编辑。

  - g：[address[,address]]g 将hold space中的内容拷贝到pattern space中，原来pattern space里的内容清除

  - G：[address[,address]]G 将hold space中的内容append到pattern space\n后

  - h：[address[,address]]h 将pattern space中的内容拷贝到hold space中，原来的hold space里的内容被清除

  - H：[address[,address]]H 将pattern space中的内容append到hold space\n后

- `:、b、t`: 在脚本中实现分支与条件结构。


##### 保持空间与模式空间

```
sed ‘1!G;h;$!d’mm
```

ps：1!G第1行不 执行“G”命令，从第2行开始执行。

​    $!d，最后一行不删除（保留最后1行）

图解分析过程

P：Pattern Space

H：Hold Space

蓝色：Hold Space中的数据

绿色：Pattern Space中的数据

![img](https://cdn.jsdelivr.net/gh/KevinJohn-GH/pictures/img/20201117170513.png)

##### 案例

```shell
# 每行后面插入空行、每行后插入两行空行、前三行每行后插入空行
sed '/^$/d;G' yes.txt	# /^$/d删除空行, 此时保持空间为空，所以插入的为空行
sed '/^$/d;G;G' yes.txt # 连续两次插入保持空间的内容
sed '/^$/d;1,3G;' yes.txt

# 将yes文本偶数行删除及隔两行删除一行
sed 'n;d' yes.txt	# n将下一行也添加进模式空间，后面的命令都对下一行进行
sed 'n;n;d' yes.txt

# 在yes匹配行前一行、后一行插入空行、同时在匹配前后插入空行
sed '/50/{x;p;x;}' yes.txt
sed '/50/G' yes.txt
sed '/50/{x;p;x;G;}' yes.txt

# 每行前加入顺序数字序号、加上制表符\t和.
sed = yes.txt | sed 'N;s/\n/ /'
sed = yes.txt | sed 'N;s/\n/\t/'
sed = yes.txt | sed 'N;s/\n/./'

# 删除行前和行尾的任意空格
sed 's/^[\t]*//;s/[\t]/*$//' yes.txt

# 打印关键字old和new之间的内容
sed -n '/old,/new/'p yes.txt

# 打印及删除最后两行
sed '$!N;$!D' yes.txt
sed 'N;$!P;$!D;$d' yes,txt

# 合并上下两行
sed '$!N;s/\n/ /' yes.txt
sed 'N;s/\n/ /' yes.txt
```

⭐`sed  '/5/{n;d}' yes.txt`删除指定字符串的下一行



### awk

```shell
awk 'pattern + {action}' file
```

##### 常用语法

- 单引号`''`为了将shell命令区分开
- 大括号`{}`表示命令分组
- pattern是一个过滤器，表示匹配pattern条件的行才进行action处理。
- action是处理动作，常见动作为print。

##### 内置变量

- FS：分隔符，默认是空格
- OFS：输出分隔符
- NR：当前行数，从1开始
- NF：当前记录字段个数
- $0：当前记录
- $1~$n：当前记录第n个字段（列）

##### awk内置函数详解

- `gsub(r,s)`: 在$0中使用s替代r
- `index(s,t)`: 返回s中t的第一个位置。
- `length(s)`: s的长度
- `match(s,r)`: s是否匹配r
- `split(s,a,fs)`: 在fs上讲s分成序列a
- `substr(s,p)`: 返回s从p开始的子串

##### awk常用操作符、运算符、判断符

- ++ 、--: 增加与减少（前置或后置）
- ^ 、**: 指数（右结合）
- ! 、+ 、-: 非、一元加号、一元减号
- \+ 、- 、* 、/ 、%: 加减乘除、余数
- < 、<= 、== 、!= 、> 、>=
- &&
- ||
- = 、+= 、-= 、*= 、/= 、%= 、^= 、**=

##### awk与流程控制语句

- `if(condition) {} else {};`
- `while{};`
- `do{}while(conditon);`
- `for(init; conditon; step){};`
- `break / continue`

##### 案例

``` shell
# 打印硬盘设备名称，默认以空格为分割
df -h|awk '{print $1}'

# 以空格、冒号、\t、分号为分割
awk -F '[ :\t;]' '{print $1}' yes.txt

# 以冒号分割，打印第一列，同时将内容追加到 awk.log
awk -F: '{print $1 >> "./awk.log"}' yes.txt

# 打印第3~5行，NR表示行，$0表示文本所有域
awk 'NR==3, NR==5 {print}' yes.txt

# 打印文件中的第3~5行的第一列与最后一列
awk 'NR==3,NR==5 {print $1, $NF}' yes.txt

# 打印文件中长度大于80的行号
awk 'length($0)>80 {print NR}' yes.txt

# awk引用shell变量，使用-v或者双引号+单引号即可
awk -v STR=hello '{print STR, $NF}' yes.txt
STR="hello"; echo | awk '{print "'${STR}'";}'

#以冒号切割，打印第一列同时只显示前5行
cat /etc/passwd | head -5 | awk -F: '{print $1}'
awk -F: 'NR>=1&&NR<=5 {print $1}' /etc/passwd

# 指定文件第一列的总和
cat yes.txt | awk '{sum += $1}END{print sum}'

# NR行号除以2余数为0则跳过该行，继续执行下一行，打印在屏幕
awk -F: 'NR%2==0{next}{print NR,$1}' yes.txt

# 添加自定义字符
ifconfig eth0 | grep "Bcast" | awk '{print "ip_" $2}'

# 输出passwd内容，printf打印字符串，%格式化输出分隔符，s表示字符串类型，-12表示12个字符，-6表示6个字符
awk -F: '{printf "% -12s % -6s % -8s\n", $1, $2,$NF}' /etc/passwd

# awk OFS输出格式化\t
netstat -an|awk '/LISTEN/&&NR>=1&&NR<=10 {print NR,$4,$5,$6}' OFS="\t"

# awk 与数组组合实战，统计passwd文件用户数
awk -F':' 'BEGIN{count=0;}{name[count]=$1;count++;};END{for(i=0;i<NR;i++)print i,name[i]}' /etc/passwd

# 分析nginx访问日志的状态码404、502,统计次数大于20的IP地址, ~可理解为等于
awk '{if($9~/502|499|500|503|504/) print $1 ,$9}' access.log |sort|uniq -c|sort -nr|awk '{if($1>20) print $2}'

# 用/etc/shadow文件中的密文部分替换/etc/passwd中的'x'位置,生成/tmp/passwd文件
awk 'BEGIN{OFS=FS=":"}NR==FNR{a[$1]=$2}NR>FNR'{$2=a[$1];print>>"./passwd"}' /etc/shadow/passwd

# 统计服务器状态连接数
netstat -an | awk '/tcp/{s[$NF]++}END{for(a in s){print a,s[a]}}'
netstat -an | awk '/tcp/{print $NF}|sort|uniq -c'
```

⭐NF表示最后一列

⭐END表示等前面对每一行都执行完，再执行下一句

⭐BEGIN 模式：是指 awk 将在读取任何输入行之前立即执行BEGIN 中指定的动作。

⭐END 模式：是指 awk 将在它正式退出前执行 END中指定的动作。

⭐NR,与NR功用类似,不同的是awk每打开一个新文件,FNR便从0重新累计

⭐uniq只能剔除临近相同的行，要剔除全部相同的行要先使用sort排序



### grep

global search regular expression,用正则表达式搜索文本，并把匹配的行打印出来。

```shell
grep -[acinv] 'word' Filename
```

##### 常用选项

- -a：以文本文件方式搜索
- -c：计算得到的符合行的次数
- -i：忽略大小写
- -n：顺便输出行号
- -v：反向选择，显示不包含匹配文本的所有行
- -h：查询多文件时不显示文件名
- -l：查询多文件时只输出包含匹配字符的文件名
- -s：不显示不存在或无匹配字符的文件名
- -E：使用egrep扩展模式匹配
- -o：只显示匹配的内容

##### 通配符类型

- *：0个或多个
- ？：任意一个字符
- #：注释
- |：管道符号
- ；：连续多个命令连续执行
- &：后台执行指令
- ！：逻辑运算非
- []：内容范围
- {}：命令块，多个命令匹配

### 正则表达式

- *：匹配前一个字符0次或多次

- .：匹配除了换行符以外任意一个字符
- .*：匹配任意字符
- ^：匹配行首
- $：匹配行尾
- []：匹配中括号里任意一个字符
- [^]：匹配中括号以外任意一个字符

- {n}：匹配字符出现n次
- {n，}：匹配字符大于等于n次
- {n, m}：匹配字符出现n~m次
- \w：匹配文字和数字字符
- \W：匹配一个或多个非单词字符
- \s：匹配任何空白字符
- \d：相当于[0-9]
- \\<：锁定单词开头
- \\>：所锁定单词结尾

##### 案例

```shell
grep -c "test" # 统计包含test字符总行数
grep -i TEST # 不区分大小写包含test的行
grep -n test # 打印包含test的行及行号
grep 'test[53]' # 打印test接5或3的行
grep "^[^test]" # 打印首行不是test的行
grep "K...D" #K和D之间三个任意字符
grep -vE "#|^$" # 不匹配#和空行
grep -E '\<([0-9]{1,3}\.){3}([0-9]{1,3})\>' # 匹配IPv4地址
```

