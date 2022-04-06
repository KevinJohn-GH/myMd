## SSH免密登录实现及原理

##### 客户机配置

1. 生成密钥对，如果用户家目录下.ssh文件里已经有id_rsa  id_rsa.pub则跳过这一步

    ```shell
    ssh-keygen	
    ```

##### 服务器配置

1. 生成`~/.ssh/authorized_keys`文件，并赋予600权限

2. 将客户机的id_rsa.pub内容拷贝到服务器的~/.ssh/authorized_keys文件中

3. 修改`/etc/ssh/sshd_config `文件，去掉注释

   ```shell
   RSAAuthentication yes
   PubkeyAuthentication yes
   AuthorizedKeysFile      %h/.ssh/authorized_keys
   ```

4. 重启ssh

    ```shell
    sudo systemctl restart sshd 
    ```

##### 原理

![](https://cdn.jsdelivr.net/gh/KevinJohn-GH/pictures/img/20201117170759.png)


