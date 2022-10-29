# JSON Web Tkoen

JSON Web Token (缩写JWT) 是目前最流行的跨域认证解决方案.

## 一.跨域认证的问题

互联网服务离不开用户认证.一般流程是下面这样:

1. 用户向服务器发送用户名和密码.
2. 服务器验证通过后,在当前对话(session)里面保存相关数据,比如用户角色,登录时间等等.
3. 服务器向永辉返回一个session_id,写入用户的Cookie.
4. 用户随后的每一次请求,都会通过Cookie,将session_id传回服务器.
5. 服务器收到session_id,找到前期保存的数据,由此得知用户的身份.

这种模式的问题在于,扩展性不好.单机当然没有问题,如果是服务器集群,或者是跨域的服务架构,就要求session数据共享,每台服务器都能够读取session.

举例来说,A网站和B网站是同一家公司的关联服务.现在要求,用户只要在其中一个网站登录,再访问另一个网站就会自动登录,请问怎么实现?

一种解决方案是session数据持久化,写入数据库或别的持久层.各种服务收到请求后,都向持久层请求数据.这种方案的优点是架构清晰,缺点是工程量比较大.另外,持久层万一挂了,就会单点失败.

另一种方案是服务器索性不保存session数据了,所有数据都保存在客户端,每次请求都发回服务器.JWT就是这种方案的一个代表.

## JWT的原理

JWT的原理是,服务器认证后,生成一个JSON对象,发回给用户.eg:

```js
{
    "姓名":"张三",
    "角色":"管理员",
    "到期时间":"2018年7月1日0点0分"
}
```

以后,用户域服务端通信的时候,都要发回这个JSON对象.服务器完全只靠这个对象认定用户身份.为了防止用户篡改数据,服务器在生成这个对象的时候,会加上签名.

服务器就不保存任何session数据了,也就是说,服务器变成无状态了,从而比较容易实现扩展.

JWT的数据结构

它是一个很长的字符串,中间用点(.)分割成三个部分.注意,JWT内部是没有换行的,这里只是为了便于展示,将它写成了几行.

JWT的三个部分依次如下.

  Header (头部)

  Payload (负载)

  Signature (签名)

写成一行,就是 Header , Payload , Signnature.

(1) Header:

  Header部分是一个JSON对象,描述JWT的元数据,通常是下面的样子.

```js
{
  "alg": "HS256",
  "typ": "JWT"
}
```

上面代码中,alg属性表示签名的算法,默认是HMAC SHA256(写成HS256); typ属性表示这个令牌(token)的类型(type),JWT令牌统一写为JWT.

最后,将上面的JSON对象使用Base64URL算法转成字符串.

(2) Payload:

 Payload部分也是一个JSON对象,用来存放需要传递的数据.JWT规定了7个官方字段,供选用.

```js
iss (issuer)：签发人
exp (expiration time)：过期时间
sub (subject)：主题
aud (audience)：受众
nbf (Not Before)：生效时间
iat (Issued At)：签发时间
jti (JWT ID)：编号
```

除了官方字段,你还可以在这个部分定义私有字段,下面就是一个例子.

```js
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

注意,JWT默认是不加密的,任何人都可以读到,所以不要把秘密信息放在这个部分.

这个JSON对象也要使用Base64URL算法转成字符串.

(3) Signature

 Signature 部分是对前面两部分的签名,防止数据篡改.

首先,需要指定一个密钥(secret).这个密钥只有服务器才知道,不能泄露给用户.然后,使用Header里面指定的签名算法(默认是HMAC SHA256),按照下面的公式产生签名.

```js
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

算出签名以后,把Header,Playload,Signature三个部分拼成一个字符串,每个部分之间用"点"(.)分隔,就可以返回给用户.

(4) Base64URL 

 前面提到过,Header和Payload串型化的算法是 Base64URL。这个算法跟Base64算法基本类似,但有一些小的不同.

JWT 作为一个令牌（token），有些场合可能会放到 URL（比如 api.example.com/?token=xxx）。Base64 有三个字符`+`、`/`和`=`，在 URL 里面有特殊含义，所以要被替换掉：`=`被省略、`+`替换成`-`，`/`替换成`_` 。这就是 Base64URL 算法。 

(5)JWT的几个特点:

1. JWT默认是不加密的,但也可以加密.生成原始TOKEN以后,可以用密钥再加密一次.
2. JWT不加密的情况下,不能将私密数据写入JWT.
3. JWT不仅可以用于认证,也可以用于交换信息.有效使用JWT,可以降低服务器查询数据库的次数.
4. JWT的最大缺点是,由于服务器不把偶才能session状态,因此无法在使用过程中废止某个token,或者更改token的权限.也就是说,一旦JWT签发了,在到期之前就会始终有效,除非服务器部署额外的逻辑.
5. JWT本身包含了认证信息,一旦泄露,任何人都可以获得该令牌的所有权限.为了减少盗用,JWT的有效期应该设置得比较短.对于一些比较重要得权限,使用是应该再次对用户进行认证.
6. 为了减少盗用,JWT不应该使用HTTP协议明码传输,要使用HTTPS协议传输.

Nodejs中关于Token得使用:

通过npm包jsonwebToken来完成token的生成和验证.

```js
npm install --save jsonwebtoken
```



index.js:

```js
const express = require("express")
const app = express()
const jwt = require("jsonwebtoken")
//撒盐，加密时候混淆
const secret = '113Bmongojsdalkfnxcvmas'
const user = {
	id:10,
	name:"Bmongo",
	age:16,
}

//生成token
//info也就是payload是需要存入token的信息
function createToken(info) {
	let token = jwt.sign(info, secret, {
        //Token有效时间 单位s
		expiresIn:60 * 60 * 10
	})
	return token
}

//验证Token
function verifyToken(token) {
	return new Promise((resolve, reject) => {
		jwt.verify(token, secret, (error, result) => {
            if(error){
                reject(error)
            } else {
                resolve(result)
            }
		})
	})
}

//设置允许跨域
app.use(function(req, res, next) {
    //指定允许其他域名访问 *所有
	res.setHeader("Access-Control-Allow-Origin", "*");
    //允许客户端请求头中带有的
	res.setHeader("Access-Control-Allow-Headers", "Content-Type,Content-Length, Authorization, Accept,X-Requested-With");
    //允许请求的类型
	res.setHeader("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
	res.setHeader("X-Powered-By",' 3.2.1')
    //让options请求快速返回
	if(req.method=="OPTIONS") res.send(200);
	else  next();
});

//白名单
const whiteList = ['/login']

app.use((req,res,next) => {
    console.log(JSON.stringify(req.headers.authorization))
	if(!whiteList.includes(req.url)) {
		verifyToken(req.headers.authorization).then(res => {
			next();
		}).catch(e => {
			res.status(401).send('您还未登录,请您先登录!')
		})
	} else {
		next();
	}
})

app.use('/login',(req,res) => {
	let token = createToken(user);
	res.json({token})
})

app.get("/api/info", (req,res) => {
	res.send({
		result:1,
		data:{
            "name":"Bmongo",
            "id":1
        }
	})
})

app.listen(3000);
```



前端代码:

```html
<script>
        let xhr = new XMLHttpRequest();
        xhr.onreadystatechange = ()=>{
            if(xhr.readyState === 4){
                if(xhr.status >=200 && xhr.status<300){
                   console.log(JSON.parse(xhr.responseText));
                   sessionStorage.setItem('token',JSON.parse(xhr.responseText).token)
                   
                }
            }
        }
       
        xhr.open('GET','http://localhost:3000/api/info');
        xhr.setRequestHeader('Authorization',sessionStorage.getItem('token'));
        xhr.send();
    </script>
```

