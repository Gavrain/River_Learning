# HTTPS

HTTPS（全称：HyperText Transfer Protocol over Secure Socket Layer），其实 HTTPS 并不是一个新鲜协议，Google 很早就开始启用了，初衷是为了保证数据安全。 近两年，Google、Baidu、Facebook 等这样的互联网巨头，不谋而合地开始大力推行 HTTPS， 国内外的大型互联网公司很多也都已经启用了全站 HTTPS，这也是未来互联网发展的趋势。

为鼓励全球网站的 HTTPS 实现，一些互联网公司都提出了自己的要求：

1）Google 已调整搜索引擎算法，让采用 HTTPS 的网站在搜索中排名更靠前；

2）从 2017 年开始，Chrome 浏览器已把采用 HTTP 协议的网站标记为不安全网站；

3）苹果要求 2017 年 App Store 中的所有应用都必须使用 HTTPS 加密连接；

4）当前国内炒的很火热的微信小程序也要求必须使用 HTTPS 协议；

5）新一代的 HTTP/2 协议的支持需以 HTTPS 为基础。

等等，因此想必在不久的将来，全网 HTTPS 势在必行。

**1.通信使用明文(不加密),内容可能被窃听.(抓包工具可以获取请求和响应内容)**



![img](https:////upload-images.jianshu.io/upload_images/1217845-4775a59681bb9022.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



**2.不验证通讯方的身份,可能遭遇伪装.(任何人都能发送请求,不管对方是谁都会返回响应).**



![img](https:////upload-images.jianshu.io/upload_images/1217845-5cd6780ca1ba6ab0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/798/format/webp)





**3.无法证明报文的完整性,可能会遭篡改.(没有办法确认发出的请求/响应和接收到的请求/响应前后一致)**





![img](https:////upload-images.jianshu.io/upload_images/1217845-246d6338cb86b685.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



这些问题不止会在http上出现,在其他未加密的协议中也会出现这类问题，因此HTTPS随之而来。

## 概念

1、HTTP 协议（HyperText Transfer Protocol，超文本传输协议）：是客户端浏览器或其他程序与Web服务器之间的应用层通信协议 。

2、HTTPS 协议（HyperText Transfer Protocol over Secure Socket Layer）：可以理解为HTTP+SSL/TLS， 即 HTTP 下加入 SSL 层，HTTPS 的安全基础是 SSL，因此加密的详细内容就需要 SSL，用于安全的 HTTP 数据传输。

![img](https://pic1.zhimg.com/80/v2-54ff04e1b0cc698f08f76d6356f59fac_hd.png)

如上图所示 HTTPS 相比 HTTP 多了一层 SSL/TLS

SSL（Secure Socket Layer，安全套接字层）：1994年为 Netscape 所研发，SSL 协议位于 TCP/IP 协议与各种应用层协议之间，为数据通讯提供安全支持。

TLS（Transport Layer Security，传输层安全）：其前身是 SSL，它最初的几个版本（SSL 1.0、SSL 2.0、SSL 3.0）由网景公司开发，1999年从 3.1 开始被 IETF 标准化并改名，发展至今已经有 TLS 1.0、TLS 1.1、TLS 1.2 三个版本。SSL3.0和TLS1.0由于存在安全漏洞，已经很少被使用到。TLS 1.3 改动会比较大，目前还在草案阶段，**目前使用最广泛的是TLS 1.1、TLS 1.2**。

## 加密算法：

据记载，公元前400年，古希腊人就发明了置换密码；在第二次世界大战期间，德国军方启用了“恩尼格玛”密码机，所以密码学在社会发展中有着广泛的用途。

1、对称加密

有流式、分组两种，加密和解密都是使用的同一个密钥。

例如：DES、AES-GCM、ChaCha20-Poly1305等

2、非对称加密

加密使用的密钥和解密使用的密钥是不相同的，分别称为：公钥、私钥，公钥和算法都是公开的，私钥是保密的。非对称加密算法性能较低，但是安全性超强，由于其加密特性，非对称加密算法能加密的数据长度也是有限的。

例如：RSA、DSA、ECDSA、 DH、ECDHE

3、哈希算法

将任意长度的信息转换为较短的固定长度的值，通常其长度要比信息小得多，且算法不可逆。

例如：MD5、SHA-1、SHA-2、SHA-256 等

4、数字签名

签名就是在信息的后面再加上一段内容（信息经过hash后的值），可以证明信息没有被修改过。hash值一般都会加密后（也就是签名）再和信息一起发送，以保证这个hash值不被修改。

## 详解

**一、HTTP 访问过程**

![img](https://pic4.zhimg.com/80/v2-e367a5e3bc28fb7fd083ddc201e7e693_hd.png)

抓包如下：

![img](https://pic1.zhimg.com/80/v2-5fbdde93cf2db19730f9e90347614e64_hd.png)

如上图所示，HTTP请求过程中，客户端与服务器之间没有任何身份确认的过程，数据全部明文传输，“裸奔”在互联网上，所以很容易遭到黑客的攻击，如下：

![img](https://pic4.zhimg.com/80/v2-831635f04f3732e866af0ec6ce1040e7_hd.png)

可以看到，客户端发出的请求很容易被黑客截获，如果此时黑客冒充服务器，则其可返回任意信息给客户端，而不被客户端察觉，所以我们经常会听到一词“劫持”，现象如下：

下面两图中，浏览器中填入的是相同的URL，左边是正确响应，而右边则是被劫持后的响应

![img](https://pic4.zhimg.com/80/v2-299b4a71f9b005fa15fbcd4cabfd841b_hd.png)

所以 HTTP 传输面临的风险有：

（1） 窃听风险：黑客可以获知通信内容。

（2） 篡改风险：黑客可以修改通信内容。

（3） 冒充风险：黑客可以冒充他人身份参与通信。

**二、HTTP 向 HTTPS 演化的过程**

第一步：为了防止上述现象的发生，人们想到一个办法：对传输的信息加密（即使黑客截获，也无法破解）

![img](https://pic1.zhimg.com/80/v2-8d8138e883455e4d316d644c79a89314_hd.png)

如上图所示，此种方式属于对称加密，双方拥有相同的密钥，信息得到安全传输，但此种方式的缺点是：

（1）不同的客户端、服务器数量庞大，所以双方都需要维护大量的密钥，维护成本很高

（2）因每个客户端、服务器的安全级别不同，密钥极易泄露

第二步：既然使用对称加密时，密钥维护这么繁琐，那我们就用非对称加密试试

![img](https://pic2.zhimg.com/80/v2-660bec42419281a9ec47c029089a77c9_hd.png)

如上图所示，客户端用公钥对请求内容加密，服务器使用私钥对内容解密，反之亦然，但上述过程也存在缺点：

（1）公钥是公开的（也就是黑客也会有公钥），所以第 ④ 步私钥加密的信息，如果被黑客截获，其可以使用公钥进行解密，获取其中的内容

第三步：非对称加密既然也有缺陷，那我们就将对称加密，非对称加密两者结合起来，取其精华、去其糟粕，发挥两者的各自的优势

![img](https://pic3.zhimg.com/80/v2-22570e3e422de7951ce7c5c3e8435312_hd.png)

如上图所示

（1）第 ③ 步时，客户端说：（咱们后续回话采用对称加密吧，这是对称加密的算法和对称密钥）这段话用公钥进行加密，然后传给服务器

（2）服务器收到信息后，用私钥解密，提取出对称加密算法和对称密钥后，服务器说：（好的）对称密钥加密

（3）后续两者之间信息的传输就可以使用对称加密的方式了

遇到的问题：

（1）客户端如何获得公钥

（2）如何确认服务器是真实的而不是黑客

第四步：获取公钥与确认服务器身份

![img](https://pic3.zhimg.com/80/v2-f2ac6567fa1a3c10e73eba59eab3823a_hd.png)

1、获取公钥

（1）提供一个下载公钥的地址，回话前让客户端去下载。（缺点：下载地址有可能是假的；客户端每次在回话前都先去下载公钥也很麻烦）
（2）回话开始时，服务器把公钥发给客户端（缺点：黑客冒充服务器，发送给客户端假的公钥）

2、那有木有一种方式既可以安全的获取公钥，又能防止黑客冒充呢？ 那就需要用到终极武器了：SSL 证书（[申购](https://link.zhihu.com/?target=https%3A//console.upyun.com/toolbox/createCertificate/)）

![img](https://pic1.zhimg.com/80/v2-5e2241fae8b593ff7f3b3a308ef81c10_hd.png)

如上图所示，在第 ② 步时服务器发送了一个SSL证书给客户端，SSL 证书中包含的具体内容有：

（1）证书的发布机构CA

（2）证书的有效期

（3）公钥

（4）证书所有者

（5）签名

………

3、客户端在接受到服务端发来的SSL证书时，会对证书的真伪进行校验，以浏览器为例说明如下：

（1）首先浏览器读取证书中的证书所有者、有效期等信息进行一一校验

（2）浏览器开始查找操作系统中已内置的受信任的证书发布机构CA，与服务器发来的证书中的颁发者CA比对，用于校验证书是否为合法机构颁发

（3）如果找不到，浏览器就会报错，说明服务器发来的证书是不可信任的。

（4）如果找到，那么浏览器就会从操作系统中取出 颁发者CA 的公钥，然后对服务器发来的证书里面的签名进行解密

（5）浏览器使用相同的hash算法计算出服务器发来的证书的hash值，将这个计算的hash值与证书中签名（CA机构进行的签名）做对比

（6）对比结果一致，则证明服务器发来的证书合法，没有被冒充

（7）此时浏览器就可以读取证书中的公钥，用于后续加密了

4、所以通过发送SSL证书的形式，既解决了公钥获取问题，又解决了黑客冒充问题，一箭双雕，HTTPS加密过程也就此形成

所以相比HTTP，HTTPS 传输更加安全

（1） 所有信息都是加密传播，黑客无法窃听。

（2） 具有校验机制，一旦被篡改，通信双方会立刻发现。

（3） 配备身份证书，防止身份被冒充。

## 总结

我们来看一下使用HTTPS的工作原理



![HTTPS的工作原理](https://user-gold-cdn.xitu.io/2017/11/8/65c06c858d9270927f014b05b98af625?imageslim)HTTPS的工作原理



  　  （1）客户使用HTTPS的URL访问Web服务器，要求与Web服务器建立SSL连接。
　　（2）Web服务器收到客户端请求后，会将网站的证书信息（证书中包含公钥）传送一份给客户端。
　　（3）客户端的浏览器与Web服务器开始协商SSL/TLS连接的安全等级，也就是信息加密的等级。
　　（4）客户端的浏览器根据双方同意的安全等级，建立会话密钥，然后利用网站的公钥将会话密钥加密，并传送给网站。
　　（5）Web服务器利用自己的私钥解密出会话密钥。
　　（6）Web服务器利用会话密钥加密与客户端之间的通信。

#### 从上面可以总结出HTTPS的优点：

　　（1）客户端产生的密钥只有客户端和服务器端能得到；
　　（2）加密的数据只有客户端和服务器端才能得到明文；
　　（3）客户端到服务端的通信是安全的。
　　另外谷歌曾在2014年8月份调整搜索引擎算法，并称“比起同等HTTP网站，采用HTTPS加密的网站在搜索结果中的排名将会更高”。



[数字签名、数字证书和HTTPS - 简书](https://www.jianshu.com/p/94d3e512953d)

对于数字证书、数字签名的概念一直很模糊，虽然网上看了很多文章，但是自己脑子里想完整地梳理一遍的时候就还是乱乱的，所以动手整理了下，在写作的过程中各个概念之间的关系慢慢变得清晰了。

## 公钥、私钥和数字签名

首先是公钥和私钥的概念。公钥（Public Key）和私钥（Private Key）是一对使用特定加密算法产生的密钥对（两串字符串），可以使用 [OpenSSL](https://link.jianshu.com?t=https://zh.wikipedia.org/wiki/OpenSSL) 或者 OpenSSH 的 ssh-keygen 工具生成。公钥和私钥都可以用来加密数据，经过私钥加密的数据，只有通过公钥才可以解密出来，反之亦然。

私钥自己保留，公钥用于发放给别人。为什么要发给别人呢？因为这样就可以实现和对方之间的加密通讯，过程如下：

\- Susan 有 Bob 的公钥，在给 Bob 发消息前使用公钥对消息进行加密，这样就只有拥有私钥的 Bob 才可以解密消息。

\- Bob 使用他的私钥可以在数据上进行数字签名，即先将要发送的数据进行 Hash 运算，然后使用私钥加密 Hash 运行的结果，最后加密的结果就是叫数字签名（Digital Signature）。

\- Bob 把数字签名随同数据一起发给 Susan，Susan 通过 Bob 的公钥可以获得两个信息：

1 - 如果使用公钥成功解密数字签名，则说明这个信息确实是通过 Bob 的私钥加密的

2 - 对传输的数据进行 Hash 运算，如果结果与解密出来的 Hash 值一致，则说明数据没有被篡改过。

\> [Hash 算法](https://link.jianshu.com?t=https://zh.wikipedia.org/wiki/散列函數)会将数据变成一段固定长度的 message digest，且不同的原始数据经过运算后会产生截然不同的结果，可以认为不存在使用不同的原始数据来伪造相同的 hash 值的情况。

如果按照以上的流程，Bob 向 Susan 发送的数据是没有加密的（即使用私钥加密了数据，别人也能用公钥解开），那如何保证数据不被窃取呢？具体的例子可以看 HTTPS 一节的介绍。

## 中间人攻击与数字证书

上文中的加密通讯是在 Susan 正确获得 Bob 公钥的前提下进行的，如果 Susan 和 Bob 的通讯被 Doug 拦截，Doug 将 Bob 发给 Susan 的公钥替换成自己的公钥（至于替换的方式我们先不管），这样 Susan 就会认为 Doug 是 Bob，把原本要发给 Bob 的消息使用 Doug 的公钥加密并发送，Doug 收到后可以用自己的私钥解密，获得数据内容，然后再用 Bob 的公钥加密发送给 Bob，Bob 会认为这些信息是 Susan 发过来的。在这个过程中 Doug 可以任意篡改信息，Doug 就是在进行中间人攻击。

数字证书（Digital Certificate）就是用来确保 Susan 拿到的确实是 Bob 的公钥，而不是中间人 Doug 的。数字证书是由 Certificate Authority（CA）机构颁发的，所谓数字证书就是 CA 机构使用自己的私钥，对 Bob 的公钥和一些相关信息（如国家、公司、域名）一起加密的结果，CA 机构在颁发证书前会有一套严格的流程来确保相关信息的准确性。以网站来举例，Github 证书经过 CA 公钥解密后获得的一定就是 Github 的公钥。知名 CA 机构的根证书会内置于游览器中，用来确保 CA 机构本身的身份，其中就包含了 CA 机构自身的公钥。有了证书以后，网站发送信息只要附带上自己的证书，就能证明自己的身份。下图是使用 Chrome 查看 Github 的证书：



![img](https:////upload-images.jianshu.io/upload_images/6197244-dad056739f1fbed7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/972/format/webp)

GitHub 的证书

## HTTPS

那么 HTTPS 与数字证书之间是什么关系呢？网上找了一张介绍 HTTPS 的流程图，如果前面的概念都理解了的话，那一看这个流程图就能大概了解 HTTPS 是怎么回事了。



![img](https:////upload-images.jianshu.io/upload_images/6197244-0809f29c8b2c4bf5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/648/format/webp)

HTTPS 通讯过程

图中的第四步中 `validata crt` 我理解就是使用 CA 的公钥验证服务器端发送的证书的有效性，如颁发机构是否有效、证书中的域名和访问的域名是否一致、证书是否过期、是否被吊销等。如果证书有问题，游览器会给出警告。如果证书有效，客户端会产生一个随机字符，并使用从证书中获得的服务器端公钥加密后传输给服务器，这个信息只有服务器端的私钥才能解密，服务器端解密获得了随机字符后，便使用该随机字符对发送出去的内容进行[对称加密](https://link.jianshu.com?t=https://zh.wikipedia.org/wiki/對稱密鑰加密)，服务器端就可以使用相同的随机字符进行解密，这样就实现了加密通讯。

从上面的过程看，HTTPS 通讯是有一定性能损耗的，但毕竟更安全了嘛。




