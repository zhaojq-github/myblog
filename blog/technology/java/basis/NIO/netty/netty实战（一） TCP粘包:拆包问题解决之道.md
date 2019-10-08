# Netty实战（一） TCP粘包/拆包问题解决之道

发表于 2016-07-24 | Java框架 | Netty

文章目录

1. [1. 什么是TCP 粘包/拆包](http://blog.720ui.com/2016/netty_01_tcp_pack/#%E4%BB%80%E4%B9%88%E6%98%AFTCP-%E7%B2%98%E5%8C%85-%E6%8B%86%E5%8C%85)
2. [2. TCP粘包/拆包 解决办法](http://blog.720ui.com/2016/netty_01_tcp_pack/#TCP%E7%B2%98%E5%8C%85-%E6%8B%86%E5%8C%85-%E8%A7%A3%E5%86%B3%E5%8A%9E%E6%B3%95)
3. 3. netty的几种解决方案
   1. [3.1. LineBasedFrameDecoder](http://blog.720ui.com/2016/netty_01_tcp_pack/#LineBasedFrameDecoder)
   2. [3.2. FixedLengthFrameDecoder](http://blog.720ui.com/2016/netty_01_tcp_pack/#FixedLengthFrameDecoder)
   3. [3.3. DelimiterBasedFrameDecoder](http://blog.720ui.com/2016/netty_01_tcp_pack/#DelimiterBasedFrameDecoder)
   4. [3.4. LengthFieldBasedFrameDecoder](http://blog.720ui.com/2016/netty_01_tcp_pack/#LengthFieldBasedFrameDecoder)

------

------

## 什么是TCP 粘包/拆包

TCP是个”流”协议，所谓流，就是没有界限没有分割的一串数据。TCP会根据缓冲区的实际情况进行包划分，一个完整的包可能会拆分成多个包进行发送，也用可能把多个小包封装成一个大的数据包发送。这就是TCP粘包/拆包。

发生TCP粘包/拆包，主要是由于下面一些原因：

- 应用程序写入的数据大于套接字缓冲区大小，这将会发生拆包。
- 应用程序写入数据小于套接字缓冲区大小，网卡将应用多次写入的数据发送到网络上，这将会发生粘包。
- 进行MSS（最大报文长度）大小的TCP分段，当TCP报文长度-TCP头部长度>MSS的时候将发生拆包。
- 接收方法不及时读取套接字缓冲区数据，这将发生粘包。

## TCP粘包/拆包 解决办法

- 设置定长消息，服务端每次读取既定长度的内容作为一条完整消息。
- 设置消息边界，服务端从网络流中按消息编辑分离出消息内容。
- 使用带消息头的协议、消息头存储消息开始标识及消息长度信息，服务端获取消息头的时候解析出消息长度，然后向后读取该长度的内容。
- 更复杂的应用层协议

## netty的几种解决方案

### LineBasedFrameDecoder

- 解码器说明：文本解码器
- 参数说明：

```
maxLength：解码的帧的最大长度
```

- 代码案例

```
//设置定长解码器 长度设置为30
ch.pipeline().addLast(new LineBasedFrameDecoder(1024));
ch.pipeline().addLast(new StringDecoder());
```

### FixedLengthFrameDecoder

- 解码器说明：定长解码器
- 参数说明：

```
frameLength：帧的固定长度
```

- 代码案例

```
//设置定长解码器 长度设置为30
ch.pipeline().addLast(new FixedLengthFrameDecoder(30));
```

### DelimiterBasedFrameDecoder

- 解码器说明：特殊分隔符解码器
- 参数说明：

```
- maxFrameLength：解码的帧的最大长度
- stripDelimiter：解码时是否去掉分隔符
- failFast：为true，当frame长度超过maxFrameLength时立即报TooLongFrameException异常，为false，读取完整个帧再报异常
- delimiter：分隔符
```

- 代码案例

```
String message = "netty is a nio server framework &"
                +"which enables quick and easy development &"
                +"of net applications such as protocol &"
                +"servers and clients!";
```

```
ByteBuf delimiter = Unpooled.copiedBuffer("&".getBytes());
//1024表示单条消息的最大长度，解码器在查找分隔符的时候，达到该长度还没找到的话会抛异常
ch.pipeline().addLast(new DelimiterBasedFrameDecoder(1024,delimiter));
```

### LengthFieldBasedFrameDecoder

- 解码器说明：基于包头不固定长度的解码器
- 参数说明：

```
- maxFrameLength：解码的帧的最大长度
- lengthFieldOffset：长度属性的起始位（偏移位），包中存放有整个大数据包长度的字节，这段字节的其实位置
- lengthFieldLength：长度属性的长度，即存放整个大数据包长度的字节所占的长度
- lengthAdjustmen：长度调节值，在总长被定义为包含包头长度时，修正信息长度。
- initialBytesToStrip：跳过的字节数，根据需要我们跳过lengthFieldLength个字节，以便接收端直接接受到不含“长度属性”的内容
- failFast ：为true，当frame长度超过maxFrameLength时立即报TooLongFrameException异常，为false，读取完整个帧再报异常
```

- 代码案例

```
ch.pipeline().addFirst(new LengthFieldBasedFrameDecoder(100000000,0,4,0,4)); 
```

LengthFieldBasedFrameDecoder使用详情，[可以参考](http://blog.163.com/linfenliang@126/blog/static/127857195201210821145721/)

- 版权声明：本文由 **梁桂钊** 发表于 [梁桂钊的博客](http://blog.720ui.com/) 
- 转载声明：自由转载-非商用-非衍生-保持署名（[创意共享3.0许可证](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)），非商业转载请注明作者及出处，商业转载请联系作者本人。
- 文章标题：[Netty实战（一） TCP粘包/拆包问题解决之道](http://blog.720ui.com/2016/netty_01_tcp_pack/)
- 文章链接：<http://blog.720ui.com/2016/netty_01_tcp_pack/>