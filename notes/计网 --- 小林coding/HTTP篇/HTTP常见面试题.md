## HTTP基本概念

**HTTP是什么?**

HTTP是超文本传输协议.

```txt
能否详细解释[超文本传输协议]?
```

HTTP的名字[超文本传输协议],可以拆分成三个部分:

超文本 , 传输 , 协议.

![三个部分](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/3-HTTP%E4%B8%89%E9%83%A8%E5%88%86.png) 

HTTP是一个用在计算机世界里的协议.它使用计算机能够理解的语言确立了一种计算机之间交流通信的规范(两个以上的参与者),以及相关的各种控制和错误处理方式(行为约定和规范).

传输:

HTTP协议是一个双向协议.HTTP是一个在计算机世界里专门用来在**两点之间传输数据**的约定和规范.

3.超文本

HTTP传输的内容是[超文本].

超文本: 超越了普通文本的文本,它是文字,图片,视频等的混合题,最关键有超链接,能从一个超文本跳转到另外一个超文本.

**HTTP是一个在计算机世界里专门在[两点]之间[传输]文字,图片,音频,视频等[超文本]数据的[约定和规范].**

```txt
那「HTTP 是用于从互联网服务器传输超文本到本地浏览器的协议 ，这种说法正确吗？
```

这种说法是**不正确**的.因为也可以[服务器 <-> 服务器],所以采用**两点之间**的描述会更准确.

### HTTP常见的状态码有那些?

![ 五大类 HTTP 状态码 ](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/6-%E4%BA%94%E5%A4%A7%E7%B1%BBHTTP%E7%8A%B6%E6%80%81%E7%A0%81.png) 

### HTTP常见字段有那些?

HOST字段

客户端发送请求时,用来指定服务器的域名.

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/7-HOST%E5%AD%97%E6%AE%B5.png) 

有了Host字段,就可以将请求发往[同一台]服务器上的不同网站.

**Content-Length字段**

服务器在返回数据时,会有 Content-Length 字段,表明本次回应的数据长度.

**Connection字段**

Connection字段最常用于客户端要求服务器使用TCP持久连接,以便其他请求复用.

HTTP/1.1版本的默认连接都是持久连接,但为了兼容老版本的HTTP,需要指定Connection 首部字段的值为 Keep-Alive.

```txt
Connection: keep-alive
```

一个可以复用的TCP连接就建立了,知道客户端或服务器主动关闭连接.

Content-Type字段

Content-Type字段用于服务器回应时,告诉客户端,本次数据是什么格式.

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/10-content-type%E5%AD%97%E6%AE%B5.png) 

```txt
Content-Type: text/html; charset=utf-8
```

上面的类型表明,发送的是网页,而且编码是UTF-8.

客户端请求的时候,可以使用Accept字段表明自己可以接受那些数据格式.

```txt
Accept: */*
```

上面代码中,客户端声明自己可以接受任何格式的数据.

Content-Encoding 字段

Content-Encoding 字段说明数据的压缩方法.表示服务器返回的数据使用了什么压缩格式.

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/11-content-encoding%E5%AD%97%E6%AE%B5.png) 

```txt
Content-Encoding: gzip
```

上面表示服务器返回的数据采用了 gzip 方式压缩，告知客户端需要用此方式解压。

客户端在请求时，用 `Accept-Encoding` 字段说明自己可以接受哪些压缩方法。

```txt
Accept-Encoding: gzip, deflate
```

## GET与POST

**GET和POST有什么区别?**

GET是从服务器获取指定的资源,GET请求的参数位置一般写在URL中,URL规定只能至此ASCLL,所以GET请求的参数只允许ASCLL字符,而且浏览器会对URL的长度有限制.(HTTP协议本身对URL长度并没有做任何规定).

POST是更具请求负荷(报文body)对指定的资源做出处理,具体的处理方式视资源类型而不同.POST请求携带数据的位置一般是写在报文body中,body中的数据可以是任意格式的数据,只要客户端与服务端协商好即可,而且浏览器不会对body大小做限制.

```txt
GET和POST方法都是安全和幂等吗?
```

安全和幂等的概念:

在HTTP协议里,所谓的[安全]是指请求方法不会[破坏]服务器上的资源.

所谓的[幂等],意思是多次执行相同的操作,结果都是[相同]的.

**GET方法就是安全且幂等的**,可以对GET请求的数据做缓存,这个缓存可以做到浏览器本身上(彻底避免浏览器发送请求),也可以做到代理上(如nginx),而且在浏览器中GET请求可以保存为书签.

POST因为是[新增或提交数据]的操作,会修改服务器上的资源,所以是不安全的,且多次提交数据救会创建多个资源,所以不是幂等的.所以,浏览器一般不会缓存POST请求,也不能把POST请求保存为书签.

小结:

GET的语义是请求获取指定的资源.GET方法是安全,幂等,可悲缓存的.

POST的语义是根据请求负荷(报文主体)对指定的资源做出处理,具体的处理方式视资源类型而不同.POST不安全,不幂等,(大部分实现)不可缓存.

上面是从RFC规范的语义来分析的.

但是实际过程中,开发者不一定会按照RFC规范定义的语义来实现GET和POST方法.

比如:

- 可以用 GET 方法实现新增或删除数据的请求，这样实现的 GET 方法自然就不是安全和幂等。
- 可以用 POST 方法实现查询数据的请求，这样实现的 POST 方法自然就是安全和幂等。

因为HTTP传输的内容都是明文的,虽然在浏览器地址栏看不到POST提交的body数据,但是只要抓个包就都能看到了.

所以,要避免传输过程中数据被窃取,就要使用HTTPS协议,这样所有HTTP的数据都会被加密传输.

```txt
GET请求可以带body吗?
```

RFC 规范并没有规定 GET 请求不能带 body 的。理论上，任何请求都可以带 body 的。只是因为 RFC 规范定义的 GET 请求是获取资源，所以根据这个语义不需要用到 body。

另外，URL 中的查询参数也不是 GET 所独有的，POST 请求的 URL 中也可以有参数的。

## HTTP缓存技术

HTTP缓存有两种实现方式,分别是强制缓存和协商缓存.

### 什么是强制缓存?

强缓存指的是只要浏览器判断缓存没有过期,则直接使用浏览器的本地缓存,决定是否使用缓存的主动性在于浏览器这边.

强制缓存是利用下面这两个HTTP响应头部(Response Header)字段实现的,它们都用来表示资源在客户端缓存的有效期:

Cache-Control , 是一个相对时间;

Expries , 是一个绝对时间;

如果HTTP响应头部同时有Cache-Control 和 Expires字段的化,**Cache-Control 的优先级高于Expires**.

Cache-control选项更多一些,设置更加精细,所以建议使用Cache-Control来实现强缓存.具体的实现流程如下:

当浏览器第一次请求访问服务器资源时,服务器会在返回这个资源的同时,在Response头部加上Cache-Control , Cache-Control 中设置了过期时间大小;

浏览器再次请求访问服务器中的该资源时,会先**通过请求资源的时间于Cache-Control中设置的过期时间大小,来计算出该资源是否过期.**如果没有,则使用该缓存,否则重新请求服务器;

服务器再次受到请求后,会再次更新Response头部的Cache-Control.

### 什么是协商缓存?

当我们在浏览器使用开发者工具的时候,可能会看到过某些请求的响应码是304,这个是告诉浏览器可以使用本地缓存的资源,通常这种通过服务端告知客户端是否可以使用缓存的方式被称为协商缓存.

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http1.1%E4%BC%98%E5%8C%96/%E7%BC%93%E5%AD%98etag.png) 

**协商缓存就是与服务端协商之后,通过协商结果来判断是否使用本地缓存.**

协商缓存可以基于两种头部来实现:

第一种: 请求头部中的If-Modified-Since字段与响应头部中的Last-Modified字段实现,这两个字段的意思是:

响应头部中的Last-Modified : 表示这个响应资源的最后修改时间;

请求头部中的 If-Modified-Since : 当资源过期了,发现响应头中具有Last-Modified声明,则再次请求时带上Last-Modified的时间,服务器收到请求后发现有If-Modified-Since则与被请求资源的最后修改时间进行对比(Last-Modified),如果最后修改时间较新(大),说明资源又被改动过,则饭hi最新资源,HTTP 200 Ok ; 如果最后修改时间较旧(小),说明资源无新修改,响应HTTP 304走缓存.

第二种: 请求头部中的If - None - Match 字段与响应头部中的Etag字段,这两个字段的意思是:

响应头部中国Etag : 唯一标识响应资源;

请求头部中的 If - None - Match : 当资源过期时,浏览器发现响应头里有Etag,则再次向服务器发送请求时,会将请求头If-None-Match 值设置为Etag的值.服务器收到请求后进行比对,如果资源没有变化返回304,如果资源变化了返回200.

第一种实现方式是基于时间实现的，第二种实现方式是基于一个唯一标识实现的，相对来说后者可以更加准确地判断文件内容是否被修改，避免由于时间篡改导致的不可靠问题。 

如果在第一次请求资源的时候，服务端返回的 HTTP 响应头部同时有 Etag 和 Last-Modified 字段，那么客户端再下一次请求的时候，如果带上了 ETag 和 Last-Modified 字段信息给服务端，**这时 Etag 的优先级更高**，也就是服务端先会判断 Etag 是否变化了，如果 Etag 有变化就不用在判断 Last-Modified 了，如果 Etag 没有变化，然后再看 Last-Modified。 

**为什么 ETag 的优先级更高？**这是因为 ETag 主要能解决 Last-Modified 几个比较难以解决的问题：

1. 在没有修改文件内容情况下文件的最后修改时间可能也会改变，这会导致客户端认为这文件被改动了，从而重新请求；
2. 可能有些文件是在秒级以内修改的，`If-Modified-Since` 能检查到的粒度是秒级的，使用 Etag就能够保证这种需求下客户端在 1 秒内能刷新多次；
3. 有些服务器不能精确获取文件的最后修改时间。

**协商缓存这两个字段都需要配合强制缓存中的Cache-Control字段来使用,只有在未能命中强制缓存的时候,才能发起带有协商缓存字段的请求.**

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/network/http/http%E7%BC%93%E5%AD%98.png) 

当使用 ETag 字段实现的协商缓存的过程： 

当浏览器第一次请求访问服务器资源时,服务器会在返回这个资源的同时,在Response头部加上Etag唯一标识,这个唯一标识的值是根据当前请求的资源生成的;

当浏览器再次请求访问服务器中的该资源时,首先会先检查强制缓存受否过期:

如果没有过期,则直接使用本地缓存;

如果缓存过期了,会在Request头部加上If-None-Match字段,该字段的值就是Etag唯一标识;

服务器再次收到请求后,会根据If-None-Match值与当前请求的资源生成的唯一标识进行比较:

如果值相等,则但会304 Not Modified , 不会返回资源;

如果不相等 , 则返回200状态码和返回资源 , 并在Response 头部机上新的Etag唯一标识;

如果浏览器收到304的请求响应状态码,则会从本地缓存中加载资源,否则更新资源.

