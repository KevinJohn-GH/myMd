## 第10章 以太网协议 IEEE 802.3

### MAC帧格式

![数据链路层---以太网/MAC帧/ARP协议详解_weixin_42617262的博客-CSDN博客](https://img-blog.csdnimg.cn/20190327200430886.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjYxNzI2Mg==,size_16,color_FFFFFF,t_70)

### CSMA/CD

Carrier Sence Multiple Access/Collision Detection

**发送方**

1. 传输前侦听

2. 如果电缆忙则等待

3. 传输并检测冲突

4. 冲突发生，重传前等待

5. 重传或夭折

   > 最大允许重传次数为15次。每次重传回退时间为一个随机数乘以512bit时间。

**接收方**

1. 检查是否为碎片（长度是否小于64B）
2. 检验目地地址
3. 检验帧的完整性
   - 帧是否超过1518B，超过则为超长帧
   - CRC是否正确
4. 处理帧