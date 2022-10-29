Web Socket(套接字)的目标是通过一个长时连接实现与服务器全双工,双向的通信.在js中创建Web Socket时,一个HTTP请求会发送到服务器以初始化连接.服务器响应后,连接使用HTTP的Upgrade头部从HTTP协议切换到Web Socket协议.这意味着Web Socket不能通过标准的HTTP服务器实现,而必须使用支持该协议的专有服务器.

 因为Web Socket使用了自定义协议,所以URL方案(scheme)稍有变化:不能再使用http://或https://,而要使用ws://和wss://.前者是不安全的连接,后者是安全连接.在指定Web Socket URL时,必须包含URL方案,因为将来有可能再支持其他方案.

24.7.1 API:

要创建一个新的Web Socket,就要实例化一个WebSocket对象并传入提供连接的URL:

```js
let socket = new WebSocket("ws://www.example.com/server.php");
```

注意,必须给WebSocket 构造函数掺入一个绝对URL.同源策略不适用于Web Socket,因此可以打开到任意站点的连接.至于是否与来自特定源的页面通信,则完全取决于服务器.

  浏览器会在初始化WebSocket对象之后立即创建连接.与XHR类似,WebSocket也有一个readyState属性表示当前状态.不过,这个值与XHR中相应的值不一样.

WebSocket.OPENING(0):连接正在建立.

WebSocket.OPEN(1):连接已经建立

WebSocket.CLOSING(2):连接正在关闭.

WebSocket.CLOSE(3):连接已经关闭.

WebSocket对象没有readystatechange事件,而是有与上述不同状态对应的其他事件.readystate值从0开始.

任何时候都可以调用close()方法关闭Web Socket连接;

打开Web Socket之后,可以通过连接发送和接收数据.要向服务器发送数据,使用send()方法并传入一个字符串,ArrayBuffer或Blob.

```js
let socket = new WebSocket("ws://www.example.com/server.php");

        socket.send('Hello World!');
```

服务器向客户端发送消息时,WebSocket对象上触发message事件.这个message事件与其他消息协议类似,可以通过event.data属性访问到有效载荷:

````js&#39;
 socket.onmessage = function(event)
        {
            let data = event.data;
        }
````

与通过send()方法发送的数据类似,event.data返回的数据也可能是ArrayBuffer或Blob.这由WebSocket对象的binaryType属性决定.



