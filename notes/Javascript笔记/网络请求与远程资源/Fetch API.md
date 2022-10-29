24.5 Fetch API:

Fectch API 能够执行XMLHttpRequest 对象的所有任务,但更容易使用,接口也更现代化,能够在web工作线程等现代web工具中使用.XMLHttoRequest可以选择异步,而Fectch API 则必须是异步的. Fectch API 本身是使用js请求资源的瓯绣工具,同时这个API也能够应用在服务线程中,提供拦截,重定向和修改通过fetch()生成的请求接口.

24.5.1 基本用法:

fectch()方法是暴露在全局作用域中的,包括主页面执行线程,模块和工作线程.调用这个方法,浏览器就会向给定的URL发送请求.

1.分派请求.

fetch()只有一个必需的参数input.多数情况下,这个参数是要获取资源的URL.这个方法返回一个期约.

```js
let r = fetch('bar.txt');
         console.log(r);//Promise
```

URL的格式(相对路劲,绝对路径等)的解释与XHR对象一样.请求完成,资源可用时,期约会解决成为以恶搞Pesponsse对象.这个对象是API的封装,可以通过它取得相应资源.获取资源要使用这个方法和属性,掌握响应的情况并将负载转化为有用的形式.

```js
 let r = fetch('bar.txt');
         r.then((response)=>{
             console.log(response)
         })
```

2.读取响应:

读取响应最简单的方式是取得纯文本得到内容,这要用到text()方法.这个方法返回一个期约,会解决为取得资源的完整内容.

```js
fetch('bar.txt').then((response)=>{
          response.text().then((data)=>{
               console.log(data);
          })
        })

        fetch('bar.txt')
          .then((response)=>response.text() )
          .then((data)=>console.log(data));
```

3.处理状态码和请求失败:

 Fetch API 支持通过Response和status(状态码)和statusText(状态文本)属性检查响应状态.成功获取响应的请求会产生值为200的状态码.

请求的URL如果抛出服务器错误会产生值为500的状态码:

通常状态码为200时就被认为成功了,其他情况可以被认为未成功.为区分这两种情况,可以在状态码非200~299时检查Response对象的ok属性.

```js
fetch('bar.txt').then((response)=>{
            console.log(response.status);//200
            console.log(response.ok);//true
          response.text().then((data)=>{
               console.log(data);
          })
        })
```

 因为服务器没有响应而导致浏览器超时,这样真正的fetch()失败会导致期约被拒绝:

4.自定义选项: 只使用URL时,fetch()会发送GET请求,只包含最低限度的请求头.要进一步配置如何发送请求,需要传入可选的第二个参数init对象. p725.

24.5.2 常见的Fetch请求模式:

与XMLHttoRequest 一样,fetch()既可以发送数据也可以接收数据.使用init对象参数,可以配置fetch()在请求体中发送各种序列化数据.

1. 发送JSON数据:

    ```js
   let playload = JSON.stringify({
               foo:'bar'
           });
           let jsonHeaders = new Headers({
               'Content-Type':'application/json'
           });
           fetch('/send-me-json',{
               method:'GET',//发送请求体时必须使用一种HTTP方法
               body:playload,
               headers:jsonHeaders  
           })
    ```

   

2.发送文件

 ```js
let imageFormData = new FormData();
        let imageInput = document.querySelector("input[type='file']");
        
        imageFormData.append('image',imageInput);
        
        fetch('/img-upload',{
            method:'POST',
            body:imageFormData
        })
 ```

5.发送跨域请求:

从不同的源请求资源,响应要包含CORS头部才能保证浏览器收到响应.没有这些头部,跨域请求会失败并抛出错误.

fetch('//cross-origin.com');

如果代码不需要访问响应,也可以发送on-cors请求.此时响应的type属性值为opaque,因此无法读取响应内容.

```js
fetch('//cross-origin.com',{method:'no-cros'});
```

6.中断请求:

Fetch APi支持通过AbortControler/AbortSignal对中断请求.调用AborController.abort()会中断所有网络请求,特别适合希望停止传输大型负载的情况.中断进行中的fetch()请求会导致包含错误的拒绝.

```js
let abortController = new AbortController();
        fetch('bar.txt',{signal:abortController.signal})
         .catch(()=> console.log('aborted!'));

         setTimeout(()=>abortController.abort(),10);
```

