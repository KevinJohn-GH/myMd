## centos VMware装机没有获取IPv4地址

修改/etc/sysconfig/network-scripts/ifcfg-ens33文件ONBOOT参数

```shell
sed -i 's/ONBOOT=no/ONBOOT=yes/g' /etc/sysconfig/network-scripts/ifcfg-ens33
```

