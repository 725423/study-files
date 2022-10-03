# HTTP

##### HTTP

​	运行在TCP之上，明文传输，客户端与服务器都无法验证对方的身份

##### HTTPS

​	身披SSL外壳的HTTP，运行于SSL上，SSL运行于TCP上，是添加了加密和认证机制的HTTP

##### HTTP与HTTPS的区别

- 端口不同，前者为80端口，后者为443端口
- 资源消耗：前者资源消耗较少，后者由于加解密处理会消耗更多的CPU和内存资源
- 前者无需证书，后者需要想认证机构购买证书
- 加密：前者无加密机制，后者有共享密钥加密和公开密钥加密并用的混合加密机制
- 安全性：前者安全性较弱，后者由于有加密机制，安全性较强

##### 	常用的HTTP状态码

| 状态码 |              类别              |            含义            |
| :----: | :----------------------------: | :------------------------: |
|  1XX   |   Information(信息性状态码)    |     接受的请求正在处理     |
|  2XX   |      Success(成功状态码)       |      请求正常处理完毕      |
|  3XX   |   Redirection(重定向状态码)    | 需要进行附加操作以完成请求 |
|  4XX   | Client Error(客户端错误状态码) |     服务器无法处理请求     |
|  5XX   | Sever Error(服务器错误状态码)  |     服务器处理请求出错     |

- ​	100 Continue : 表示到目前为止都和正常，客户端可以继续发送请求或者忽略这个响应
- ​	200 OK
- ​	204 No Content  ：请求已经成功处理，但是返回的响应报文不包含实体的主体部分。一般在只需要从客户端往服务器发送信息，而不需要返回数据时使用
- ​	206 Partial Content  ：表示客户端进行了范围请求，响应报文包含由 Content-Range 指定范围的实体内容
- ​	301 Moved Permanently ：永久性重定向

- ​	302 Found ：临时性重定向

- ​	303 See Other ：和 302 有着相同的功能，但是 303 明确要求客户端应该采用 GET 方法获取资源
- ​	注：虽然 HTTP 协议规定 301、302 状态下重定向时不允许把 POST 方法改成 GET 方法，但是大多数浏览器都会在 301、302 和 303 状态下的重定向把 POST 方法改成 GET 方法

- ​	304 Not Modified ：如果请求报文首部包含一些条件，例如：If-Match，If-Modified-Since，If-None-Match，If-Range，If-Unmodified-Since，如果不满足条件，则服务器会返回 304 状态码
- ​	307 Temporary Redirect ：临时重定向，与 302 的含义类似，但是 307 要求浏览器不会把重定向请求的 POST 方法改成 GET 方法
- ​	400 Bad Request ：请求报文中存在语法错误
- ​	401 Unauthorized ：该状态码表示发送的请求需要有认证信息（BASIC 认证、DIGEST 认证）。如果之前已进行过一次请求，则表示用户认证失败
- ​	403 Forbidden ：请求被拒绝
- ​	404 Not Found
- ​	500 Internal Server Error ：服务器正在执行请求时发生错误
- ​	503 Service Unavailable ：服务器暂时处于超负载或正在进行停机维护，现在无法处理请求

##### HTTP首部

​	HTTP有四种类型的首部字段：通用首部字段、请求首部字段、响应首部字段和实体首部字段

##### HTTP 有以下安全性问题

- 使用明文进行通信，内容可能会被窃听；
- 不验证通信方的身份，通信方的身份有可能遭遇伪装；
- 无法证明报文的完整性，报文有可能遭篡改。

HTTPS 并不是新协议，而是让 HTTP 先和 SSL（Secure Sockets Layer）通信，再由 SSL 和 TCP 通信，也就是说 HTTPS 使用了隧道进行通信。

通过使用 SSL，HTTPS 具有了加密（防窃听）、认证（防伪装）和完整性保护（防篡改）

##### 缓存

	缓存一般是为了应对高并发场景、缓解数据库读写压力，而将数据存储在读写更快的某种存储介质中（如内存），以加快读取数据的速度。先查询缓存中有没有要的数据，如果有，就直接返回缓存中的数据。

​	优点

​		1.提升了用户体验：用户在使用产品过程中建立起来的一种纯主观感受。
​	缓存的使用可以提升系统的响应能力，大大提升了用户体验。

​		2.减轻了服务器压力。客户端缓存、网络端缓存减轻应用服务器压力。
​	服务端缓存减轻数据库服务器的压力。

​	缺点：

​		1.额外的硬件支出。缓存是一种软件系统中以空间换时间的技术
​	需要额外的磁盘空间和内存空间来存储数据。

​		2.缓存和主数据之间数据一致性问题。缓存与数据库无法做到数据的实时同步，
​	Redis无法做到主从时时数据同步。

​		3.高并发缓存失效。在高并发场景下会出现缓存失效（缓存穿透、缓存雪崩、缓存击穿）
​	造成瞬间数据库访问量增大，甚至崩溃。

### 通信数据转发

#### [#](http://www.cyc2018.xyz/计算机基础/HTTP/HTTP.html#_1-代理) 1. 代理

代理服务器接受客户端的请求，并且转发给其它服务器。

使用代理的主要目的是：

- 缓存
- 负载均衡
- 网络访问控制
- 访问日志记录

代理服务器分为正向代理和反向代理两种：

- 用户察觉得到正向代理的存在。

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/a314bb79-5b18-4e63-a976-3448bffa6f1b.png)



- 而反向代理一般位于内部网络中，用户察觉不到。

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/2d09a847-b854-439c-9198-b29c65810944.png)



#### [#](http://www.cyc2018.xyz/计算机基础/HTTP/HTTP.html#_2-网关) 2. 网关

与代理服务器不同的是，网关服务器会将 HTTP 转化为其它协议进行通信，从而请求其它非 HTTP 服务器的服务。

#### [#](http://www.cyc2018.xyz/计算机基础/HTTP/HTTP.html#_3-隧道) 3. 隧道

使用 SSL 等加密手段，在客户端和服务器之间建立一条安全的通信线路
