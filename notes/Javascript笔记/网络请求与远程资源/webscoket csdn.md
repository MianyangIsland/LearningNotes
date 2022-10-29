1.websocket:

由于http请求只能由客户端发起,所有当服务器资源变化时,客户端只能通过轮询的方式.非常浪费资源.

websocket的特点:

 服务器可以主动向客户端推送消息,客户端也可以主动向服务器发送信息,是真正的双向平等对话,属于服务器推送技术的一种.

建立在TCP协议之上,服务器端的实现比较容易.

可以发送文本,也可以发送二进制数据.

没有同源限制,客户端可以与任意服务器通信.

协议标识符是ws(如果加密,则为wss),服务器网址就是URL.如:wslocalhost:8080/msg.

2.api:

1.websocket构造函数: 用于创建一个webSocket实例,执行后,客户端就会与服务器连接.

```js
var ws = new WebSocket("ws://localhost:8080/msg");
```

2.webSocket.readyState :

readyState属性返回实例对象的当前状态,共有四种:

CONNECTING:值为0,表示正在连接.

OPEN:值为1,表示连接成功,可以通信了.

CLOSING:值为2,表示连接正在关闭.

CLOSED:值为3,表示连接已经关闭,或者打开连接失败.

eg: if(ws.readyState == WebSocket.CONNECTING){};

3.webSocket.onopen :用于指定连接成功后的回调函数.

```js
ws.onopen = function()
        {
            //函数体
        }
或者
ws.addEventListener('open',function(event){
            //函数体
        })
```

4.webSocket.onclose:用于指定连接关闭后的回调函数.

```js
ws.onclose = function()
        {
            var code = event.code;
            var reason = event.reason;
            var wasClean = event.wasClean;
        }

        ws.addEventListener('close',function(event){
            var code = event.code;
            var reason = event.reason;
            var wasClean = event.wasClean;
        })
```

5.webSocket.onmessage: 用于指定收到服务器数据后的回调函数:

```js
ws.onmessage = function(event)
        {
          var data = event.data;
        }

        ws.addEventListener('message',function(event){
            var data = event.data;
        })
```

服务器数据有可能是文本，也有可能是二进制数据，需要判断 

```js
ws.onmessage = function(event){
  if(typeof event.data === String) {
    console.log("Received data string");
  }
 
  if(event.data instanceof ArrayBuffer){
    var buffer = event.data;
    console.log("Received arraybuffer");
  }
}
```

除了动态判断收到的数据类型，也可以使用binaryType属性，显式指定收到的二进制数据类型 

```js
// 收到的是 blob 数据
ws.binaryType = "blob";
ws.onmessage = function(e) {
  console.log(e.data.size);
};
 
// 收到的是 ArrayBuffer 数据
ws.binaryType = "arraybuffer";
ws.onmessage = function(e) {
  console.log(e.data.byteLength);
};
```

6.webSocket.send(): send方法用于向服务器发送数据.

发送文本:

```js
ws.send('Hello WebSocket!');
```

发送Blob数据:

```js
var file = document.querySelector('input[type="file"]').files[0];
       ws.send(fs);
```

发送ArrayBuffer:

```js
var img = canvas_context.getImageData(0, 0, 400, 320);
var binary = new Uint8Array(img.data.length);
for (var i = 0; i < img.data.length; i++) {
  binary[i] = img.data[i];
}
ws.send(binary.buffer);
```

7.webSocket.bufferedAmount: bufferedAmount属性,表示还要多少字节的二进制没有发送出去.它可以用来判断发送是否结束.

```js
var data = new ArrayBuffer(10000000);
socket.send(data);
 
if (socket.bufferedAmount === 0) {
  // 发送完毕
} else {
  // 发送还没结束
}
```

8.webSocket.onerror :用于指定报错是的回调函数:

```js
ws.onerror = function(event) {
};
 
es.addEventListener("error", function(event) {
});
```

![1649769395636](C:\Users\22719\AppData\Local\Temp\1649769395636.png)