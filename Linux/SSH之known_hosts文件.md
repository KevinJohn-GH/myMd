## SSH之known_hosts文件

1. A首次ssh连接到B，B会将host_key公钥发送给A

2. A将公钥存到本地known_hosts中
3. 以后A再次ssh连接B，B会再次传递公钥给A
4. 若B发送到的公钥与A本地存储的公钥不一致，则触发异常

A重新生成本地公钥解决警告

![img](https://cdn.jsdelivr.net/gh/KevinJohn-GH/pictures/img/20201223155527.jpeg)

```shell
ssh-keygen -R 192.168.0.100
```

