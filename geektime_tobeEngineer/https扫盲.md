---


---

密码学指南

[TOC]



# 1. blockchain_guide

| 算法 类型    | 特点                    | 优势                    | 缺陷                              | 代表算法                        |
| ------------ | ----------------------- | ----------------------- | --------------------------------- | ------------------------------- |
| 对称 加密    | 加解密密钥相 同或可推算 | 计算效率高， 加密强度高 | 需提前共享密钥；易 泄露           | DES、3DES、 AES、IDEA           |
| 非对 称加 密 | 加解密密钥不 相关       | 无需提前共享 密钥       | 计算效率低，仍存在 中间人攻击可能 | RSA、ElGamal、椭 圆曲线系列算法 |

## 1.1 对称加密

DES、3DES、 AES、IDEA

 对称密码从实现原理上可以分为两种：**分组密码和序列密码**。前者将明文切分为定长数据块 作为加密单位，应用最为广泛。后者则只对一个字节进行加密，且密码不断变化，只用在一 些特定领域，如数字媒介的加密等。    

## 1.2 非对称加密

非对称加密:加密密钥和解密密钥是不同的，分别称为公钥和私钥。      

非对称加密算法的安全性往往需要基于数学问题来保障，目前主要有基于**大数质因子分解、 离散对数、椭圆曲线**等几种思路。    

**RSA**：经典的公钥算法。算法利用了对**大数进行质因子分解困难**的特性，但目前 还没有数学证明两者难度等价，或许存在未知算法在不进行大数分解的前提下解密。    

**Diffie-Hellman** 密钥交换：基于**离散对数无法快速求解**，可以在不安全的通道上，双方协 商一个公共密钥。    

**ElGamal**：由 Taher ElGamal 设计，利用了**模运算下求离散对数困难**的特性。被应用在 PGP 等安全工具中。    

**椭圆曲线算法（Elliptic curve cryptography，ECC）** ：现代备受关注的算法系列，基于**对椭圆曲线上特定点进行特殊乘法逆运算难以计算**的特性。ECC 系列算法一般被认为具备较高的安全性，但加解密 计算过程往往比较费时。    

**一般适用于签名场景或密钥协商，不适于大量数据的加解密。**    

**RSA 算法等已被认为不够安全，一般推荐采用椭圆曲线系列算法。**    



## 1.3 **混合加密机制（HTTPS 机制）**

典型场景就是https应用，TLS 1.0，对应到 SSL 3.1 版本    

- 客户端浏览器发送信息到服务器，包括随机数 R1，支持的加密算法类型、协议版本、压 缩算法等。注意该过程为明文。

- 服务端返回信息，包括随机数 R2、选定加密算法类型、协议版本，以及服务器证书。注 意该过程为明文。

- 浏览器检查带有该网站公钥的证书。该证书需要由第三方 CA 来签发，浏览器和操作系统 会预置权威 CA 的根证书。如果证书被篡改作假（中间人攻击） ，很容易通过 CA 的证书 验证出来。

- 如果证书没问题，则用证书中公钥加密随机数 R3，发送给服务器。此时，只有客户端和 服务器都拥有 R1、R2 和 R3 信息，基于 R1、R2 和 R3，生成对称的会话密钥（如 AES 算法） 。后续通信都通过对称加密进行保护。

## 1.4 数字签名

数字签名用于证实某数字内容的完整性（integrity） 和 来源（或不可抵赖，non-repudiation）。A 先对文件进行摘要，然后用自己的私钥进行加密，将文件和加密串都发给 B。B 收到文件和加密串后，用 A 的公钥来解密加密串，得到原始的数字摘要，跟对文件进行 摘要后的结果进行比对。    

HMAC

​	 **HMAC(K, H, Message)**：Hash-based Message Authentication Code，即“基于 Hash 的消息认证码”。K 为提前共享的对称密钥，H 为提前商定的 Hash 算法（一般为公认的经典算法） ， Message 为要处理的消息内容。

​	 **HMAC 主要问题是需要共享密钥** 。当密钥可能被多方拥有的场景下，无法证明消息确实来自 某人（Non-repudiation） 。反之，如果采用非对称加密方式，则可以证明。    



## 1.5 数字证书、PKI体系



## 1.6 Merkle树

​	默克尔树（又叫哈希树） 是一种二叉树，由一个根节点、一组中间节点和一组叶节点组成。 最下面的叶节点包含存储数据或其哈希值，每个中间节点是它的两个孩子节点内容的哈希 值，根节点也是由它的两个子节点内容的哈希值组成    

**默克尔树的典型应用场景包括**：

- 快速比较大量数据：当两个默克尔树根相同时，则意味着所代表的数据必然相同。 

- 快速定位修改：例如上例中，如果 D1 中数据被修改，会影响到 N1，N4 和 Root。因 此，沿着 Root --> N4 --> N1，可以快速定位到发生改变的 D1； 

- 零知识证明：例如如何证明某个数据（D0……D3） 中包括给定内容 D0，很简单，构造 一个默克尔树，公布 N0，N1，N4，Root，D0 拥有者可以很容易检测 D0 存在，但不知 道其它内容。    



## 1.7 **同态加密Homomorphic Encryption** 

​	如果我们有一个加密函数 f , 把明文A变成密文A’, 把明文B变成密文B’，也就是说 f(A) = A’ ， f(B) = B’ 。另外我们还有一个解密函数 f−1f−1 能够将 f 加密后的密文解密成加密前的明文。 

​	其他人可以对加密数据进行处理，但是处理过程不会泄露任何原始内容。同时，拥有密钥的用户对处理过的数据进行解密后，得到的正好是处理后的结果。 

​	对于一般的加密函数，如果我们将A’和B’相加，得到C’。我们用f−1f−1 对C’进行解密得到的结果一般是毫无意义的乱码。 但是，如果 f 是个可以进行同态加密的加密函数， 我们对C’使用 f−1f−1 进行解密得到结果C， 这时候的C = A + B。这样，**数据处理权与数据所有权可以分离，这样企业可以防止自身数据泄露的同时，利用云服务的算力。** 

a) 如果满足 f(A)+f(B)=f(A+B)f(A)+f(B)=f(A+B) ， 我们将这种加密函数叫做加法同态  

b) 如果满足 f(A)×f(B)=f(A×B)f(A)×f(B)=f(A×B) ，我们将这种加密函数叫做乘法同态。 

如果一个加密函数f只满足**加法同态**，就只能进行**加减法运算**；

如果一个加密函数f只满足**乘法同态**，就只能进行**乘除法运算**;

如果一个加密函数同时满足**加法同态和乘法同态**，称为**全同态加密**。**那么这个使用这个加密函数完成各种加密后的运算(加减乘除、多项式求值、指数、对数、三角函数)。**

同态加密算法种类：

1. **RSA** 算法对于乘法操作是同态的。ElGamal    
2. **Paillier** 算法则是对加法同态的。Benaloh    
3. **Gentry**算法则是全同态的。

**一个HE方案应该拥有的函数**： 

1. KeyGen函数：密钥生成函数。这个函数应该由Alice运行，用于产生加密数据Data所用的密钥Key。当然了，应该还有一些公开常数PP（Public Parameter）；
2. Encrypt函数：加密函数。这个函数也应该由Alice运行，用Key对用户数据Data进行加密，得到密文CT（Ciphertext）；
3. Evaluate函数：评估函数。这个函数由Cloud运行，在用户给定的数据处理方法f下，对密文进行操作，使得结果相当于用户用密钥Key对f(Data)进行加密。
4. Decrypt函数：解密函数。这个函数由Alice运行，用于得到Cloud处理的结果f(Data)。

**函数加密** 

​	全同态加密用于解决云安全有个问题，就是密文及密文计算的结果对于云平台完全是“不懂的”，这种“不懂”看似是完全安全的，但是实际用起来却做不了很多事情。例如，邮件服务器的垃圾邮件过滤，按道理用户把邮件加密后发出去，邮件服务器收到加密的邮件，可以通过全同态技术，运行垃圾邮件检测算法对加密邮件进行垃圾邮件检测，然而得到的检测结果是密文，邮件服务器“读不懂”检测结果，所以无法判断是否为垃圾邮件。还有很多其他例子，例如数据库的检索，由于要对密文进行比较，而比较的结果是密文，服务器无法判断是否找到了匹配的记录。 

​	 那么有没有一种方法，例如能让服务器只知道检测结果是否为垃圾邮件，而不知道邮件的内容呢？包括数据库的检索也一样。 有的，这就是所谓的函数加密。例如，任何人可以使用公钥PK对明文m进行加密得到密文Enc(m)，密钥的持有者对某个函数 F 颁发一个KEY, 任何拥有KEY和密文Enc(m)的一方，都可以计算F(m), 但是除了F(m)外不能获得关于m的任何信息。

Gentry的FHE方案如何呢？效率如下： 

![img](https://d33ypg4xwx0n86.cloudfront.net/direct?url=https%3A%2F%2Fpic1.zhimg.com%2F50%2Fd32bbca287efe40ff124087e55071bd0_hd.jpg&resize=w704)

**实例（垃圾邮件检测）**

​	这样上述垃圾邮件检测的例子就可以通过函数加密来解决。例如，Alice公开自己的公钥以及给邮件服务器一个KEY，这个KEY用于邮件服务器的垃圾邮件检测函数。当有人要发邮件给Allice的时候，先用Alice的公钥加密邮件，然后发出。邮件服务器收到邮件后，就可以通过垃圾邮件检测算法判断出邮件是否为垃圾邮件，然后做出相应的操作。

 	对多个通用函数能够提供任意多数量KEY的函数加密方案是不可能的。目前最好的结果就是：对任意一个通用函数提供一个KEY的函数加密方案。 

**同态加密在区块链应用** 

​	Amazon EC2(亚马逊弹性计算云)、Google Cloud以及Microsoft Azure是数字商业中云存储和云服务的主要提供者。 同态加密技术也可以在原有基础上使用区块链技术，两者结合达到一种完美的平衡，且不会对区块链属性造成任何重大的改变。在公有区块链上，所有的技术将会进行加密，不仅提供了隐私保护，也同样允许随时访问公用区块链上的加密数据。 

**同态加密的应用** 

​	“数学魔术”技术可以达到同态加密技术的处理效果， 政府情报高级研究项目活动(IARPA) 正在开发基于同态加密技术的数据查询系统。 可以用于医疗或财务的专用设备上 ，在案件调查过程中，警察可以搜索嫌烦的行程、财务记录，以及调查通讯和邮件记录，且不会暴露嫌烦的数据。医学研究人员可以根据数百万患者的记录，来识别基于人口结构和地理位置的疾病趋势。 

**云计算加密技术常见的有同态加密技术和代理重加密技术**  

​	代理重加密技术可以解决用户在数据共享方面的不便，在云端进行的数据密文转换可以有效的减轻用户端频繁释放和获取密码的负担，并强化了云端数据的可靠性和保密性。

​	代理重加密实质上是一种用于密文之间的密钥转换机制。在代理重加密过程中，服务商得不到数据的明文信息。代理重加密的具体方案是授权人甲透过代理者产生针对被授权人乙的转换密钥代理者利用该密钥就能将原本由授权人公钥加密的密文转化为用户乙的公钥所加密的密文。这在很大程度上解决了用户甲使用公钥pkI加密的密文转化为用户乙用公钥pk2加密的密文的问题，因此，被授权人只需要使用自己的私钥就可以获取密文对应的明文。而这甲乙的密文所对应 的明文是一致，实现了用户甲和用户乙之间的数据共享





# 2. blockchain_bitcoin

借鉴了来自数字货币、密码学、博弈论、分布式系统、控制论等多个领域的 技术成果。本章将介绍比特币项目的来源、核心原理设计、相关的工具，以及关键的技术话题。    

比特币网络在功能上具有如下特点： 

- 去中心化：意味着没有任何独立个体可对网络中交易进行破坏，任何交易请求都需要大多数参与者的共识； 

- 匿名性：比特币网络中账户地址是匿名的，无法从交易信息关联到具体个体，但这也意味着很难进行审计；

- 通胀预防：比特币的发行需要通过挖矿计算来进行，发行量每四年减半,总量上限为 2100 万枚，无法被超发。    



pgp  gunpg,rsa区别？metzdowd的加密邮件列表。











# 3. 编程随想安全指南



[0扫盲 HTTPS 和 SSL/TLS 协议：引子](https://program-think.blogspot.com/2014/11/https-ssl-tls-0.html)

为了方便阅读，把本系列帖子的目录整理如下（需翻墙）：

1. [背景知识、协议的需求、设计的难点](https://program-think.blogspot.com/2014/11/https-ssl-tls-1.html) 
2. [可靠密钥交换的难点，以及身份认证的必要性](https://program-think.blogspot.com/2014/11/https-ssl-tls-2.html) 
3. [扫盲几种密钥交换（密钥协商）算法](https://program-think.blogspot.com/2016/09/https-ssl-tls-3.html) 
4. SSL/TLS 协议的实现 5. 针对 HTTPS 的各种攻击手法 6. 各种相应的防范措施 



对称加密算法：AES，RC4，3DES 

非对称加密算法：RSA，DSA/DSS 

HASH算法：MD5，SHA1，SHA256 

## 2.1 背景知识、协议的需求、设计的难点

### 2.1.1 HTTP

​	SSL 是洋文“Secure Sockets Layer” ，TLS（是“Transport Layer Security”的缩写） ，这两者可以视作同一个东西的不同阶段 。HTTP 0.9 【没有】被广泛使用，而 HTTP 1.0 被广泛使用过 ，如今咱们用的 HTTP 协议，版本号是 1.1 。

​	在HTTP/1.1浏览器客户端在同一时间，针对同一域名的请求有一定数量的限制，超过限制数量的请求会被阻塞。这也是为何一些站点会有多个静态CDN域名的原因之一。HTTP 2.0 的出现,大幅度的提升了 web 性能 ,进一步减少了网络延迟 ，多路复用，允许同时通过单一的HTTP/2连接发起多重的请求-响应消息。

​	HTTP 对 TCP 连接的使用，分为两种方式：俗称“短连接”和“长连接”（“长连接”又称“持久连接”，洋文叫做“Keep-Alive”或“Persistent Connection”） 。在 HTTP 1.0 版本，【默认】使用的是“短连接”（那时候是 Web 诞生初期，网页相对简单，“短连接”的问题不大）； （建立 TCP 连接是有“时间成本”和“CPU 成本” ），在 HTTP 1.1 中，【默认】采用的是“Keep-Alive”的方式 。

**（1）HTTP通用消息格式**

> <start-line> 
> <message-headers> 
> <empty-line> 
> [<message-body>] 
> [<message-trailers>]

**（2）HTTP请求消息格式**

```
<request-line>  
<general-headers>  
<request-headers>  
<entity-headers> 
<empty-line> 
[<message-body>]  
[<message-trailers>] 
```

![HTTP请求消息格式](/HTTP请求消息格式.png)

**（3）HTTP响应消息格式**

```
<status-line>  
<general-headers>  
<response-headers>  
<entity-headers>  
<empty-line> 
[<message-body>] 
[<message-trailers>] 
```

![HTTP响应消息格式](/HTTP响应消息格式.png)

### 2.1.2 对称加密和非对称加密



### 2.1.3 CA证书用途和原理

《[数字证书及CA的扫盲介绍](https://program-think.blogspot.com/2010/02/introduce-digital-certificate-and-ca.html)》 

《[学习技术的三部曲：WHAT、HOW、WHY](https://program-think.blogspot.com/2009/02/study-technology-in-three-steps.html)》，其中谈到“WHY 型问题”的重要性。一上来就给你讲协议细节，你充其量只能知道 WHAT 和 HOW，无法理解 WHY。俺在前一个章节讲了“背景知识”，在这个章节讲了“需求”，这就有助于你理解：当初**为什么**要设计成这样？——这就是 WHY 型的问题。 



### 2.1.4 HTTPS需求和难点

**（1）需求**

​	兼容性，可拓展性（ssl可跟ftp、SMTP、POP、Telnet 搭配），保密性（防嗅探、防重放攻击）、完整性防篡改（《[扫盲文件完整性校验——关于散列值和数字签名](https://program-think.blogspot.com/2013/02/file-integrity-check.html)》 ）、真实性防假冒（DNS 的不可靠（存在“域名欺骗”和“域名劫持”） 《[扫盲 DNS 原理，兼谈“域名劫持”和“域名欺骗/域名污染”](https://program-think.blogspot.com/2014/01/dns.html)》 ）、性能

**（2）难点**

​	最大的难点在于“密钥交换” 



## 2.2 可靠密钥交换的难点，以及身份认证的必要性







数字签名

发送加密

> 1.数字签名用户发送电子文件时，发送方通过哈希函数对电子数据文件进行加密生成数据摘要（digest）；
> 2.数字签名发送方用自己的私钥对数据摘要进行加密，私钥加密后的摘要即为数字签名；
> 3.数字签名和报文将一起发送给接收方。

接收解密

> 1.接收方首先用与发送方一样的哈希函数从接收到的原始报文中计算出报文摘要；
> 2.接收方用发送方的提供的公钥来对报文附加的数字签名进行解密，得到一个数字摘要；
> 3.如果以上两个摘要相一致，则可以确认文件内容没有被篡改。
> 4.发送方的公钥能够对数字签名进行解密，证明数字签名由发送方发送。



SSL 证书具有服务器身份验证和数据传输加密功能。这个证书总共有 3 个测评等级，其中，EV SSL 证书最高，OV SSL 证书其次，而 DV SSL 证书最弱。

具体而言，DV SSL 证书只验证域名所有权，仅为加密传输信息的作用，并不能证明网站的真实身份;OV SSL 证书是需要验证网站所有单位的真实身份的标准型 SSL 证书，而 EV SSL 证书则是遵循全球统一严格身份验证标准办法的 SSL 证书，是目前业界最高安全级别的 SSL 证书。其中，OV SSL 主要在国内网站应用，而 EV SSL 则在国外网站应用更多。靠谱的商用网站一般都会部署 EV SSL 证书或 OV SSL 证书，绝对不会部署已经被欺诈网站滥用的 DV SSL 证书。





# 4.blockchain_以太坊



# 5.blockchain_Hyperledger

[gerrit路径](https://gerrit.hyperledger.org),[github路径](https://github.com/hyperledger/)

目前主要包括三大账本平台项目和若干其它项目。 

账本平台项目： 

Fabric：包括 Fabric、Fabric CA、Fabric SDK（包括 Node.Js、Python 和 Java 等语 言） 和 fabric-api、fabric-sdk-node、fabric-sdk-py 等，目标是区块链的基础核心平台， 支持 pbft 等新的 consensus 机制，支持权限管理，最早由 IBM 和 DAH 发起； 

SawToothLake：包括 arcade、core、dev-tools、validator、mktplace 等。是 Intel 主要 发起和贡献的区块链平台，支持全新的基于硬件芯片的共识机制 Proof of Elapsed Time（PoET） 。 

Iroha：账本平台项目，基于 C++ 实现，带有不少面向 Web 和 Mobile 的特性，主要由 Soramitsu 发起和贡献。    

其它项目： 

Blockchain Explorer：提供 Web 操作界面，通过界面快速查看查询绑定区块链的状态 （区块个数、交易历史） 信息等。 

Cello：提供"Blockchain as a Service" 功能，使用 Cello，管理员可以轻松获取和管理多 条区块链；应用开发者可以无需关心如何搭建和维护区块链。 目前，所有项目均处于孵化（Incubation） 状态。    

项目原则 

项目约定共同遵守的 基本原则 为： 重视模块化设计，包括交易、合同、一致性、身份、存储等技术场景； 

代码可读性，保障新功能和模块都可以很容易添加和扩展； 

演化路线，随着需求的深入和更多的应用场景，不断增加和演化新的项目。 

如果你对 Hyperledger 的源码实现感兴趣，可以参考 [Hyperledger 源码分析之 Fabric](https://github.com/yeasy/hyperledger_code_fabric)。    

https://wiki.hyperledger.org/groups/twgc

# 6.EOS

Dan Larimer（Bitshares，Graphene和Steem / Steemit的发明人）与eos.io 团队一起宣布开发 EOS，这是一个共识区块链操作系统，提供数据库，帐户权限，日程安排，身份验证和互联网应用通信。EOS 将为开发人员提供他们需要的工具，以便他们可以专注于其应用程序的特定业务逻辑，而不用担心密码实现或与去中心化计算机的通信（即区块链）。此外，EOS 将使用并行化来增强区块链可伸缩性，以便达到每秒数百万次交易。 

https://sdk.cn/news/8101(EOS和以太坊区别，比特币，三代的区别)

鉴于以太坊使用 PoW（即将转而采用混合 PoW/PoS），EOS 将使用石墨烯技术，该技术利用 DPOS 共识机制。 EOS 的 DPOS 共识机制在硬分叉期间不会产生多个竞争链。 Steem 网络所经历的 18 次成功的硬分叉证明了这一点，背后采用的就是 Graphene 石墨烯算法。 

[区块链3.0：拥抱EOS](https://www.cnblogs.com/Evsward/p/eos-intro.html)









