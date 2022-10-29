# 网络请求与远程资源

# 一.AJAX技术

​        AJAX技术涉及发送服务器请求额外数据而不刷新页面,从而实现更好的用户体验. 把AJAX推到历史舞台上的关键技术是XMLHttpRequest(XHR)对象.XHR为发送服务器请求和获取响应提供了合理的接口.这个接口可以实现异步从服务器获取额外数据,意味着用户点击不用页面刷新也可以获取数据,通过XHR对象获取数据后,可以使用DOM方法把数据插入网页.虽然Ajax这个名称中包含XML,但实际上Ajax通信与数据格式无关.这个技术主要是可以实现在不刷新页面的情况下从服务器获取数据,格式并不一定是XML.

## 1.XMLHttpRequest对象

所有现代浏览器都通过XMLHttpRequest构造函数原生支持XHR对象.

```js
let xhr = new XMLHttpRequest();
```

### (1) 使用XHR

  使用XHR对象首先要调用open()方法,这个方法接收3个参数,请求类型('get','post'等),请求URL,以及表示请求是否异步的布尔值.

```js
let xhr = new XMLHttpRequest();
xhr.open('get','http://localhost:8080/User',true);
```

调用open()方法不会实际发送请求,只是为发送请求做好准备.

Warning: 只能发送同源URL,也就是域名相同,端口相同,协议相同.如果请求的URL与发送请求的页面在任何方面有所不同,则会抛出安全错误(跨域请求). 关于解决跨域问题,在下一篇文章为大家详细介绍.

要发送定义好的请求,必须像下面这样调用send()方法:

```js
let xhr = new XMLHttpRequest();
xhr.open('get','http://localhost:8080/User',true);
xhr.send(null);
```

send()方法接收一个参数,是作为请求体发送的数据.如果不需要请求体,则必须传null,因为这个参数在某些浏览器中是必需的.调用send()之后,请求就会发送到服务器.

如果这个请求是同步的,那么JavaScript代码会等待服务器响应之后再继续执行.受到响应后,XHR对象的以下属性会被填充上数据.

   **responseText**: 作为响应体返回的文本.

   **responseXML**:如果响应的内容类型是"text/xml"或"application/xml",那就是包含响应数据的XML DOM文档.

  **status**:响应的HTTP状态.

  **statusText**: 响应的HTTP状态描述.

收到响应后,第一步要检查status属性以确保响应成功返回.一般来说,HTTP状态码为2xx表示成功.此时,responseText或responseXML属性中会有内容.如果HTTP状态码是304,则表示资源未修改过,是从浏览器缓存中直接拿取的.当然这也意味着本次响应是有效的.

 为确保收到正确的响应,应该检查这些状态.

```js
let xhr = new XMLHttpRequest();
        xhr.open('get','http://localhost:8080/User',false);
        xhr.send(null);
        if(xhr.status >= 200 && xhr.status <300 || xhr.status === 304){
            alert(xhr.responseText);
        }
        else{
            alert('Request was unsuccessful: '+ xhr.status);
        }
```

以上代码可能先死服务器返回的内容,也可能显示错误信息,取决于HTTP响应的状态码.为确定下一步该执行什么操作,最好检查status而不是statusText属性,因为后者已经被证明在跨浏览器的情况下不可靠.无论是什么响应内容类型,responseText属性始终会保存响应体,而responseXML则对于非XML数据是null.

虽然可以像前面一样发送同步请求,但多数情况下最好使用异步请求,这样可以不阻塞JavaScript代码继续执行.XHR对象有一个readyState属性,表示当前处在请求/响应的哪个阶段.

 这个属性有如下可能的值:

  **0**: 未初始化.尚未调用open()方法.

  **1**: 已打开,已调用open()方法,尚未调用send()方法.

  **2**:已调用send()方法,尚未收到响应.

  **3**: 接受中,已经收到部分响应.

 **4**: 完成. 已经收到所有响应,可以使用了.

每次readyState从一个值变成另一个值,都会触发readystatechange事件.因此可以借此机会检查readyState的值.一般来说,我们唯一需要关心的值是4,表示数据已经就绪.**为保证跨浏览器兼容,onreadystatechange 事件处理程序应该在调用opne()之前赋值.**

```js
let xhr = new XMLHttpRequest();
        xhr.onreadystatechange = ()=>{
            if(xhr.readyState  ===  4){
                if(xhr.status >= 200 && xhr.status < 300 || xhr.status === 304){
                    //响应成功处理
                }
                else{
                    //响应失败处理
                }
            }
        }
        xhr.open('get','http://localhost:8080/User',true);
        xhr.send(null);
```

在收到响应之前如果想要取消异步请求,可以调用abort()方法.

```js
xhr.abort()
```

调用这个方法之后,XHR对象会停止触发事件,并阻止访问这个对象上任何与响应相关的属性.中断请求后,应该取消对XHR对象的引用.由于内存问题,不推荐重用XHR对象.

## 2.HTTP头部

每个HTTP请求和响应都会携带一些头部字段,这些字段可能对开发者有用.XHR对象会通过一些方法暴露与请求相关的头部字段.

  默认情况下,XHR请求会发送以下头部字段:

```js
Accept:浏览器可以处理的内容类型
Accept-Charset : 浏览器可以显示的字符集
Accept-Encoding : 浏览器可以处理的压缩编码类型.
Accept-Language: 浏览器使用的语言
Connection: 浏览器与服务器的连接类型
Cookie: 页面中设置的Cookie
Host: 发送请求的页面所在的域
Referer: 发送请求的页面的URL.注意,这个字段在HTTP规范中就拼错了,所以考虑到兼容性也必须将错就错
User-Agent: 浏览器的用户代理字符串
```

虽然不同浏览器发送的确切头部字段可能各不相同,但这些通常都是会发送的.如果需要发送额外的请求头部,可以使用**setRquestHeader()**方法.这个方法接收两个参数:头部字段的名称和值.

为保证请求头部被发送,必须在open()之后,send()之前调用setRequestHeader().

```js
let xhr = new XMLHttpRequest();
xhr.onreadystatechange = function(){
    if(xhr.readyState === 4){
        if(xhr.status >= 200 && xhr.status <300   || xhr.status == 304){
            //请求正常处理
        }
        else
        {
            //请求错误处理
        }
    }
    xhr.open('get','example.php',true);
    xhr.setRequestHeader('MyHeader',"MyValue");
    xhr.send(null);
}
```

服务器通过自定义头部可以确定适当的操作.自定义头部一定要区别于浏览器正常发送的头部,否则可能影响服务器的正常响应.有些浏览器允许重写默认头部,有些浏览器则不允许.

可以使用getRequestHeader()方法从XHR对象获取响应头部,只要传入要获取头部的名称即可.如果想要取得所有响应头部,可以使用getAllRequestHeader()方法,这个方法会返回包含所有头部的字符串.

### 3.GET请求

最常用的请求方法是GET请求,用于向服务器查询某些信息.必要时,需要在GET请求的URL后面添加字符串查询.对XHR而言,查询字符串必需正确编码后添加到URL后面,然后再传给open()方法.

发送GET请求最常见的一个错误是查询字符串格式不对.查询字符串中每个名和值都必须使用encodeURIComponent()编码,所有名/值对必须以和号(&)分隔,如下面的例子所示:

```js&#39;
xhr.open("get","example.php?name1=value1&name2=value2",true);
```

可以使用以下函数将查询字符串添加到现有的URL末尾:

```js
function addURLParam(url,name,value){
            url += (url.indexOf("?") == -1 ? "?" : "&");
            url += encodeURIComponent(name) + "=" + encodeURIComponent(value);
            return url;
        }

//在此就不详细介绍该函数了,相信会一点js的同学都能看懂
```

可以使用这个函数构建请求URL,

```js
let url = 'example.php';

url = addURLParam(url,"name","Nicholas");
url = addURLParam(url,"book","Professional JavaScript");

xhr.open("get",url,true);
```

## 4.POST请求

第二个最常用的方法是POST请求,用于向服务器发送应该保存的数据. 每个POST请求都应该在请求体中携带提交的数据,而GET请求则不然.POST请求的请求体可以包含非常多的数据,而且数据可以是任意格式. 要初始化POST请求,open()方法的第一个参数要传"post",

默认情况下,对服务器而言,POST请求与提交表单是不一样的.服务器逻辑需要读取原始POST数据才能取得浏览器发送的数据.不过,可以使用XHR模拟表单提交.为此,第一步需要把Content-Type头部设置为"application/x-www-formurlencoded",这是提交表单时使用的内容类型.

第二步是创建对应格式的字符串.POST数据此时使用与查询字符串相同的格式.如果网页中确实有一个表单需要序列化并通过XHR发送到服务器,则可以使用serialize()函数来创建相应的字符串.

```js
function submitData(){
            let xhr = new XMLHttpRequest();
            xhr.onreadystatechange = ()=>{
                if(xhr.readyState === 4){
                    if((xhr.status >=200 && xhr < 300) || xhr.status === 304){
                        //响应正常处理
                    }
                    else{
                        //响应错误处理
                    }
                }
            }

            xhr.open('POST',url,true);
            xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded');
            let form = document.getElementById('User_info');
            xhr.send(serialize(form));
        }
```

现代Web应用程序中经常需要对表单数据进行系列化,因此XMLHttpRequest Level2新增了FormData类型,FormData类型便于表单序列化,也便于创建与表单类似的数据然后通过XHR发送. 大家可以自行了解以下FormData.

## 5.接下来,为大家演示一下Ajax中使用GET请求:

首先我们封装一个Ajax文件:  Ajax.js

```js
function Ajax(type,url,async){
    let xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function(){
        if(xhr.readyState === 4){
            if((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304){
               console.log(xhr.responseText);
            }
            else{
              return  console.log("Request was unsuccessful: "+ xhr.status);
            }
        }
    }
    xhr.open(type,url,async);
    xhr.send(null);
}
```

main.js:

```js

function Submit() {
    let UserName = document.getElementById('UserName').value;
    let PassWord = document.getElementById('PassWord').value;
    let url = 'http://localhost:8080/Login';
    url = addURLParam(url,'UserName',UserName);
    url = addURLParam(url,'PassWord',PassWord);
    Ajax("get",url,false);
 }

 function addURLParam(url,name,value){
    url += (url.indexOf('?') == -1 ? '?' : "&");
    url += encodeURIComponent(name) + "=" +  encodeURIComponent(value);
    return url;
}

```

index.html:

```js
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ajax技术</title>
</head>

<body>
    <form>
        UserName:<input type="text" id="UserName"><br>
        Password:<input type="password" id="PassWord"><br>
        <button type="button" onclick="Submit()">点击提交</button>
    </form>
    <script src="./Ajax.js"></script>
    <script  src="./main.js"> </script>
</body>

</html>
```

Node.js后台:

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

app.use('/Login',(req,res)=>{
   let data = {
      'UserName':req.query.UserName,
      'PassWord':req.query.PassWord
   }
   res.send(JSON.stringify(data));
})

app.listen(8080);
```

输入UserName : '123123123' , PassWord:'123123123',

看一下控制台打印:

```js
{"UserName":"123123","PassWord":"123123"}
```

ok , 那么本期分享就到这了, 下一期分享 fech API.  以上纯属个人观点, 如有错误还请大佬匡正!