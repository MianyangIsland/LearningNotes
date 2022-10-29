XHR为发送服务器请求和获取响应提供了合理的接口,这个接口可以实现异步从服务器获取额外数据意味着用户点击不用刷新页面也可以获取数据.

24.1 XMLHttpRequest对象.

所有浏览器都通过XMLHttpRequest构造函数原生支持XHR对象.

```js
let xhr = new XMLHttpReauest()
```

241.1 使用XHR:

使用XHR对象首先调用open()方法,这个方法接收3个参数:请求类型("get","post"等),请求url,以及表示请求是否异步的布尔值.

```js
xhr.open("get","example.php",false)
```

调用open不会实际发送请求,只是为发送请求做好准备.

注意:只能访问同源url,也就是域名相同,端口相同,协议相同.如果请求的URL与发送请求的页面在任何地方有所不同,就会抛出错误.

要发送定义好的请求,必须调用send()方法:

```js
xhr.open('get','123.com',true);
      xhr.send();
```

send方法接收一个参数,是作为请求体发送的数据.如果不需要发送请求体,则必须传null.调用sned()后,请求就会发送大服务器.

如果请求是同步的,那么js代码会等待服务器响应之后在继续执行.收到响应后,XHR对象的以下属性会被填充上数据.

responseText:作为响应体返回的文本.

responseXML;如果响应的内容类型是"text/xml"或"applocation/xml",那就是包含响应数据的XML DOM文档.

status:响应的HTTP状态.

statusText:响应的HTTP状态描述.

收到响应之后,第一步要检查status属性以确保响应成功返回.一般来说,HTTP状态码2xx表示成功,此时,responseText或preponseXML属性中会有内容.如果HTTP状态码是304,则表示资源未修改过,是从浏览器缓存中直接拿取的.当然这也意味着响应有效.

```js
let xhr = new XMLHttpRequest();
      xhr.open('get','123.com',true);
      xhr.send(null);
      if((xhr.status>=200 && xhr.status<300)|| xhr.status==304)
      {
          console.log(xhr.responseText);
      }
      else
      {
          alert("Request was unsucessful :"+ xhr.status);
      }
```

以上代码可能显示返回的内容,也可能显示错误消息,取决于HTTP响应的状态码.为确定下一步执行什么操作,最好检查status而不是statusText属性,因为后者已经被证明在跨浏览器的情况下不可靠.

虽然可以像前面的例子一样发送同步请求,但多数情况下最好使用异步请求,这样可以不阻塞js代码继续执行.XHR对象有一个readyState属性,表示当前在请求过程中的哪个阶段.

这个属性有如下可能的值:

0:未初始化,尚未调用open()方法.

1:已打开(open).已调用open()方法,尚未调用sned()方法.

2:以发送(sent):已调用send()方法,尚未收到响应.

3:接收中.已经收到部分响应.

4:完成,已近收到所有响应可以使用了.

每次readyState从一个值变成另一个值,都会触发readyStatechange事件.可以借此机会检查readyState的值.一般来说,我们唯一关心的redyState值是4,表示数据已就绪.为保证跨浏览器兼容,onreadystatechange事件处理程序应该在调用open()之前赋值.

```js
let xhr = new XMLHttpRequest();
      xhr.onreadystatechange=function()
      {
          if(xhr.readyState==4)
          {
              if((xhr.status >=200 && xhr.status<300) || xhr.status==304)
              {
                  alert(xhr.responseText);
              }
              else
              {
                  alert('Request was unsuccessful:'+xhr.status);
              }
          }
      }
      xhr.open('get','example.txt',true);
      xhr.send(null);
```

与其他事件处理程序不同,onreadystatechange事件处理不会收到event对象.在事件处理程序中,必须使用XHR对象本身来确定接下来该作什么.

在收到响应之前如果想取消异步请求,可以调用abort()放发.

```js
xhr.abort();
```

调用这个方法之后,XHR对象会停止触发事件,并阻止访问这个对象上任何与响应相关的属性.中断请求后,应该取消对XHR对象的引用.由于内存问题,不推荐重用XHR对象.

24.1.2 HTTP头部:

每个HTTP请求和响应都会携带一些头部字段,这些字段可能对开发者有用.XHR对象会通过一些方法暴露与请求和响应相关的头部字段.

默认情况下,XHR请求会发送以下头部字段:

P714.

如果需要发送的额外的头部请求,可以使用setRequestHeader()方法.这个方法接收两个参数:头部字段的名称和值.

为保证请求头部被发送,必须在open()之后,sned()之前调用setRequestHeader(),

24.1.3 GET请求:

最常用的请求方法是GET请求,用于向服务器查询某些信息.必要时,需要在GET请求的URL后面添加查询的字符串参数,对XHR而言,查询字符串必须正确编码后添加到URL后面,然后再传给opne()方法.

发送GET请求最常见的一个错误是查询字符串格式不对.查询字符串中每个名和值都必须使用encodeURLComponent()编码,所有名/值对都必须以和号(&)分割.

可以使用以下参数将查询字符串参数添加到现有的URL末尾:

```js
function addURLParam(url,name,value){
          url += (url.indexOf("?"))== -1 ? "?" : "&";
          url += encodeURIComponent(name) + "=" + encodeURIComponent(value);
          return url;
      }
```

24.1.4 POST请求:

每个POST请求都应该在请求体中携带提交的数据,而GET请求则不然.POST请求的请求体可以包含非常多的数据,而且数据可以是任意格式.要初始化POST请求,open方法的第一个参数要传"post",

默认情况下,对服务而言,POST请求与提交表单是不一样的.服务器逻辑需要读取原始POST数据才能取得浏览器发送的数据.不过可以使用XHR模拟表单提交.为此,第一步需要把Content-Type头部设置为"application/x-formurlencoded".这是提交表单时使用的内容类型.第二步是创建对应格式的字符串.POST数据此时使用与查询字符串相同的格式,如果网页中确实有一个表单需要序列化并通过XHR发送到服务器,则可以使用第十四章的serialize()函数来创建相应的字符串.

1.FormDate类型:

FormDate类型便于表单序列化,也便于创建与表单类似格式的数据然后通过XHR发送.

```js
let data = new FormData();
data.append("name","Nicholas");
```

append()方法接收两个参数:键和值,相当于表达字段的名称和该字段的值.可以像这样添加任意多个键/值对数据.此外,通过直接给FormDate构造函数传入一个表单元素,也可以将表单中二点数据作为键/值对填充进去.

```js
let form = document.getElementById('user-info');
       xhr.send(new FormData(form));
```



2.超时:

timeout属性,用于表示发送请求后等待多少毫秒,如果响应不成功就中断请求.在给timeout属性设置了一个时间且在该时间过后没有收到响应时,XHR对象就会触发timeout属性.

```js
let xhr = new XMLHttpRequest();
       xhr.onreadystatechange = function()
       {
           if(xhr.readyState ==4)
           {
               try{
                   if((xhr.status>=200&&xhr.status<300)|| xhr.status ==304)
                   {
                      alert(xhr.responseText);
                   }
                   else{
                      alert("Request was unsuccessful:"+xhr.status);
                   }
               }catch(ex)
               {
                   //假设由ontimeout处理.
               }
           }
       }

       xhr.open("get","timeour.php",true);
	  xhr.timeout = 1000
       xhr.ontimeout = function()
       {
           alert('Request did not return in a second');
       }
       xhr.send(null);
```

24.2 进度事件:

24.2.1 load事件:

load事件用于替代readystatechange事件.load事件在响应接收完成之后立即触发,这样就不用检查readyState属性,onload事件处理程序会接收到一个event对象,其target属性设置为XHR实例,在这个实例上可以访问所有XHR对象属性和方法.

```js
let xhr = new XMLHttpRequest();
       xhr.onload = function()
       {
           if((xhr.status>=200 && xhr.status <300)|| xhr.status == 304)
           {
               alert(xhr.responseText);
           }
           else
           {
               alert('Request was unsuccessful:'+ xhr.status)
           }
       };
       xhr.open('get','a;tevents.txt',true);
       xhr.send(null);
```

24.3 跨域资源共享:

 通过XHR进行Ajax通信的一个主要限制是跨域安全策略.默认情况下,XHR只能访问与发起请求的页面在同一个域内的资源.这个安全限制可以防止某些恶意行为.不过,浏览器也需要支持合法跨源访问的能力.

跨域资源共享定义了浏览器与服务器如何实现跨域通信,CORS背后的基本思路就是使用自定义的HTTP头部允许浏览器和服务器相互了解,以确实请求应该成功还是失败.

对于简单的请求,比如GET或POST请求,没有自定义头部,而且请求体是text/plain类型,这样的请求在发送时会有一个额外的头部叫Origin.Origin头部包含发送请求的页面的源(协议,域名和端口).以便服务器确定是否为其提供响应.下面是Origin头部的一个实例:

```js
Origin:http://www/nczonline.net
```

如果服务器决定响应请求,那么应该发送Access-Control-Allow-Origin头部,包含相同的源;或者如果资源是公开的,那么就包含"*".

eg:

```js
 Access-Control-Allow-Origin:http://www/nczonline.net
```

如果没有这个头部或者有但源不匹配,则表明不会响应浏览器请求.否则,服务器就会处理这个请求.注意,无论请求还是响应都不会包含cookie信息.

现代浏览器通过XMLHttpRequest对象原生支持CROS.在尝试访问不同源的资源时,这个行为会被自动触发.要向不同域的源发送请求,可以使用XHR对象并给open()方法传入一个绝对的URL.

跨域XHR对象允许访问status和statusText属性,也允许同步请求.出于安全考虑,跨域XHR对象也施加了一些额外限制.

不能使用setRequestHeader()设置自定义头部.

不能发送和接收cookie.

getAlResponseHeader()方法始终返回空字符串.

因为无论同域还是跨域请求都使用同一个接口,所以最好在访问本地资源时使用相对URL,在访问远程资源时使用绝对URL.

24.3.1预检请求:

CORS通过一种叫预检请求的服务器验证机制,允许使用自定义头部,除GET,POST之外的方法,以及不同请求体内容类型.在要发送涉及上述某种高级选项的请求时,会先向服务器发送一个预检请求.这个请求使用OPTIONS方法发送并包含以下头部.

Origin:与简单请求相同. 

Access-Control-Request-Method:请求希望使用的方法.

Access-Control-Request-Header:(可选)要使用的逗号分隔的自定义头部列表.

在这个请求发送后,服务器可以确定是否允许这种类型的请求.服务器会通过在响应中发送如下头部与浏览器沟通这些信息.

Access-Control-Allow-Origin:与简单请求相同.

Access-Control-Allow-Methods:允许的方法(逗号分隔的列表).

Access-control-Allow-Headers:服务器允许的头部(逗号分隔的列表).

Access-Control-Max-Age:缓存预检请求的秒数.

24.3.2 凭据请求:

默认情况下,跨域请求不提供(cookie,HTTP认证及SSL证书).可以通过将withCredentials属性设置为true来表明请求会发送凭据.如果服务器允许带凭据的请求,那么可以在响应中包含HTTP头部:

Access-Control-Allow-Credentials: true.

如果发送了凭据请求而服务器返回的响应中没有这个头部,则浏览器不会把响应交给JS.注意,服务器也可以在预检请求的响应中发送这个HTTP头部,以表明这个源允许发送凭据的请求.