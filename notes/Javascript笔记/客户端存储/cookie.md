# Cookie , Token , Session

## Cookie:

1.  什么是Cookie?

​       Cookie是通过浏览器将服务器返回的数据保存在本地上的一小块数据(一般小于4kb).当浏览器发送请求且浏览器存在Cookie时,浏览器会自动在请求头携带上Cookie数据.引入Cookie的意义是因为HTTP的请求是无状态的,如:浏览器发出的请求服务器没办法区分浏览器用户身份以及用户的相关操作状态(可以通过cookie传递这些信息).

2.Cookie主要使用在一下场景:

  会话状态管理(如用户登录状态,及其他需要记录的信息)

   个性化设置(如用户自定义设置)

   浏览器追踪行为(如追踪分析用户行为)

3.同源限制:

   协议相同,域名相同,端口相同

只有同源的网页才能共享Cookie,服务器可以在设置Cookie的时候,指定Cookie的所属域名为一级域名,比如: .example.com.

这样的话,二级域名和三级域名不同做任何设置,都可以读取这个Cookie.

4.创建Cookie:

 (1)浏览器自动创建:

   当服务器收到HTTP请求时,服务器可以在响应头里设置一个Set-Cookie选项,浏览器接收到响应后会自动保存下Cookie,之后浏览器对该服务器的每一次请求都会通过请求头把Cookie信息发送给服务器.

  eg: (nodejs + express):

```js
const express = require('express')
let app = express();
app.get('/',(req,res)=>{
    console.log(req.headers.cookie)
    res.cookie('token','dsafdsfd453542scdsfd')//设置Cookie
    res.send('path is /')
});
app.get('/login',(req,res)=>{
    console.log(req.headers.cookie)
    res.send('path is /login')
});
app.listen(3000,()=>{
    console.log('express服务已经启动~~~');
})
```

(2).通过JS设置Cookie(也可以设置过期时间,域名,路径等).

Warning: 字符串中不能包含HttpOnly标识.可以包含expires,max-age,domian,path,secure.基本用法:

```js
// 可以直接在浏览器控制台输入进行测试
document.cookie = "token=dsagsdshj43sfs";
```

5.Cookie的设置详解:

 (1)过期时间设置(Expires字段):

   设置Cookie 30S 后过期:

```js
// 30s 后浏览器 cookie 自动清空
res.cookie('token','dsafdsfd453542scdsfd',{expires:new Date(Date.now() + 30*1000)})
```

设置Cookie的过期时间,必须是GMT格式的时间(new Date().toGMTSting() 或 new Date.toTUCString() 获得).

`expires=Thu, 25 Feb 2018 04:18:00 GMT` 表示 Cookie 在 2018 年 2 月 25 日 4:18 分失效，浏览器会清空失效的 Cookie。 

如果没有设置expires,默认有效期为Session,即会话Cookie,关闭浏览器后清空.

expires是http1.0中的属性,在Http1.1中由max-age替代,两种的作用是一样的,只是语法不同.

max-age是以秒为单位时间段,cookie失效时刻 = 创建时刻 + max-age,也就是存货多少秒.默认值为 -1 , 表示浏览器关闭就删除的会话Cookie , 0 表示马上失效(删除).

 (2) . 设置域(domain),指定cookie在哪个域下可以被接收:

 Warning: 如果不指定domian则默认是当源origin,但不包括子域.如果指定了Domian则一般包含子域(二级子域,三级子域).

```js
res.cookie('token','dsafdsfd453542scdsfd',{domain:'localhost'})
```

假设有一个domian为baidu.com的Cookie.若请求的域名是baidu.com,api.baidu.com都会发送该Cookie(一级域名含二级域名).如果请求google.com就不会发送这个Cookie.

如果是跨域XHR请求,即使domain和path都满足Cookie的domain和path,默认情况下Cookie也不会添加到请求头中.

domian可以设置为页面本域或父域,例如www.baidu.com可以设置www.baidu.com和baidu.com这两个域.

(3).设置路劲(path),指定cookie在当前主机下那些路径可以接受Cookie.

设置在/login下接受Cookie.(只要是/login开头的都能接受,如:/login/user等),此时"/"下是没有Cookie的.

```js
res.cookie('token','dsafdsfd453542scdsfd',{path:'/login'})
```

/表示所有路劲. path的默认值为设置该Cookie的网页所在目录.

(4) 设置不能通过javascript访问cookie. (HttpOnly字段)

  不能通过document.cookie访问.

  ```js
res.cookie('token','dsafdsfd453542scdsfd',{httpOnly:true})
  ```

默认情况下,Cookie不会带HttpOnly选项.可以通过JS读取,修改,删除Cookie.

(5) 设置secure字段:

  标记为secure的Cookie只能通过被Https协议加密过的请求发送给给服务端.(通过https创建的Cookie只能通过Https请求将Cookie携带到服务器,通过http无法拿到Cookie).

  ```js
res.cookie('token','dsafdsfd453542scdsfd',{secure:true}
  ```

(6)设置 someSite ，它有三个可选值 None、strict、Lax

首先了解下什么是跨站请求？比如说有 A、B两个网站，其中A站请求会产生 Cookie，且后续访问请求需携带回 Cookie（身份认证），如果在B网站通过链接的形式访问A站资源这个就叫跨站。这种情况访问成功与否会根据 Cookie 设置的 someSite 而定。

- someSize:None: 浏览器在同站请求、跨站请求下都会发送 Cookies
- someSize:Strict: 浏览器只会在相同站点下发送 Cookies
- someSize:Lax: 与 strict 类似，不同的是它可以从外站通过链接导航到该站。

6. 通过JS访问Cookie :

    设置了HttpOnly标志的Cookie无法访问.

   ```js
   document.cookie
   ```

   

7. 跨域

​        从安全角度考虑,浏览器无法获取跨域的Cookie是不会变的,跨域携带Cookie只有两种方法:

  nginx 转发到同一个域名.

   前后台设置withCredentials , Access-Control-Allow-Credentials

默认情况下浏览器对跨域请求不会携带Cookie,但鉴于Cookie在身份验证等方面的重要性,CORS推荐使用额外的响应头字段来允许跨域发送Cookie.

在open XMLHttpRequest之后,设置withCredentials = true 可让该跨域请求携带Cookie (携带的是目标页面所在域的Cookie)

```js
var xhr = new XMLHttpRequest();
xhr.open('GET', url);
xhr.withCredentials = true;
xhr.send();
```

Access-Control-Allow-Credentials

 只设置客户端当然是没用的,还需要目标服务器接受让你跨域发送的Cookie.否则会被浏览器的同源策略挡住:

 服务器同时设置Access-Control-Allow-Credentials 响应头为"true" , 即可允许跨域请求携带Cookie.

Access-Control-Allow-Origin 

除了设置Access - Control - Allow - Credentials 之外,跨域发送Cookie还要球Access-Control-Allow-Origin不允许使用通配符 * . 否则,浏览器还是会组织跨域请求.

小结: Cookie更多的用途使用在身份认证来保证网站资源的安全性,而不是大数据量的本地数据存储.大数据量本地存储方案取而代之的是 webstorage 和 indexedDB.设置Cookie过期时间有助于防止会话固定攻击.设置HttpOnly有助于跨站点脚本(xss)攻击.设置SameSite可以阻止跨站请求伪造攻击(CSRF).

