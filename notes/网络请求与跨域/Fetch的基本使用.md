# Fetch API

Fetch API能够执行XHR对象的所有任务,但更容易使用,接口也更现代,能够在Web工作线程等现代Web工具中使用.XHR可以选择异步,Fetch 必须是异步.

Fetch本身是使用JS请求资源的优秀工具,同时这个API也能够应用在服务线程中,提供拦截,重定向和修改通过fetch()生成的请求接口.

## 一.基本用法

fetch()方法是暴露在全局作用域中的,包括主页执行线程,模块和工作线程.调用这个方法,浏览器就会向给定的URL发送请求.

### 1.分派请求

 fetch()只有一个必须的参数input.多数情况下,这个参数是要获取资源的URL.这个方法返回一个期约(Promise).

```js
 let r = fetch('/bar');
 console.log(r);//Promise<pending>
```

URL的格式(相对路径,绝对路径)的解释和XHR对象一样.

请求完成,资源可用时,期约会解决为一个Response对象.这个对象是API的封装,可以通过它取得相应的资源.获取资源要使用这个对象的属性和方法,掌握响应的情况并将负载转换为有用的形式.

```js
fetch('/bar.txt').then((response)=>{
        console.log(response);
      })
```

### 2.读取响应

 读取响应内容的最简单方式是取得纯文本格式的内容,这要用到**text()**方法.这个方法返回一个期约,会解决为取得资源的完整内容.

```js
fetch('/bar.txt').then((response)=>{
        response.text()
        .then((data)=>{
            console.log(data);
        })
      })
```

```js
fetch('/bar.txt').then((response)=>{
       response.text();
      }).then((data)=>{
        console.log(data);
      })
```

### 3.处理状态码和请求失败

Fetch API 支持通过Response的status(状态码)和statusText(状态文本)属性检查响应的状态.成功获取响应的请求通常会产生值为200的状态码.

```js
 fetch('/bar').then((response)=>{
        console.log(response.status);
        console.log(response.statusText);
      })
//response.status 的值为 200 和 response.statusText 的值为  ok 则请求成功
```

请求不存在的资源通常会产生值为 404 的状态码:

```js
 fetch('/bar').then((response)=>{
        console.log(response.status);
        console.log(response.statusText);
      })
//response.status 的值为 404 和 response.statusText 的值为  Not Found 则请求出错
```

请求的URL如果抛出服务器错误会产生值为500的状态码:

```js
 fetch('/bar').then((response)=>{
        console.log(response.status);
        console.log(response.statusText);
      })
//response.status 的值为 500 和 response.statusText 的值为  Internal Server Error 则请求出错
```

虽然请求可能失败(如状态码为500),但都只执行了期约的**解决**处理函数.事实上,只要服务器返回了响应,fetch()期约都会解决.这个行为是合理的:系统级网络协议已经成功完成消息的一次性往返传输.至于真正的"成功"请求,则需要在处理响应时再定义.

通常状态码为200时就会被认为成功了,其他情况可以被认为未成功.为区分这两种情况,可以在状态码非200 ~ 299 时检查Reponse对象的ok属性.

因为服务器没有响应而导致浏览器超时,这样真正的fetch()失败会导致期约被拒绝.

```js
fetch('/hangs-forever').then((response)=>{
        console.log(response);
     },(err)=>{
       console.log(err);
     })
```



违反CORS,无网络连接,HTTPS错配以及其他浏览器/网络策略都会导致期约被拒绝.

可以通过url属性检查通过fetch()发送请求时使用的完整URL.

### 4.自定义选项

 只使用URL时,fetch()会发送GET请求,只包含最低限度的请求头.要进一步配置如何发送请求,需要传入可选的第二个参数init对象.

init对象的填充规则请自行查阅.

## 二.常见的Fetch请求模式

与XHR一样,fetch()既可以发送数据也可以接收数据.使用init对象参数可以配置fetch()在请求体中发送的各种序列化数据.

### 1.发送JSON数据

```js
let payload = JSON.stringify({
        foo:'bar'
       });
       let jsonHeaders = new Headers({
        'Content-type':'application/json'
       });
       fetch('/send-me-json',{
        method:'POST',//发送请求体时必须使用一种TTP方法
        body:payload,
        headers:jsonHeaders
       })
```

### 2.在请求体中发送参数

因为请求体支持任意字符串值,所以可以通过它发送请求参数:

```js
 let payload = 'foo=bar&&baz=qux';

       let paramHeaders = new Headers({
        'Content-type':'application/x-www-form-urlencoded; charset=UTF-8'
       });
       fetch('/send-me-json',{
        method:'POST',//发送请求体时必须使用一种TTP方法
        body:payload,
        headers:paramHeaders
       })
```

### 3.发送文件

因为请求体支持FormData实现,所以fetch()也可以序列化并发送文件字段中的文件:

```js
let imageFormData = new FormData();
       let imageInput = document.querySelector("input[type=file]");
       imageFormData.append('image',imageInput.files[0]);

       fetch('/img-upload',{
        method:'POST',
        body:imageFormData
       })
```

也支持发送多个文件:

```js
let imageFormData = new FormData();
       let imageInput = document.querySelector("input[type='file'][multiple]");
      
       for(let i=0;i<imageInput.files.length;++i){
        imageFormData.append('image',imageInput.files[i]);
       }

       fetch('/img-upload',{
        method:'POST',
        body:imageFormData
       })
```

### 4.发送跨域请求

从不同的源请求资源,响应要包含CORS头部才能保证浏览器受到响应.没有这些头部,跨域请求会失败并抛出错误.

如果代码不需要访问响应,也可以发送no-cors请求.此时响应的type属性值为opque,因此无法读取响应内容.这种方式适合发送探测请求或者将响应缓存起来以后使用.

```js
fetch('//corss-origin.com',{
    method:'no-cors'
}).then((response)=>{
    console.log(response.type) // opaque
})
```

### 5.中断请求

Fetch APi通过调用 AbortController/AbortSignal对中断请求.调用AbortController.abort()会中断所有网络传输,特别适合希望停止传输大型负载的情况.中断进行中的fetch()请求会导致包含错误的拒绝.

```js
let abortController = new AbortController();
       fetch('wikipedia.zip',{
        signal:abortController.signal
       }).catch((err)=>{
        console.log('aborted!');
       })

       //10毫秒后中断请求
       setTimeout(()=>{
          abortController.abort();
       },10);

       //已经中断
```

下面演示基本用法:

GET请求:

01.html:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script>
       fetch('http://localhost:8080/Test_Fetch?UserName=祁同伟&PassWord=胜天半子').then((response)=>{
        return response.json();
       }).then((data)=>{
        console.log(data);
       })
   </script>
</body>
</html>
```

server.js:

```js
const express  = require('express');
const app = express();

app.use('*',function(req,res,next){
   // 允许的请求主机名及端口号 也可以用通配符*， 表示允许所有主机请求
   res.setHeader('Access-Control-Allow-Origin', '*');
   // 允许请求携带cookie
   res.setHeader('Access-Control-Allow-Credentials', true);
   // 允许的请求方式
   res.setHeader('Access-Control-Allow-Methods', 'GET, POST, OPTIONS, PUT, PATCH, DELETE');
   // 允许的请求头
   res.setHeader('Access-Control-Allow-Headers', 'X-Requested-With,content-type');

   next();
})


app.use('/Test_Fetch',(req,res)=>{
   console.log(req.query);
   let data = {
     UserName:req.query.UserName,
     PassWord:req.query.PassWord
   }
   res.send(JSON.stringify(data));
})

app.listen(8080);
```

控制台打印:

```
{UserName: '祁同伟', PassWord: '胜天半子'}
```

POST请求:

01.html:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script>
       let data = {
        'UserName':'祁同伟',
        'PassWord':'胜天半子'
       }
       fetch("http://localhost:8080/Test_Fetch",{
        method:'POST',
        "Access-Control-Allow-Origin" : "*",
        "Access-Control-Allow-Credentials" : true,
        headers:{
            'Content-Type':'application/json'
        },
        body:JSON.stringify(data)
       }).then((response)=>{
        return  response.json();
       }).then((data)=>{
        console.log(data);
       })
   </script>
</body>
</html>
```

server.js

```js
const express  = require('express');
const bodyParser = require('body-parser');
const app = express();

app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())

app.use('*',function(req,res,next){
   // 允许的请求主机名及端口号 也可以用通配符*， 表示允许所有主机请求
   res.setHeader('Access-Control-Allow-Origin', '*');
   // 允许请求携带cookie
   res.setHeader('Access-Control-Allow-Credentials', true);
   // 允许的请求方式
   res.setHeader('Access-Control-Allow-Methods', 'GET, POST, OPTIONS, PUT, PATCH, DELETE');
   // 允许的请求头
   res.setHeader('Access-Control-Allow-Headers', 'X-Requested-With,content-type');

   next();
})

app.use('/Login',(req,res)=>{
   let data = {
      'UserName':req.query.UserName,
      'PassWord':req.query.PassWord
   }
   res.send(JSON.stringify(data));
})

app.post('/Test_Fetch',(req,res)=>{
   console.log(req.body);
   let data = {
      UserName:req.body.UserName,
      PassWord:req.body.PassWord
   }
   res.send(JSON.stringify(data));
})

app.listen(8080);
```

控制台打印:

```
{UserName: '祁同伟', PassWord: '胜天半子'}
```

ok,本期就聊到这块,下一期我们继续聊webSocket. 以上内容为个人理解,如有错误请大佬斧正!