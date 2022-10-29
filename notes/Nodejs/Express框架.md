# Node.js Express框架

## Express简介

Express是一个简洁而灵活的node.js Web应用框架,提供了一系列强大特性帮助用户创建各种Web应用,和丰富的Http工具.

使用Express可以快速地搭建一个完整功能地网站.

Express框架核心特征:

- 可以设置中间件来响应 HTTP 请求。
- 定义了路由表用于执行不同的 HTTP 请求动作。
- 可以通过向模板传递参数来动态渲染 HTML 页面。

## 安装Express:

```js
npm install express --save
```

以上命令会将 Express 框架安装在当前目录的 **node_modules** 目录中， **node_modules** 目录下会自动创建 express 目录。以下几个重要的模块是需要与 express 框架一起安装的： 

- **body-parser** - node.js 中间件，用于处理 JSON, Raw, Text 和 URL 编码的数据。
- **cookie-parser** - 这就是一个解析Cookie的工具。通过req.cookies可以取到传过来的cookie，并把它们转成对象。
- **multer** - node.js 中间件，用于处理 enctype="multipart/form-data"（设置表单的MIME编码）的表单数据。

## 第一个 Express 框架实例

```js
const express = require('express');
let app = express();

app.get('/',(req,res)=>{
    res.send('Hello World!');
})

app.listen(8081);
```

## 请求和响应:

Express应用使用回调函数的参数: request 和 response 对象来处理请求和响应的数据.

```js
app.get('/', function (req, res) {
   // --
})
```

**request** 和 **response** 对象的具体介绍：

**Request 对象** - request 对象表示 HTTP 请求，包含了请求查询字符串，参数，内容，HTTP 头部等属性。常见属性有：

1. req.app：当callback为外部文件时，用req.app访问express的实例
2. req.baseUrl：获取路由当前安装的URL路径
3. req.body / req.cookies：获得「请求主体」/ Cookies
4. req.fresh / req.stale：判断请求是否还「新鲜」
5. req.hostname / req.ip：获取主机名和IP地址
6. req.originalUrl：获取原始请求URL
7. req.params：获取路由的parameters
8. req.path：获取请求路径
9. req.protocol：获取协议类型
10. req.query：获取URL的查询参数串
11. req.route：获取当前匹配的路由
12. req.subdomains：获取子域名
13. req.accepts()：检查可接受的请求的文档类型
14. req.acceptsCharsets / req.acceptsEncodings / req.acceptsLanguages：返回指定字符集的第一个可接受字符编码
15. req.get()：获取指定的HTTP请求头
16. req.is()：判断请求头Content-Type的MIME类型

**Response 对象** - response 对象表示 HTTP 响应，即在接收到请求时向客户端发送的 HTTP 响应数据。常见属性有：

1. res.app：同req.app一样
2. res.append()：追加指定HTTP头
3. res.set()在res.append()后将重置之前设置的头
4. res.cookie(name，value [，option])：设置Cookie
5. opition: domain / expires / httpOnly / maxAge / path / secure / signed
6. res.clearCookie()：清除Cookie
7. res.download()：传送指定路径的文件
8. res.get()：返回指定的HTTP头
9. res.json()：传送JSON响应
10. res.jsonp()：传送JSONP响应
11. res.location()：只设置响应的Location HTTP头，不设置状态码或者close response
12. res.redirect()：设置响应的Location HTTP头，并且设置状态码302
13. res.render(view,[locals],callback)：渲染一个view，同时向callback传递渲染后的字符串，如果在渲染过程中有错误发生next(err)将会被自动调用。callback将会被传入一个可能发生的错误以及渲染后的页面，这样就不会自动输出了。
14. res.send()：传送HTTP响应
15. res.sendFile(path [，options] [，fn])：传送指定路径的文件 -会自动根据文件extension设定Content-Type
16. res.set()：设置HTTP头，传入object可以一次设置多个头
17. res.status()：设置HTTP状态码
18. res.type()：设置Content-Type的MIME类型

## 路由

我们已经了解了HTTP请求的基本应用,而路由决定了由谁(指定脚本)去响应客户端请求.

在HTTP请求中,我们可以通过路由提取出请求的URL以及GET/POST参数.

接下来我们扩展Hello World , 添加一些功能来处理更多类型的HTTP请求.

```js
var express = require('express');
var app = express();
 
//  主页输出 "Hello World"
app.get('/', function (req, res) {
   console.log("主页 GET 请求");
   res.send('Hello GET');
})
 
 
//  POST 请求
app.post('/', function (req, res) {
   console.log("主页 POST 请求");
   res.send('Hello POST');
})
 
//  /del_user 页面响应
app.get('/del_user', function (req, res) {
   console.log("/del_user 响应 DELETE 请求");
   res.send('删除页面');
})
 
//  /list_user 页面 GET 请求
app.get('/list_user', function (req, res) {
   console.log("/list_user GET 请求");
   res.send('用户列表页面');
})
 
// 对页面 abcd, abxcd, ab123cd, 等响应 GET 请求
app.get('/ab*cd',function(req,res){
    console.log("/ab*cd GET 请求");
    res.send('正则匹配');
})

app.listen(8080);
```



## 静态文件

Express提供了内置的中间件 express.static 来设置静态文件 如: 图片 , css , JavaScript 等.

可以使用express.static 中间件来设置静态文件路径. 例如 , 如果将图片 , CSS , JavaScript文件放在 public目录下,可以这么写:

```js
app.use('/public', express.static('public'));
```

## GET方法:

以下实例演示了在表单中通过 GET 方法提交两个参数，我们可以使用 server.js 文件内的 **process_get** 路由器来处理输入： 

index.html 文件代码:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Express框架</title>
</head>
<body>
     <form action="http://127.0.0.1:8081/process_get" method="get">
        First Name:<input type="text" name="first_name"><br>
        Last Name: <input type="text" name="last_name">
        <input type="submit" value="Submit">
     </form>
</body>
</html>
```

server.js代码:

```js
const express = require('express');
const app = express();

app.use('/public',express.static('public'));

app.get('/index.html',function(req,res){
    console.log(123);
    res.sendFile(__dirname+'/'+'index.html');
})

app.get('/process_get',function(req,res){
    console.log(req.query.first_name);
    console.log(req.query.last_name);
    let response = {
        'first_name':req.query.first_name,
        'last_name':req.query.last_name
    };
    console.log(response);
    res.send(JSON.stringify(response));
})

app.listen(8081);
```

## POST 方法:

index.html文件:

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Express框架</title>
</head>
<body>
     <form action="http://127.0.0.1:8080/process_post" method="POST">
        First Name:<input type="text" name="first_name"><br>
        Last Name: <input type="text" name="last_name">
        <input type="submit" value="Submit">
     </form>
</body>
</html>
```

index.js文件:

```js
const express = require('express');
const app = express();
const bodyParser = require('body-parser');

//创建 application/x-www-form-urlencoded 编码解析
let urlencodedParser = bodyParser.urlencoded({extended:false});

app.use('/public',express.static('public'));

app.get('/index.html',function(req,res){
    res.sendFile(__dirname + "/" + 'index.html');
});

app.post('/process_post',urlencodedParser,function(req,res){
    console.log(123);
    console.log(req.body);
    let response = {
        'first_name':req.body.first_name,
        'last_name':req.body.last_name
    };
    console.log(response);
    res.send(JSON.stringify(response));
});

app.listen(8080);
```

## 文件上传

以下我们创建一个用于上传文件的表单，使用 POST 方法，表单 enctype 属性设置为 multipart/form-data。 以下我们创建一个用于上传文件的表单，使用 POST 方法，表单 enctype 属性设置为 multipart/form-data。 

index.html

```js
<html>
<head>
<title>文件上传表单</title>
</head>
<body>
<h3>文件上传：</h3>
选择一个文件上传: <br />
<form action="/file_upload" method="post" enctype="multipart/form-data">
<input type="file" name="image" size="50" />
<br />
<input type="submit" value="上传文件" />
</form>
</body>
</html>
```

index.js文件

```js
const express = require('express');
const app = express();
const fs = require('fs');
const bodyParser = require('body-parser');
const multer = require('multer');
const { response } = require('express');

app.use('/public',express.static('public'));
app.use(bodyParser.urlencoded({extended:false}));
app.use(multer({dest:'/tmp/'}).array('image'));

app.get('/index.html',function(req,res){
    res.sendFile(__dirname + '/' + 'index.html');
})

app.post('/file_upload',function(req,res){
    console.log(req.files[0]);
    let response;
    let des_file = __dirname + '/' + req.files[0].originalname;
    fs.readFile(req.files[0].path,function(err,data){
        fs.writeFile(des_file,data,function(err){
            if(err){
                console.log(err);
            }
            else{
                response = {
                    message:'File uploaded successfully', 
                    filename:req.files[0].originalname
                };
            }
            console.log(response);
            res.send(JSON.stringify(response));
        })
    })
})

app.listen(8080);
```

## Cookie管理

我们可以使用中间件向 Node.js 服务器发送 cookie 信息，以下代码输出了客户端发送的 cookie 信息： 

```js
// express_cookie.js 文件
var express      = require('express')
var cookieParser = require('cookie-parser')
var util = require('util');
 
var app = express()
app.use(cookieParser())
 
app.get('/', function(req, res) {
    console.log("Cookies: " + util.inspect(req.cookies));
})
 
app.listen(8081)
```

