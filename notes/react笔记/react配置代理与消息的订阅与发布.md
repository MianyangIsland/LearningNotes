一·第一种配置代理解决跨域的问题.(配置一个代理)

在package.json里面写上:

   ```javascript
"proxy":"http://localhost:5000"
   ```

脚手架就会配置一个代理端口，注意：这个端口还是在3000上开启的，客户端发送请求之后，首先会在3000的public里面找，如果没有，就会向5000发送请求，5000将数据返回.通过配置代理解决跨域问题.

二.配置多个代理：

通过middleware中间件的方式设置proxy.

1.首先在项目中安装middleware

```javascript
npm install http-proxy-middleware --save
```



安装完成后，在src文件目录下新建setupProxy.js文件（注意：webpack会自动照这个文件，所以文件名不能改变）,在文件中写入以下代码：

```javascript
const { createProxyMiddleware } = require('http-proxy-middleware')

module.exports = function (app) {
  app.use(createProxyMiddleware('/api', {
    target: 'http://172.16.136.249:8080',//访问的目标端口
    secure: false,
    changeOrigin: true,//控制服务器收到的响应头中Host字段的值
    pathRewrite: {//重写请求路径(必须)
      "^/api": "/api"
    }
  }))
}

```



 消息订阅与发布机制： ---------    subscribe和publish的介绍：

​    消息发布与订阅是用来解决我们组件之间的一个通信方式,简单理解为事件（发布：通过相同名字找到订阅的函数）.

   引入第三方库PubSunJs,就能直接在触发事件的组件中发布消息，监听组件中订阅消息来实现兄弟组件的通信.

   一.使用方法：

​     1.下载：  

```javascript
 npm install pubsub-js --save
```

   2.使用：

```javascript
// 创建订阅的回调函数
var mySubscriber = function (msg, data) {
    //msg表示发送来消息的名称,data表示发送的数据
    console.log(msg, data);
};

// 订阅
var token = PubSub.subscribe('MY TOPIC', mySubscriber);

// 发布
PubSub.publish('MY TOPIC', 'hello world!');

// 取消指定订阅
PubSub.unsubscribe(token);
// 取消一个函数的所有订阅
PubSub.unsubscribe(mySubscriber);



```

注意：

   1.先订阅，在发布.

   2.适用于任意组件的通信.

   3.要在组件的componentWillUnmount中取消订阅.