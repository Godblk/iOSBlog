# 大话Socket


要了解Socket首先要了解 TCP，他们两个的关系可以说是：

Socket 是抽象出来的使用 TCP/UDP 的概念模型，屏蔽掉了晦涩的底层协议的实现，是一个接口。


最近看到了一张如此详细的 TCP 三次握手和四次挥手，打印一张放工位！摘自《图解网络硬件》249页 图5-11 《TCP的三次握手》

![enter image description here](http://ww4.sinaimg.cn/large/64dfd849jw1f2udfv668pj20m00nm44c)

![《TCP三次握手图解：iOS的Socket开发基础
http://www.coderyi.com/archives/429》](http://ww1.sinaimg.cn/large/65e4f1e6jw1f7u2xjocatj20j80dbq46.jpg)


所谓的`X、X+1`、`Y、Y+1`
对应于`你收到了没、我收到了`、`你收到'我收到'没、我收到了不用回了`,为什么用`+1`表示呢？那是因为前两个指的是一个人，后两个指的是一个人。
四组是三个连接，每个连接的序号依次是X、Y、Z。


TCP的连接过程就像两个人的对话：

想象一下，每次这俩儿人聊天，都要像下面这样一来一回三次，接下来他们才能【好好聊天了。。。】真是有点“作”。。。

我是客户端，树懒是服务端，演示三次握手、数据传输步骤


![enter image description here](http://ww4.sinaimg.cn/large/64dfd849jw1f2udg97aymj20m81mck1l)


其实有个问题，为什么连接的时候是三次握手，关闭的时候却是四次挥手？

因为当Server端收到Client端的SYN连接请求报文后，可以直接发送SYN+ACK报文。其中ACK报文是用来应答的，SYN报文是用来同步的。但是关闭连接时，当Server端收到FIN报文时，很可能并不会立即关闭SOCKET，所以只能先回复一个ACK报文，告诉Client端，”你发的FIN报文我收到了”。只有等到我Server端所有的报文都发送完了，我才能发送FIN报文，因此不能一起发送。故需要四步握手。



而这一设计，主要是因为“服务器不是你想关就能关”。。。



比如说两个热恋中的人正在QQ上发送一个传mp4格式的文件，

A说，我要下QQ了，

B说：我知道了，你下吧。

A说，那我关了，（想关）

但是当A尝试关闭QQ的时候，QQ弹窗说“正在传输文件，传输完成后自动关闭QQ？”

 这时候A对B说，呀，正在传东西，等传完了，我就关吧。（不能关）

B说：行。既然关不掉，不行再聊会儿呗？

A：聊吧。。。传完了啊，下了啊（传输结束了--能关）

B：下吧。我也下了。。。

就是多了一个Finish报文。


或者简单点表示是这样的：

![enter image description here](http://ww4.sinaimg.cn/large/64dfd849jw1f2ujpg4b5qj20yi1pcqdp)

图片演示了四次挥手，与三次握手相比，只多了一个被动方确认自身任务Finish的动作。


![](http://ww2.sinaimg.cn/large/65e4f1e6jw1f7u2xjsgn8j20j60dktah.jpg)


总结下相关的函数：

创建套接字

       Socket(af,type,protocol)
       
建立地址和套接字的联系

       bind(sockid, local addr, addrlen)
服务器端侦听客户端的请求

       listen( Sockid ,quenlen)
建立服务器/客户端的连接 (面向连接TCP）

客户端请求连接

        Connect(sockid, destaddr, addrlen)
服务器端等待从编号为Sockid的Socket上接收客户连接请求

        newsockid=accept(Sockid，Clientaddr, paddrlen)
发送/接收数据

面向连接：

        send(sockid, buff, bufflen) 
        recv( )
面向无连接：

        sendto(sockid,buff,…,addrlen) 
        recvfrom( )
释放套接字

        close(sockid)


至于为什么是三次握手，而不是二次握手，还有四次握手，可以看看下面的类比：

三次握手 | 二次握手 | 四次握手
-------------|-------------|-------------
![](http://ww2.sinaimg.cn/large/65e4f1e6jw1f7u56wfxhzj20yi1pc3zv.jpg)| ![](http://ww3.sinaimg.cn/large/65e4f1e6jw1f7u4zd30npj20yi1pcqad.jpg) |![](http://ww4.sinaimg.cn/large/65e4f1e6jw1f7u4xasma0j20yi1pcacb.jpg)

参考：http://zhihu.com/question/24853633/answer/114872771

三次握手的对话，也挺像这个的，哈哈：

A: How are you?
B: I'm fine, thanks,and you?
A: I'm fine too.
