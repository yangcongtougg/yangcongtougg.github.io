---
title: 最新python面试题之数网络基础
date: 2018-01-18 15:12:17
tags: python面试题
categories: 面试题
---

## 网络
> 网络的基本知识

---
#### 三次握手
- 客户端通过向服务器发送一个SYN来创建一个主动打开，作为三次握手的一部分。客户端把这段连接的序号设定为随机数A。
- 服务器端应当为一个合法的SYN回送一个SYN/ACK。ACK的确认码应该为A+1，SYN/ACK包本身又有一个随机序号B。
- 最后，客户端再发送一个ACK。当服务端接受到这个ACK的时候，就完成了三路握手，并进入了链接创建状态。此时包序号被设定为收到的确认号A+1，而响应则为B+1.
---
#### 四次挥手
> 注意：中断连接可以是客户端，也可以是服务端。下面仅以客户端断开连接举例，反之亦然。

- 客户端发送一个数据分段，其中的FIN标记设置为1。客户端进入PIN-WAIT状态。该状态下的客户端只接收数据，而不再发送数据。
- 服务端当收到PIN=1的数据分段的时候，发送带有ACK=1的剩余的数据分段，来确认收到客户端发送过来的FIN信息。
- 服务器等到所有的数据传输服务结束之时，向客户端发送一个带有ACK=1的数据分段，并进入CLOSE-WAIT状态，等待客户端发来带有ACK=1的确认报文。
- 客户端收到服务器发来带有FIN=1的报文，返回ACK=1的报文确认，为了防止服务端未收到需要重发，进入TTIME-WAIT状态。服务器收到报文之后关闭连接。客户端等待2MSL后未收到答复；则认为服务器成功关闭，客户端关闭连接。

图解：http://blog.csdn.net/whuslei/article/details/6667471

---
#### ARP协议
地址解析协议（Address Resolution Protocol），其基本的功能便是通过目标设备的ip地址，查询目标的MAC地址,以保证通信的顺利进行。她是IPv4网络层必不可少的协议，不过在IPv6中已经不再适用，并被邻居发现协议（NDP）所代替。

---
#### urllib和urllib2的区别
- urllib提供urlencode方法用来GET查询字符串的产生，而urllib2没有。这就是两者通常一块使用的原因。
- urllib2可以接受一个Request类的实例来设置请求的headers，urllib仅可以接受URL。这意味着，你可以不用伪装你的User-Agent等。
---
#### POS和GET
[GET和POST有什么区别？及为什么网上的多数答案都是错的](http://www.cnblogs.com/nankezhishi/archive/2012/06/09/getandpost.html)
[知乎回答](https://www.zhihu.com/question/31640769?rf=37401322)
get:[RFC 2616 - Hypertext Transfer Protocol -- HTTP/1.1](https://tools.ietf.org/html/rfc2616#section-9.3)
post:[RFC 2616 - Hypertext Transfer Protocol -- HTTP/1.1](https://tools.ietf.org/html/rfc2616#section-9.5)

---
#### Cookie和Session
|   |  Cookie |Session   |
| :------------: | :------------: | :------------: |
|存储位置   |客户端   |服务端   |
|目的   |跟踪会话，也可以保存用户的偏好设置或者保存用户的用户名密码等   | 跟踪会话  |
| 安全性  | 不安全  | 安全  |
session的使用是需要cookie的支持的，之所以使用到session，主要是为了安全。

---
#### apache和nginx的区别
nginx相对apache的优缺点：
- 轻量级，同样起web服务，比apache占用更少的内存及连接资源。
- 抗并发，nginx处理请求是异步非阻塞的，支持更多的并发连接，而apache则是阻塞型的，在高并发下nginx能保持低资源低消耗及高性能。
- 配置简洁。
- 高度模块化的设计，编写模块相对简单。
- 社区活跃。

apache相对于nginx的优点：
- rewrite比nginx的rewrite强大。
- 模块超多，基本想到的都可以找到。
- 少bug，nginx的bug较多。
- 超稳定。

---
#### 网站用户密码的保存
- 明文保存。
- 明文hash后保存，如md5。
- md5+salt方式，这个salt可以随机。
- 知乎好像使用了Bcrypy加密。

---
#### HTTP和HTTPS
| 状态码  |定义   |
| :------------: | :------------: |
|1xx报告   |接收到请求，继续进程   |
|2xx报告|步骤成功接收，被理解，并被接受   |
|3xx重定向   |为了完成请求必须采取进一步的措施   |
|4xx客户端出错   |请求顺序出错或者不能完成   |
|5xx服务端出错   |服务器无法完显然有效的请求   |
|403|Forbidden|
|404|Not Found|
HTTPS握手，对称加密，非对称加密，TLS/SSH/RSA。

---
#### XSRF和XSS
> 跟跨网站脚本（XSS）相比，XSS利用的是用户对指定网站的信任，CSRF利用的是网站对用户网页浏览器的信任。

- CSRF(Cross-site request forgery)跨站请求伪造。
 - 举个例子：因为CSRF攻击利用的是冲着浏览器分不清发起请求是不是真正的用户本人。，也就是说，简单的身份验证只能保证请求发自某个用户的浏览器，却不能保证请求本身是用户自愿发出的。
 - 用户Alice登录和访问某银行网站A，保留cookie。
 - Alice被某些信息诱导访问危险网站B。
 - 危险网站B上有一个img标签。
 - 这个标签的src不指向一张图片，而是一个http请求，这个请求向银行要求将Alice的1000元转给Badman，由于Alice的浏览器上有cookie，这样浏览器发出的这个请求就能得到响应执行。
 - 这样Alice的钱就被偷了。
- XSRF防护（用户在浏览器中留下信息，浏览器对于接下来的操作是无法确定是不是用户本身的意愿）。所以防范的关键在于在请求中放入黑客所不能伪造的信息。从而防止黑客伪造一个完整的请求欺骗服务器。

- XSS(Cross Site Scripting)跨站脚本攻击。
 - XSS利用的是用户对目标网站的信任，而嵌入了恶意的脚本。

CSRF重点在请求,XSS重点在脚本

---
#### 幂等 Idempotence
> 这里针对HTTP的幂等性。
HTTP方法的幂等性是指一次和多次请求某一个资源应该具有同样的副作用。(注意是副作用)

- GET： 不会改变资源的状态，不论调用一次还是N次都没有副作用。请注意，这里强调的是一次和N次具有相同的副作用，而不是每次GET的结果相同。GET这个HTTP请求可能会每次得到不同的结果，但它本身并没有产生任何副作用，因而是满足幂等性的。
- DELETE： DELETE方法用于删除资源，有副作用，但它应该满足幂等性。比如：DELETE `http://www.forum.com/article/4231`调用一次和N次对系统产生的副作用是相同的，即删掉id为4231的帖子；因此，调用者可以多次调用或刷新页面而不必担心引起错误。
- POST： POST所对应的URI并非创建的资源本身，而是资源的接收者。比如：POST `http://www.forum.com/articles`的语义是在`http://www.forum.com/articles`下创建一篇帖子，HTTP响应中应包含帖子的创建状态以及帖子的URI。两次相同的POST请求会在服务器端创建两份资源，它们具有不同的URI；所以，POST方法不具备幂等性。
- PUT：PUT所对应的URI是要创建或更新的资源本身。比如：PUT `http://www.forum/articles/4231`的语义是创建或更新ID为4231的帖子。对同一URI进行多次PUT的副作用和一次PUT是相同的；因此，PUT方法具有幂等性。

---
#### RESTful架构(SOAP,RPC)
推荐阅读：http://www.ruanyifeng.com/blog/2011/09/restful.html

---
#### SOAP
> 了解即可

SOAP（原为Simple Object Access Protocol的首字母缩写，即简单对象访问协议）是交换数据的一种协议规范，使用在计算机网络Web服务（web service）中，交换带结构信息。SOAP为了简化网页服务器（Web Server）从XML数据库中提取数据时，节省去格式化页面时间，以及不同应用程序之间按照HTTP通信协议，遵从XML格式执行资料互换，使其抽象于语言实现、平台和硬件。

---
#### RPC
> 了解即可

RPC（Remote Procedure Call Protocol）——远程过程调用协议，它是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。RPC协议假定某些传输协议的存在，如TCP或UDP，为通信程序之间携带信息数据。在OSI网络通信模型中，RPC跨越了传输层和应用层。RPC使得开发包括网络分布式多程序在内的应用程序更加容易。

总结:服务提供的两大流派.传统意义以方法调用为导向通称RPC。为了企业SOA,若干厂商联合推出webservice,制定了wsdl接口定义,传输soap.当互联网时代,臃肿SOA被简化为http+xml/json.但是简化出现各种混乱。以资源为导向,任何操作无非是对资源的增删改查，于是统一的REST出现了。

进化的顺序: RPC -> SOAP -> RESTful

---
#### CGI和WSGI
> 了解即可

CGI是通用网关接口，是连接web服务器和应用程序的接口，用户通过CGI来获取动态数据或文件等。 CGI程序是一个独立的程序，它可以用几乎所有语言来写，包括perl，c，lua，python等等。

WSGI, Web Server Gateway Interface，是Python应用程序或框架和Web服务器之间的一种接口，WSGI的其中一个目的就是让用户可以用统一的语言(Python)编写前后端。

官方说明：[PEP-3333](https://www.python.org/dev/peps/pep-3333/)

---
#### 中间人攻击
> 中间人攻击（Man-in-the-middle attack，通常缩写为MITM）是指攻击者与通讯的两端分别创建独立的联系，并交换其所收到的数据，使通讯的两端认为他们正在通过一个私密的连接与对方直接对话，但事实上整个会话都被攻击者完全控制。

---
#### c10k问题
所谓c10k问题，指的是服务器同时支持成千上万个客户端的问题，也就是concurrent 10 000 connection（这也是c10k这个名字的由来）。
推荐阅读：https://my.oschina.net/xianggao/blog/664275

---
#### socket
> Socket=Ip address+ TCP/UDP + port

推荐阅读：http://www.360doc.com/content/11/0609/15/5482098_122692444.shtml

---
#### 浏览器缓存
推荐阅读： http://www.cnblogs.com/skynet/archive/2012/11/28/2792503.html
304 Not Modified

---
#### Ajax
> AJAX,Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）, 是与在不重新加载整个页面的情况下，与服务器交换数据并更新部分网页的技术。


