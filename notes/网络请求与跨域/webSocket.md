# WebSocket

WebSocket的目标是通过一个长时连接实现与服务器全双工,双向的通信.在JavaScript中创建WebSocket时,一个HTTP请求会发送到服务器以初始化连接.服务器响应后,连接使用HTTP的Upgrade头部从HTTP协议切换到WebSocket协议.这意味着WebSocket不能通过标准HTTP服务器实现,而必须使用支持该协议的专有服务器.

   因为WebSocket使用了自定义协议,所以URL方案稍有变化:不能再使用http://或https://,而要使用ws://或则wss:// ,前者是不安全连接,后者是安全连接.在指定WebSocket URL时,必须包含URL方案,因为将来有可能使再支持其他方案.

​    使用自定义协议而非HTTP协议的好处是,客户端与服务器之间可以发送非常少的数据,不会对HTTP造成任何负担.使用更小的数据包让WebSocket非常适合带宽和延迟问题比较明显的移动应用.使用自定义协议的缺点是,定义协议的时间比定义JavaScript API时间要长,Websocket得到了主流浏览器的支持.

## 1.API

要创建一个新的WebSocket,就要实例化一个WebSocket对象并传入提供连接的URL.

```js
let socket = new WebSocket('ws://www.example.com/server.php');
```

注意,必须给WebSocket构造函数传入一个绝对URL.同源策略不适用于WebSocket,因此可以打开到任意站点的连接.至于是否与来自特定源的页面通信,则完全取决于服务器.(在握手阶段就可以确定请求来自哪里).

浏览器会在初始化WebSocket对象之后立即创建连接.与XHR类似,WebSocket也有一个readyState属性表示当前状态.

```js
WebSocket.OPENING(0) :连接正在建立
WebSocket.OPEN(1) : 连接已经建立
WebSocket.CLOSING(2) : 连接正在关闭
WebSocket.CLOSE(3) : 连接已经关闭
```

WebScoket对象没有readystateChange事件,而是有与上述不同状态对应的其他事件.readyState值从0开始.

任何时候都可以调用close()方法关闭WebSocket连接:

```js
socket.close();
```

调用close()之后,readyState立即变为2(连接正在关闭),并会在关闭之后变为3(连接已经关闭).

## 2.发送和接收数据

打开WebSocket之后,可以通过连接发送和接收数据.要向服务器发送数据,使用send()方法并传入一个字符串,ArryBuffer或Blob.

```js
let socket = new WebSocket(url);
let stringData = "Hello World";
socket.send(stringData)
```

服务器向客户端发送消息时,WebSocket对象上会触发message事件.这个message事件与其它消息协议类似,可以通过event.data属性访问到其有效载荷:

```js
socket.message = function(event){
         let data = event.data;
         //进行操作
       }
```

返回数据的类型可以有WebSocket对象的binaryType属性决定.

## 3.其他事件

WebSocket对象在连接生命周期中有可能触发3个其他事件.

```js
open: 在连接成功建立时触发
error : 在发生错误时触发.连接无法存续.
close : 在连接关闭时触发
```

WebSocket对象不支持DOM Level2 事件监听器,因此需要使用DOM Level0风格的事件处理程序来监听这些事件:

```js
let socket = new WebSocket(url);
    socket.onopen = function(){
        console.log('连接已经建立');
    }
    socket.onerror = function(){
        console.log('连接发生错误');
    }
    socket.onclose = function(){
        console.log('连接已经关闭');
    }
```

在这些事件中,只由close事件的event对象上有额外的信息.这个对象上有3个额外的属性:

wasClean,code和reason.其中,wasClean是一个布尔值,表示连接是否干净的关闭;code是一个来自服务器的数值状态码;reason是一个字符串,包含服务器发来的信息.可以将这些信息显示给用户或记录到日志.

ok, 那么接下来让我们自己封装一个webSocket函数:

这是我在React项目中封装过的.

```js
let websocket, lockReconnect = false;
let createWebSocket = (url) => {
    websocket = new WebSocket(url);
    websocket.onopen = function () {
       heartCheck.reset().start();
    }
    websocket.onerror = function () {
        reconnect(url);
    };
    websocket.onclose = function (e) {
        console.log('websocket 断开: ' + e.code + ' ' + e.reason + ' ' + e.wasClean)
    }
    websocket.onmessage = function (event) {
        lockReconnect=true;
        //event 为服务端传输的消息，在这里可以处理
    }
}
let reconnect = (url) => {
    if (lockReconnect) return;
    //没连接上会一直重连，设置延迟避免请求过多
    setTimeout(function () {
        createWebSocket(url);
        lockReconnect = false;
    }, 4000);
}
let heartCheck = {
    timeout: 60000, //60秒
    timeoutObj: null,
    reset: function () {
        clearInterval(this.timeoutObj);
        return this;
    },
    start: function () {
        this.timeoutObj = setInterval(function () {
            //这里发送一个心跳，后端收到后，返回一个心跳消息，
            //onmessage拿到返回的心跳就说明连接正常
            websocket.send("HeartBeat");
        }, this.timeout)
    }
}
//关闭连接
let closeWebSocket=()=> {
    websocket && websocket.close();
}
export {
    websocket,
    createWebSocket,
    closeWebSocket
};
```

下面附一些关于WebSocket的面试题:

## WebSocket（为了实现客户端、浏览器实时与服务器通信）

是HTML5的一种新的协议，Websocket是真正实现了全双工通信的服务器向客户端的互联网技术，是单个TCP连接上进行全双工通信协议

## 全双工通讯传输协议

允许数据在两个方向上同时传输 。双向传输的意思

半双工：可以双向传输，但是同一时刻只能一个方向传输
半工：单向传输数据

## WebSocket与Socket的区别

WebSocket拥有完整的应用层协议，包含一套标准的API
Socket是一组接口，是应用层与TCP/IP协议通信的中间软件抽象层，

## HTTP与WebSocket区别

http是短连接，请求之后会关闭连接。
WebSocket长连接，只需通过一次请求初始化连接，然后所有的请求和响应都是通过这个TCP连接进行通信。

## WebSocket特点

与http协议有良好的兼容性
建立在TCP协议之上，与http同属于'应用层
数据量小、性能开销小、通信高效
可以发送文本和二进制
可以与任意服务器通信
握手阶段采用http协议，默认端口是80和443
协议标识字符ws、加密wss
服务器可以主动向客户端请求
