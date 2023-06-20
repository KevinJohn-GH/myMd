## 批量telnet检测远端服务器端口

```shell
#!/bin/bash
echo `date`
while read line
do
    echo $line
    timeout 0.5 telnet $line < /dev/null
    echo ''
done < inPut
```

从inPut文件中读物IP和端口号，循环执行telnet

🚦注意：

- 像ssh与telnet这种命令，会接受全部输入，所以要将telnet命令的输入重定向到/dev/null。

否则telnet只执行一次，将inPut的内容全部吃掉。

- timeout + 时间 用于自动终止telnet这种交互式的程序
- 重定向只对当前命令有效

另一种实现方式

```shell
#!/bin/bash
echo `date`
exec 3<inPut
while read line <&3
do
    echo $line
    timeout 0.5 telnet $line
    echo ''
done
```

- shell描述符有12个
- 3-9都是空白
- exec执行后，后面的不在执行！
- exec 命令可以永久性地重定向，后续命令的输入输出方向也被确定了，文件描述符读写的记录也会被保存
- `exec 100>&-`注销该文件描述符