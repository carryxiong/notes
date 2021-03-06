##  为什么tcp连接需要三次握手才能建立连接
主要是为了初始化sequence number的初始值，通信的双方要互相通知双方的sequence number，这个要作为以后数据通信的序号，保证以后不会因为网络上的传输问题而乱序，tcp会使用这个序号来拼接数据。因此，在服务器回发它的sequence number以后，还需要发送确认报文发送给服务器告知服务器客户端已经收到了你的报文。
因此，如果只是两次握手的话，那么client发送一个请求，server接收到，在回复一个，这就表示server收到了client的sequence number，但是如果client不给server回复一个消息的话，那么server将无法确定client是否已经收到了自己的seq。
![](https://img2018.cnblogs.com/blog/1690472/202002/1690472-20200204222517903-1357410709.png)
****
1. 第一次握手
客户端向服务端发送连接请求报文段。该报文段的头部中SYN=1，ACK=0，同时选择一个初始序号seq=x。请求发送后，客户端便进入SYN-SENT状态。

2. 第二次握手
服务端收到连接请求报文段后，如果同意连接，会发送一个应答：SYN=1，ACK=1，seq=y，ack=x+1。发送完应答后服务端进入SYN-RCVD状态。

3. 第三次握手
客户端收到服务端连接同意的应答后，还会向服务端发送一个确认报文段，表示：服务端发来的连接同意应答已经成功收到。该报文段的头部为：ACK=1，seq=x+1，ack=y+1。该报文发送完毕后，客户端和服务器端都进入ESTABLISHED状态，完成TCP三次握手。