[TOC]

# Docker容器

![](https://miro.medium.com/max/600/1*joAfS_1sBhCOJzJuaAzzeg.png)

#### Dockerfile 安装

官方文档：[docs.docker.comg](https://docs.docker.com/)

更换国内镜像：阿里云容器服务

#### Docker基本命令

- ``docker search -s LikeNumber MirrorName``

  查找镜像，-s点赞数量大于LikeNumber

- ``docker run -it MirrorName/MirrorID Commend``

  根据镜像创建实例

- ``docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]]``

  根据容器创建镜像

- ``docker attach MirrorID``

  再次进入容器

- ``docker exec MirrorID Commend``

  不进入容器，执行费Commend命令

- ``docker pull MirrorName``

  拉取镜像

  ​

## Dcoker与虚拟机区别

- **Docker守护进程**可以直接与**主操作系统**进行通信，为各个**Docker容器**分配资源；它还可以将容器与**主操作系统**隔离，并将各个容器互相隔离。**虚拟机**启动需要数分钟，而**Docker容器**可以在数毫秒内启动。由于没有臃肿的**从操作系统**，Docker可以节省大量的磁盘空间以及其他系统资源。
- Docker容器并不是加载整个虚拟机，所有虚拟机共享一个**bootfs** ，使用不同的**rootfs** 

![](https://www.netadmin.com.tw/upload/news/NP171002000217100217562801.jpg)



## Docker 数据卷

用于容器之间，容器与宿主机之间共享数据。

``docker run -it -v 宿主目录:容器目录``

``--volumes-from``

- 顾名思义，就是从另一个容器当中挂载容器中已经创建好的数据卷。



## Dockerfile

#### Dockerfile基础内容

1. 每条保留字指令都必须为全大写字母且必须至少带一个参数
2. 指令由上到下，顺序执行
3. `#` 表示注释
4. 每条指令都会创建一个新的镜像层，并对其提交
5. `scartch`所有镜像的祖先镜像

![](https://www.netadmin.com.tw/upload/news/NP171002000217100217562804.jpg)

#### Docker执行Dockerfile大致流程

``docker build -f dockerfile -t MirrorName .``

在当前目录根据dockerfile创建镜像

1. docker从基础镜像执行一个容器
2. 执行一条指令并对容器进行修改
3. 执行类似docker commit的操作提交一个镜像
4. docker再基于刚刚提交的镜像创建一个新的容器
5. 执行Dockerfile中的下一条指令直至所有指令都执行完成

#### Dockerfile体系结构（保留字指令）

1. **From**

   基础镜像，当前新镜像是基于哪个镜像的

2. **MAINTAINER**

   镜像维护者的姓名和邮箱地址

3. **RUN**

   容器构建的时候需要运行的命令

4. **EXPOSE**

   当前容器对外暴露的服务端口号

5. **WORKDIR**

   进入容器后，默认的工作目录，落脚点

6. **ENV**

   用来在构建镜像过程中设置环境变量

7. **ADD**

   将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和 解压tar压缩包

8. **COPY**

   语法：``COPY src dir``

   类似ADD但不解压缩

9. **VOLUME**

   容器数据卷，用于数据报保存和持久化工作

10. **CMD**

    指定一个容器启动的时候要执行的命令

    Dockerfile可以有多个CMD命令，但是只有最后一个生效，CMD会被`docker run`后面的参数替换

11. **ENTRYPOINT**

    指定一个容器启动时要运行的命令

    ENTRYPOINT	的目的和CMD一样，都是指定容器启动程序和参数

    但是不会被覆盖，而是追加

12. **ONBUILD**

    当当前Dockerfile被继承的时候，ONBUILD被触发

##### 参考 [gitbook](https://yeasy.gitbooks.io/docker_practice/install/ubuntu.html)