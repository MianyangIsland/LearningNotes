t一.SPA的理解:

​    1.单页web应用.   2.整个应用只有一个完整的页面.   3.点击页面中的链接不会刷新页面,只会做页面的局部更新.

  4.数据都需要通过ajax请求获取,并在前端异步实现.

二.路由的理解：

  1.什么是路由：

         1. 一个路由就是一个映射关系.

   2.key 为路径,value可能是function或component.

 2.路由的分类：

​     1.后台路由：

​     1）理解：value是function,用来处理客户端的请求.

​      2) 注册路由： router.get(path,function(req,res)).

​      3) 工作过程：当node 接收到一个请求时,根据请求路径找到匹配的路由，调用路由中的函数来处理请求，返回响应数据.

​     2.前端路由：

​     1)  浏览器端路由,value是component,用于展示页面内容.

​     2)   注册路由：<Route path="/test" component={Test}>

​      3) 工作过程：当浏览器的pathj变为/test时,当前路由组件就会变为Test组件.

3.路由的基本使用：

​     1.明确好界面中的导航区，展示区.

​     2.导航区的a标签改为Link标签.

​                     <Linkl to="/xxxx">Demo</Link>

​     3.展示区写Route标签进行路径的匹配.

​               <Route path="/xxxxx"  component={Demo}/>

​    4.<App>的最外侧包裹了一个<BrowserRouter>或<HashRouter>

三.关于MyNavLink的封装

   ```javascript
1.封装的一般组件：
import React,{Component} from "react";
import {NavLink} from 'react-router-dom';

export default class MyNavLink extends Component{
    render()
    {
        return(
            <NavLink className="link-item" {...this.props}/>
        )
    }
}

2.使用时：
  <MyNavLink   to="/about" children="About"/>
  <MyNavLink   to="/home"  children="Home"/>
  注意：利用children这个标签属性
   ```

NavLink标签可以实现路由链接的高亮,通过activeClassName指定样式名.

四.Switch的使用:

​    1.通常情况下，path和component是一一对应的关系.

​    2.Switch可以提高路由匹配效率（单一匹配）.

```javascript
 <Switch>
                   <MyNavLink   to="/about" children="About"/>
                   <MyNavLink   to="/home"  children="Home"/>
                   <MyNavLink   to="/home"  children="Test"/>
 </Switch>
```



五.解决多级路径刷新页面样式丢失问题:( public里面的index.html就是最后路径的最后一层.react脚手架只找到这一层)

​     1.public/index.html 中  引入样式时不写 ./   写   / (常用).

​     2.public/index.html 中   引入样式时不写  ./     写   %PUBLIC_URL%(常用)

​     3.使用HashRputer  :  #后面的都视作前端资源,不向后台请求.

  六：路由的严格匹配和模糊匹配：

​     1.默认使用的是模糊匹配,（简单记：输入的路径必须要包含匹配的路径，且顺序要一致）。

​     2.开启严格匹配：

       ```javascript
 <Route exact={true} path="/about" element={<About></About>}/>
  <Route exact path="/home" element={<Home></Home>}/>
       ```

   3.严格匹配不要随便开启，需要再开，有些时候开启会导致无法继续匹配二级路由.

 七.Redirect的使用：

​    1.一般在所有路由注册的最下方，当所有路由都无法匹配时,跳转到Redirect指定的路由.

​    2.具体编码：

   ```javascript
    <Switch>

              <Route exact={true} path="/about" element={<About></About>}/>

                  <Route exact path="/home" element={<Home></Home>}/>

                  <Redirect to="/home"></Redirect>

                

      </Switch>
   ```

但在React6.0已经不再支持Redirect了.

  解决办法如下：  使用Navigate.

```javascript
 <Route path="*" element={<Navigate to ="/about" />}/>
```

八.嵌套路由：（自己查阅文档,网课方法过时）



​     1.注册子路由时要写上父路由的path值

​      2.路由的匹配是按照注册路由的顺序进行的.