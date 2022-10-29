# Session 介绍:

(1) Web中什么是会话 ?

 用户开一个浏览器,点击多个超链接,访问服务器多个web资源,然后关闭浏览器,整个过程称之为一个会话.

(2) 什么是Session ?

 Session : 在计算机中,尤其是在网络应用中,称之为"会话控制" .Session 对象存储特定用户会话所需的属性及配置信息.

(3)Session什么时候产生?

  当用户请求来自应用程序的web页是,如果该用户还没有会话,则Web服务器将自动创建一个Session对象.

  这样,当用户在应用程序的Web页之间跳转时,存储在Session对象中的变量将不会丢失,而是在整个用户会话中一直存下去.

 服务器会向客户浏览器发送一个每个用户特有的会话编号sessionID , 让它进入到cookie里.

 服务器同时也把sessionID和对应的用户信息,用户操作记录在服务器上,这些记录就是session.再次访问时会发送cookie给服务器,其中就包括sessionID.

 服务器从cookie里找到sessionID,再根据sessionID找到以前记录的用户信息就可以直到他之前操控,访问过哪里.

  (4) Session的生命周期 :

  一般来说,半小时.举个例子，你登录一个服务器，服务器返回给你一个sessionID，登录成功之后的半小时之内没有对该服务器进行任何HTTP请求，半小时后你进行一次HTTP请求，会提示你重新登录。 

小结:

 Session 是另一个记录客户状态的机制,不同的是Cookie保存在客户端浏览器中,而Session保存在服务器上.客户端浏览器访问服务器的时候,服务器把客户端信息以某种形式记录在服务器上.这就是Session.客户端浏览器再次访问时只需要从Session中查找该用户的状态就可以了.

在Nodejs中如何使用Session ?

利用express - session 插件:

常用参数:

 secret : 一个String类型的字符串,作为服务端生成session的签名.

 name: 返回客户端的key名称,默认为connect.sid,也可以自己设置.

 resave : (是否允许)当前客户端并行发送多个请求时,其中一个请求在另一个请求在另一个请求结束时对session进行修改覆盖并保存. 默认为true . 但是(后续版本)可能默认失效,所以最好手动添加.

  **saveUninitialized**:初始化session时是否保存到存储。默认为true， 但是(后续版本)有可能默认失效，所以最好手动添加。 

      **cookie**:设置返回到前端key的属性，默认值为{ path: '/', httpOnly: true, secure: false, maxAge: null }。 

express-session的一些方法:

 Session.destroy():删除session,当检测到客户端关闭时调用.

 Session.reload(): 当session有修改时,刷新session.

 Session.regenerate() : 将已有session初始化.

 Session.save() :  保存session.

 第一次访问http://localhost:3000/users时,会提示没有登录,访问http://localhost:3000/ 后再访问,则显示用户信息.

app.js:

```
const express = require('express');
const cookieParser = require('cookie-parser');
const session = require('express-session');

const app = express();

app.use(session({
    secret: 'keyboard cat',
    resave: false,
    saveUninitialized: true,
    cookie: { secure: false }
}));

//第一次访问home展示还未登录,通过login登录获取session才会登录
app.use('/home',(req,res,next)=>{
    if(req.session.user){
        var user=req.session.user;
        var name=user.name;
        res.send('你好'+name+'，欢迎登录。');
        next();
    }else{
        res.send('你还没有登录，先登录下再试试！');
        next();
    }
})

//为用户设置session
app.get('/login', function(req, res, next) {
    var user={
          name:"david",
          age:"22"
    }
    req.session.user=user;
    res.send('恭喜您成功获取session!');
    next();
});





app.listen(3000);
```

session作为一种较为安全的会话控制,一般来讲时不允许前端来获取的,所以这里就不演示通过js获取session的方法了.