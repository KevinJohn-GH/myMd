# python Socket套接字模块

- 用于Internet套接字的套接字API，又是成为Berkeley或BSD套接字
- Unix域套接字，它只能用于同一台主机上的进程之间的通信
### socket类型

``socket.socket([domain],[type],[protocol])``
- domain（套接字的域）：

  - ``AF_INET`` ,  ``AF_INET6`` ,  ``AF_UNIX``
  - 协议族指定了socket的地址类型，通信中必须采用相对应的地址
    - AF_INET决定了基于ipv4的服务器与服务器之间的网络通信
    - AF_INET6决定了基于ipv6的服务器与服务器之间的网络通信
    - AF_UNIX用于同一台及其上的进程通信（本机通信）

- tpye（套接字的类型）：

  - ``SOCK_STREAM`` ,  ``SOCK_DGREAM``
  - internet提供了两种通信机制：stream（流）和datagram（数据报）
  - 因此，套接字的类型也就分为流套接字和数据报套接字。python网络编程中，主要使用流套接字。
    - 流套接字``SOCK_STREAM``：提供一个有序、可靠、双向字节流的连接因此发送的数据可以确保不是丢失、重复或乱序到达。而且它还有一定的出错后重新发送的机制
    - 数据报套接字``SOCK_DGREAM``：与数据报先对的是由类型SOCK_DREAM指定的数据报套接字，它不需要建立和维持一个连接，它们在AF_INET中通常是通过UDP/IP协议实现
  - 流套接字由SOCK_STREAM指定，在AF_INET域中通过TCP/IP连接实现

- protocol（套接字的协议）：

  - 只要底层的传输机制允许不止一个协议来提供要求的套接字类型，就可以为套接字选择一个特定点1协议
  - 通常使用默认值

  ​


### socket函数

- TCP发送数据是，已建立号TCP连接，不需要指定地址，而UDP是面向无连接的，每次发送都需要指定发给谁
- 服务器与客户端之间不能直接发送列表、元组、字典等带有数据类型的格式，发送的内容必须是字符串数据

#####服务端Socket函数

- ``sock.bind(address)``

  - 将套接字绑定到地址，在AF_INET下，以tuple(host,port)传入
  - 服务端调用``lisen()``前会调用，客户端则是用``connect()``随机生成一个

- ``sock.lisen(backlog)`` 

  - backlog :在内核2.2之后。socket指等待accept的完全建立的套接字队列长度，而不是syncs不完全连接请求的数量。（即为用户连接数）

  ![tcp](https://upload-images.jianshu.io/upload_images/2184951-feff3fcc300fcfcd.png?imageMogr2/auto-orient/)

- ``accept()``

  - 接受client端连接请求，建立起与客户端之间的通信连接
  - 接受TCP连接并发挥（conn， address），其中conn是新的套接字对象，可以用来接受和发送数据，address是连接客户端的地址


###### TCP服务端代码

```python
import socket

server_socket = socket.socket(family=socket.AF_INET, type=socket.SOCK_STREAM)
server_socket.bind(('localhost', 8079))
server_socket.listen(3)
while True:
    conn, addr = server_socket.accept()
    info = conn.recv(1024).decode('ascii')
    print(str(info))
```

##### 客户端Socket函数

- ``sock.connect(address)``
  - 请求连接远端服务器，address 存储远端服务器的IP与端口信息(tuple)


- ``sock.connect_ex(address)``
  - 功能与``sock.connect(address)``相同，但成功返回0，失败返回errno的值

###### TCP服务端代码

```python
import socket
client_socket = socket.socket()
client_connect = client_socket.connect_ex(('localhost', 8079))
print(client_connect)
client_socket.send('haha'.encode('ascii'))
```

##### 公共Socket函数

- ``sock.recv([bufsize])``
  - 从缓冲区读取内容，bufsize指定缓冲区大小，单位字节
- ``sock.send(string)``
  - 将buf中的字节内容写入到 socket描述符（发送数据，但一次send不一定能全部发完）
  - 成功返回写的字节数，失败返回-1
- ``sock.sendall(string)``
  - 完整发送TCP数据，将字符串中的数据发送到连接的套接字，想到与循环调用```sock.send()``
  - 成功返回None，失败抛出异常
- ``sock.recvfrom(bufsize)``
  - 接受UDP套接字的数据，与recv类似，但返回值是tuple(data,address)
- ``sock.sendto(string,address)``
  - 发送UDP数据，address形式为tuple(ipaddr,port)，返回值是字节数
- ``sock.close()``
  - 关闭套接字
- ``sock.getpeername()``
  - 返回套接字的远程地址,tuple(ipaddr,port)
- ``sock.getsockname()``
  - 返回套接字自己的地址
- ``sock. settimeout()``
  - 设置套接字操作的超时时间，timeout是一个浮点数，单位是秒，值为None表示永远不会超时
- ``sock.gettimeout()``
  - 返回当前超时值，单位时秒，没有设置则返回None
- ``sock.setblocking(flag)``
  - 如果flag为0，则将套接字设置为非阻塞模式，否则将套接字设置为阻塞模式（默认值）
  - 非阻塞模式，如果调用recv()发现没有任何数据，或send()调用无法立即发送数据，那么将引起socket.error异常

######  UDP服务端

````python
import socket

server_socket = socket.socket(socket.AF_INET, type=socket.SOCK_DGRAM)
server_socket.bind(('localhost', 8079))

while True:
    data, address = server_socket.recvfrom(4)
    print(data.decode('ascii'))
````



###### UDP客户端

```python
import socket
client_socket = socket.socket(socket.AF_INET, type=socket.SOCK_DGRAM)
client_socket.sendto('haha'.encode('ascii'), ('localhost', 8079))
```



