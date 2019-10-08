[TOC]



# 学习WebSocket协议—从顶层到底层的实现原理（修订版）

### 从RealTime说起

自从即时Web的概念提出后，RealTime便成为了web开发者们津津乐道的话题。实时化的web应用，凭借其响应迅速、无需刷新、节省网络流量的特性，不仅让开发者们眼前一亮，更是为用户带来绝佳的网络体验。

近年来关于RealTime的实现，主要还是基于Ajax的拉取和Comet的推送。大家都知道Ajax，这是一种借助浏览器端JavaScript实现的异步无刷新请求功能：要客户端按需向服务器发出请求，并异步获取来自服务器的响应，然后按照逻辑更新当前页面的相应内容。但是这仅仅是**拉取**啊，这并不是真正的RealTime：缺少服务器端的自动推送！因此，我们不得不使用另一种略复杂的技术Comet，只有当这两者配合起来，这个web应用才勉强算是个RealTime应用！

### Hello WebSocket！

![image-20180926141757082](image-201809261417/image-20180926141757082.png)

不过随着HTML5草案的不断完善，越来越多的现代浏览器开始全面支持WebSocket技术了。至于WebSocket，我想大家或多或少都听说过。

这个WebSocket是一种全新的协议。它将TCP的Socket（套接字）应用在了web page上，从而使通信双方建立起一个保持在活动状态连接通道，并且属于**全双工**（双方同时进行双向通信）。

其实是这样的，WebSocket协议是借用HTTP协议的`101 switch protocol`来达到协议转换的，从HTTP协议切换成WebSocket通信协议。

再简单点来说，它就好像将Ajax和Comet技术的特点结合到了一起，只不过性能要高并且使用起来要方便的多（当然是之指在客户端方面。。）

### 设计哲学

RFC草案中已经说明，WebSocket的目的就是为了在基础上保证传输的数据量最少。
这个协议是基于Frame而非Stream的，也就是说，数据的传输不是像传统的流式读写一样按字节发送，而是采用一帧一帧的Frame，并且每个Frame都定义了严格的数据结构，因此所有的信息就在这个Frame载体中。（后面会详细介绍这个Frame）

#### 特点

- 基于TCP协议
- 具有命名空间
- 可以和HTTP Server共享同一port

### 打开连接-握手

下面我先用自然语言描述一下WebSocket的工作原理：
若要实现WebSocket协议，首先需要浏览器主动发起一个HTTP请求。

这个请求头包含“Upgrade”字段，内容为“websocket”（注：upgrade字段用于改变HTTP协议版本或换用其他协议，这里显然是换用了websocket协议），还有一个最重要的字段“Sec-WebSocket-Key”，这是一个随机的经过`base64`编码的字符串，像密钥一样用于服务器和客户端的握手过程。一旦服务器君接收到来自客户端的upgrade请求，便会将请求头中的“Sec-WebSocket-Key”字段提取出来，追加一个固定的“魔串”：`258EAFA5-E914-47DA-95CA-C5AB0DC85B11`，并进行`SHA-1`加密，然后再次经过`base64`编码生成一个新的key，作为响应头中的“Sec-WebSocket-Accept”字段的内容返回给浏览器。一旦浏览器接收到来自服务器的响应，便会解析响应中的“Sec-WebSocket-Accept”字段，与自己加密编码后的串进行匹配，一旦匹配成功，便有建立连接的可能了（因为还依赖许多其他因素）。

这是一个基本的Client请求头：(我只写了关键的几个字段)

```
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: ************==
Sec-WebSocket-Version: **
```

Server正确接收后，会返回一个响应头：(同样只有关键的)

```
Upgrade：websocket
Connnection: Upgrade
Sec-WebSocket-Accept: ******************
```

这表示双方**握手**成功了，之后就是全双工的通信。

### 安全性限制

当你看完上面一节后一定会质疑该协议的保密性和安全性，看上去任何客户端都能够很容易的向WS服务器发起请求或伪装截获数据。WebSocket协议规定在连接建立时检查Upgrade请求中的某些字段（如`Origin`），对于不符合要求的请求立即截断；在通信过程中，也对Frame中的控制位做了很多限制，以便禁止异常连接。

对于握手阶段的检查，这种限制仅仅是在浏览器中，对于特殊的客户端（non-browser，如编码构造正确的请求头发送连接请求），这种源模型就失效了。

（后面会介绍通信过程中的**连接关闭**种类与流程。）

除此之外，WebSocket也规定了加密数据传输方法，允许使用TLS/SSL对通信进行加密，类似HTTPS。默认情况下，ws协议使用80端口进行普通连接，加密的TLS连接默认使用443端口。

### 和TCP、HTTP协议的关系

WebSocket是基于TCP的独立的协议。
和HTTP的唯一关联就是HTTP服务器需要发送一个“Upgrade”请求，即`101 Switching Protocol`到HTTP服务器，然后由服务器进行协议转换。

### ws的子协议

客户端向服务器发起握手请求的header中可能带有“Sec-WebSocket-Protocol”字段，用来指定一个特定的子协议，一旦这个字段有设置，那么服务器需要在建立连接的响应头中包含同样的字段，内容就是选择的子协议之一。

子协议的命名应该是注册过的（有一套规范）。
为了避免潜在的冲突，建议子协议的源（发起者）使用ASCII编码的域名。
例子：
一个注册过的子协议叫“chat.xxx.com”，另一个叫“chat.xxx.org”。这两个子协议都会被server同时实现，server会动态的选择使用哪个子协议（取决于客户端发送过来的值）。

### Extensions

扩展是用来增加ws协议一些新特性的，这里就不详细说了。

### 建立连接部分代码

上面说的仅仅是个概述，重要的是该如何在我们的web应用中使用或者说该如何建立一个基于WebSocket的应用呢？

我直说了，客户端使用WebSocket简直易如反掌，服务端实现WebSocket真是难的一B啊！尤其是我们现在还没有学过计算机网络，对一些网络底层的（如TCP/IP协议）知识了解的太少，理解并实现WebSocket确实不太容易。所以这次我先把WebSocket用提供一部分接口的高级语言来实现。

Node.js的异步I/O模型实在是太适合这种类型的应用了，因此我选择它作为I/O编程的首选。来看下面的JavaScript代码～：
*Note：以下代码仅用于阐明原理，不可用于生产环境！*

```js
      var http = require('http');
    var crypto = require('crypto');

    var MAGIC_STRING = "258EAFA5-E914-47DA-95CA-C5AB0DC85B11";

    // HTTP服务器部分
    var server = http.createServer(function (req, res) {
      res.end('websocket test\r\n');
    });

    // Upgrade请求处理
    server.on('upgrade', callback);

    function callback(req, socket) {
      // 计算返回的key
      var resKey = crypto.createHash('sha1')
        .update(req.headers['sec-websocket-key'] + MAGIC_STRING)
        .digest('base64');

      // 构造响应头
      resHeaders = ([
        'HTTP/1.1 101 Switching Protocols',
        'Upgrade: websocket',
        'Connection: Upgrade',
        'Sec-WebSocket-Accept: ' + resKey
      ]).concat('', '').join('\r\n');

      // 添加通信数据处理
      socket.on('data', function (data) {
        // ...
      });

      // 响应给客户端
      socket.write(resHeaders);
    }

    server.listen(3000);
```

上面的代码是等待客户端与之握手，当有客户端发出请求时，会按照“加密-编码-返回”的流程与之建立通信通道。既然连接已建立，接下来就是双方的通信了。为了让大家明白WebSocket的全程使用，在此之前有必要提一下支持WebSocket的底层协议的实现。

## 协议

协议这种东西就像某种魔法，赋予了计算机之间各种神奇的通信能力，但对用户来说却是透明的。
不过对于WebSocket协议，我们可以透过IETF的RFC规范，看到关于实现WebSocket细节的每次变更与修正。

### Frame

前面已經说过了WebSocket在客户端与服务端的“Hand-Shaking”实现，所以这里讲数据传输。
WebSocket传输的数据都是以`Frame`（帧）的形式实现的，就像TCP/UDP协议中的报文段`Segment`。下面就是一个Frame：（以bit为单位表示）

```
  0                   1                   2                   3
  0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
 +-+-+-+-+-------+-+-------------+-------------------------------+
 |F|R|R|R| opcode|M| Payload len |    Extended payload length    |
 |I|S|S|S|  (4)  |A|     (7)     |             (16/64)           |
 |N|V|V|V|       |S|             |   (if payload len==126/127)   |
 | |1|2|3|       |K|             |                               |
 +-+-+-+-+-------+-+-------------+ - - - - - - - - - - - - - - - +
 |     Extended payload length continued, if payload len == 127  |
 + - - - - - - - - - - - - - - - +-------------------------------+
 |                               |Masking-key, if MASK set to 1  |
 +-------------------------------+-------------------------------+
 | Masking-key (continued)       |          Payload Data         |
 +-------------------------------- - - - - - - - - - - - - - - - +
 :                     Payload Data continued ...                :
 + - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - +
 |                     Payload Data continued ...                |
 +---------------------------------------------------------------+
```

按照RFC中的描述：

- FIN： 1 bit

  ```
  表示这是一个消息的最后的一帧。第一个帧也可能是最后一个。  
  %x0 : 还有后续帧  
  %x1 : 最后一帧
  ```

- RSV1、2、3： 1 bit each

  ```
  除非一个扩展经过协商赋予了非零值以某种含义，否则必须为0
  如果没有定义非零值，并且收到了非零的RSV，则websocket链接会失败
  ```

- Opcode： 4 bit

  ```
  解释说明 “Payload data” 的用途/功能
  如果收到了未知的opcode，最后会断开链接
  定义了以下几个opcode值:
      %x0 : 代表连续的帧
      %x1 : text帧
      %x2 ： binary帧
      %x3-7 ： 为非控制帧而预留的
      %x8 ： 关闭握手帧
      %x9 ： ping帧
  %xA :  pong帧
  %xB-F ： 为非控制帧而预留的
  ```

- Mask： 1 bit

  ```
  定义“payload data”是否被添加掩码
  如果置1， “Masking-key”就会被赋值
  所有从客户端发往服务器的帧都会被置1
  ```

- Payload length： 7 bit | 7+16 bit | 7+64 bit

  ```
  “payload data” 的长度如果在0~125 bytes范围内，它就是“payload length”，
  如果是126 bytes， 紧随其后的被表示为16 bits的2 bytes无符号整型就是“payload length”，
  如果是127 bytes， 紧随其后的被表示为64 bits的8 bytes无符号整型就是“payload length”
  ```

- Masking-key： 0 or 4 bytes

  ```
  所有从客户端发送到服务器的帧都包含一个32 bits的掩码（如果“mask bit”被设置成1），否则为0 bit。一旦掩码被设置，所有接收到的payload data都必须与该值以一种算法做异或运算来获取真实值。（见下文）
  ```

- Payload data: (x+y) bytes

  ```
  它是"Extension data"和"Application data"的总和，一般扩展数据为空。
  ```

- Extension data: x bytes

  ```
  除非扩展被定义，否则就是0
  任何扩展必须指定其Extension data的长度
  ```

- Application data: y bytes

  ```
  占据"Extension data"之后的剩余帧的空间
  ```

**注意：这些数据都是以二进制形式表示的，而非ascii编码字符串**

#### 构造Frame

Frame的结构已经清楚了，我们就构造一个Frame。
在构造时，我们可以把Frame分成两段：**控制位**和**数据位**。其中控制位就是**Frame的前两字节**，包含FIN、Opcode等与该Frame的元信息。

**Note：网络中使用大端次序（Big endian）表示大于一字节的数据，称之为网络字节序。**
Node.js中提供了Buffer对象，专门用来弥补JavaScript在处理字节数据上的不足，这里正好可以用它来完成这个任务：

```
  // 控制位: FIN, Opcode, MASK, Payload_len
  var preBytes = [], 
      payBytes = new Buffer('test websocket'), 
      mask = 0;
      masking_key = Buffer.randomByte(4);

  var dataLength = payBytes.length;

  // 构建Frame的第一字节
  preBytes.push((frame['FIN'] << 7) + frame['Opcode']);

  // 处理不同长度的dataLength，构建Frame的第二字节（或第2～第8字节）
  // 注意这里以大端字节序构建dataLength > 126的dataLenght
  if (dataLength < 126) {
    preBytes.push((frame['MASK'] << 7) + dataLength);
  } else if (dataLength < 65536) {
    preBytes.push(
      (frame['MASK'] << 7) + 126, 
      (dataLength & 0xFF00) >> 8,
      dataLength & 0xFF
    );
  } else {
    preBytes.push(
      (frame['MASK'] << 7) + 127,
      0, 0, 0, 0,
      (dataLength & 0xFF000000) >> 24,
      (dataLength & 0xFF0000) >> 16,
      (dataLength & 0xFF00) >> 8,
      dataLength & 0xFF
    );
  }

  preBytes = new Buffer(preBytes);

  // 如果有掩码，就对数据进行加密，并构建之后的控制位
  if (mask) {
    preBytes = Buffer.concat([preBytes, masking_key]);
    for (var i = 0; i < dataLength; i++) 
      payBytes[i] ^= masking_key[i % 4];
  }

  // 生成一个Frame
  var frame = Buffer.concat([preBytes, payBytes]);
```

按照这种格式，就定义好了一个帧，客户端或者服务器就可以用这个帧来互传数据了。既然数据已经接收，接下来看看如何处理这些数据。

#### Masking

规范里解释了`Masking-key`掩码的作用了：就是当`mask`字段的值为1时，`payload-data`字段的数据需要经这个掩码进行解密。

在处理数据之前，我们要清楚一件事：服务器推送到客户端的消息中，`mask`字段是0,也就是说`Masking-key`为空。这样的话，数据的解析就不涉及到掩码，直接使用就行。

但是我们前面提到过，如果消息是从客户端发送到服务器，那么`mask`一定是1,`Masking-key`一定是一个32bit的值。下面我们来看看数据是如何解析的：

当消息到达服务器后，服务器程序就开始以字节为单位逐步读取这个帧，当读取到`payload-data`时，首先将数据按byte依次与`Masking-key`中的4个byte按照如下算法做异或：

```
      //假设我们发送的"Payload data"以变量`data`表示，字节（byte）数为len;
      //masking_key为4byte的mask掩码组成的数组
    //offset：跳过的字节数

    for (var i = 0; i < len; i++) {
        var j = i % 4;
        data[offset + i] ^= masking_key[j];
    }
```

上面的JavaScript代码给出了掩码`Masking-key`是如何解密`Payload-data`的：先对i取模来获得要使用的masking-key的索引，然后用`data[offset + i]`与`masking_key[j]`做异或，从而得到真实的byte数据。

#### 控制帧

控制帧用来说明WebSocket的状态信息，用来控制分片、连接的关闭等等。所有的控制帧必须有一个小于等于125字节的payload，并且**control Frames不允许被分片**。`Opcode`为`0x0`（持续的帧），`0x8`（关闭连接），`0x9`（Ping帧）和`0xA`（Pong帧）代表控制帧。

一般Ping Frame用来对一个有超时机制的套接字keepalive或者验证对方是否有响应。Pong Frame就是对Ping的回应。

#### 数据帧

前面我们总是谈到“控制帧”和“非控制帧”，想必大家已經看出来一些门路。其实数据帧就是非控制帧。因为这个帧并不是用来提供协议连接状态信息的。数据帧由最高符号位是0的`Opcode`确定，现在可用的几个数据帧的Opcode是`0x1`（utf-8文本）、`0x2`（二进制数据）。

### 分片（Fragment）

理论上来说，每个帧（Frame）的大小是没有限制的，因为payload-data在整个帧的最后。但是发送的数据有不能太大，否则 WebSocket 很可能无法[高效的利用网络带宽](https://github.com/abbshr/abbshr.github.io/issues/22#issuecomment-261436452)。那如果我们想传点大数据该怎么办呢？WebSocket协议给我们提供了一个方法：分片，将原本一个大的帧拆分成数个小的帧。下面是把一个大的Frame分片的图示：

```
  编号：      0  1  ....  n-2 n-1
  分片：     |——|——|......|——|——|
  FIN：      0  0  ....   0  1
  Opcode：   !0 0  ....   0  0
```

由图可知，第一个分片的`FIN`为0，`Opcode`为非0值（0x1或0x2），最后一个分片的`FIN`为1，`Opcode`为0。中间分片的`FIN`和`Opcode`二者均为0。

**Note1：消息的分片必须由发送者**按给定的顺序**发送给接收者。**

*Note2：控制帧禁止分片*

**Note3：接受者不必按顺序缓存整个frame来处理**

### 关闭连接

#### 正常的连接关闭流程

1. 发送关闭连接请求（Close Handshake）
   即发送`Close Frame`（Opcode为0x8）。一旦一端发送/接收了一个Close Frame，就开始了Close Handshake，并且连接状态变为`Closing`。
   Close Frame中如果包含Payload data，则data的**前2字节**必须为两字节的无符号整形，（同样遵循网络字节序：BE）用于表示**状态码**，如果2byte之后仍有内容，则应包含utf-8编码的**关闭理由**。
   如果一端在之前未发送过Close Frame，则当他收到一个Close Frame时，必须回复一个Close Frame。但如果它正在发送数据，则可以推迟到当前数据发送完，再发送Close Frame。比如Close Frame在分片发送时到达，则要等到所有剩余分片发送完之后，才可以作出回复。
2. 关闭WebSocket连接
   当一端已经收到Close Frame，并已发送了Close Frame时，就可以关闭连接了，close handshake过程结束。这时丢弃所有已经接收到的末尾字节。
3. 关闭TCP连接
   当底层TCP连接关闭时，连接状态变为`Closed`。

#### clean closed

如果TCP连接在Close handshake完成之后关闭，就表示WebSocket连接已经**clean closed**（彻底关闭）了。
如果WebSocket连接并未成功建立，状态也为连接已关闭，但并不是`clean closed`。

#### 正常关闭

正常关闭过程属于`clean close`，应当包含`close handshake`。

通常来讲，应该由服务器关闭底层TCP连接，而客户端应该等待服务器关闭连接，除非等待超时的话，那么自己关闭底层TCP连接。

服务器可以随时关闭WebSocket连接，而客户端不可以主动断开连接。

#### 异常关闭

1. 由于某种算法或规定，一端直接关闭连接。（特指在open handshake（打开连接）阶段）
2. 底层连接丢失导致的连接中断。

#### 连接失败

由于某种算法或规范要求指定连接失败。这时，客户端和服务器必须关闭WebSocket连接。当一端得知连接失败时，**不准**再处理数据，包括**响应close frame**。

#### 从异常关闭中恢复

为了防止海量客户端同时发起重连请求（reconnect），客户端应该推迟一个随机时间后重新连接，可以选择回退算法来实现，比如**截断二进制指数退避算法**。

### 关于补充

这两篇blog里主要用自然语言讲了WebSocket的实现。代码的细节操作（例如：处理数据、安全处理等）并没有给出，因为核心实现原理已经阐明。

因为近期写了一个比较完整的WebSocket库[RocketEngine](https://github.com/abbshr/RocketEngine)，在编码过程中发现了好多需要注意的问题，特此加以补充和修正，增加了部分章节，改正了一些不精确的说法，同时将两篇日志合并。

如需详细学习，请戳=> [RocketEngine（附详细注释与wiki）](https://github.com/abbshr/RocketEngine/wiki/RocketEngine-V0.4.x--%E4%B8%AD%E6%96%87%E7%89%88Wiki)

（2014.12.28 修改补充）



https://github.com/abbshr/abbshr.github.io/issues/22