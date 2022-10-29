1.父组件注册路由: v5版本： 

```javascript
import { Route } from 'react-router-dom'
<Route path="/about" component={About} />
<Route path="/home" component={Home} />

```

v6版本： 

```javascript
import { Route } from 'react-router-dom'
<Route path="about" element={<About />} />
<Route path="home" element={<Home />} />

```

2.NavLink点击高亮显示 v5版本： 

```javascript
import { NavLink } from 'react-router-dom'
<NavLink activeClassName="highlight" className="about"  to="/about" >About</NavLink>

```

v6版本： 

```javascript
以下二者选一即可，一定要注意 （isActive ? " highlight"） highlight前面的空格！！！
// 官方写法
<NavLink className={({ isActive }) => "about" + (isActive ? " highlight" : "")} to="about">About</NavLink>
// ES6 模版字符串写法 
<NavLink className={({ isActive }) => `about ${isActive ? "highlight" : ""}`} to="about">About</NavLink>

```

3.Switch（单一匹配）更换为Routes 

```javascript
import { Routes,Route } from 'react-router-dom'
<Routes>
	<Route path="about" element={<About />} />
	<Route path="home" element={<Home />} />
</Routes>

```

4.Redirect重定向 v5版本： 

```javascript
import ｛ Redirect ｝from 'react-router-dom'
<Routes>
	<Route path="/about" element={<About />} />
	<Route path="/home" element={<Home />} />
	// 写在这里
	<Redirect to="/about" />
</Routes>

```

v6版本： 

```javascript
import ｛ Navigate ｝from 'react-router-dom'
<Routes>
	<Route path="about" element={<About />} />
	<Route path="home" element={<Home />} />
	// 写在这里
	<Route path="/" element={<Navigate to="about" />} />
</Routes>

```

5.嵌套路由 v5版本: 

```javascript
// 父组件
<Route path="home/" element={<Home />} />
// 路由链接
<NavLink  className="about"  to="/home/news" >News</NavLink>
// 注册路由和重定向
<Switch>
	<NavLink  className="news"  to="/home/news" >News</NavLink>
	<NavLink  className="message"  to="/home/message" >Message</NavLink>
	<Redirect to="/home/news" />
</Switch>

```

v6版本： 

```javascript
// 父组件
<Route path="home/*" element={<Home />}>
// 路由链接
<NavLink  className="news"  to="news" >News</NavLink>
<NavLink  className="message"  to="news" >News</NavLink>
// 注册路由和重定向
<Routes>
	<Route path="news" element={<News />} />
	<Route path="message" element={<Message />} />
	<Route path="/" element={<Navigate to="news" />} />
</Routes>

```

6.params参数传递 v5版本： 

```javascript
//路由链接(携带参数)：
<Link to='/demo/test/tom/18'}>详情</Link> 
//或 <Link to={{ pathname:'/demo/test/tom/18' }}>详情</Link>

//注册路由(声明接收)：
<Route path="/demo/test/:name/:age" component={Test}/>
    
//接收参数：
this.props.match.params

```

v6版本： 

```javascript
//路由链接(携带参数)：
<Link to={{ pathname:`/b/child1/${id}/${title}` }}>Child1</Link>
//或 <Link  to={`/b/child1/${id}/${title}`}>Child1</Link> 

//注册路由(声明接收)：
<Route path="/b/child1/:id/:title" element={<Test/ >} />
    
//接收参数：接收参数的组件一定要是函数式声明的（class不可以）！！！
import { useParams } from "react-router-dom";
const params = useParams();
//params参数 => {id: "01", title: "消息1"}

```

7.search参数 v5版本： 

```javascript
//路由链接(携带参数)：
<Link to='/demo/test?name=tom&age=18'}>详情</Link>

//注册路由(无需声明，正常注册即可)：
<Route path="/demo/test" component={Test}/>
        
//接收参数：
this.props.location.search

//备注：获取到的search是urlencoded编码字符串(例如: ?id=10&name=zhangsan)，需要借助query-string解析参数成对象

```

v6版本： 

```javascript
//路由链接(携带参数)：
 <Link className="nav" to={`/b/child2?age=20&name=zhangsan`}>Child2</Link>

//注册路由(无需声明，正常注册即可)：
<Route path="/b/child2" component={Test}/>
        
//接收参数方法1：接收参数的组件一定要是函数式声明的（class不可以）！！！
import { useLocation } from "react-router-dom";
import { qs } from "url-parse";
const { search } = useLocation();
const searchs = qs.parse(search)
//search参数 => {age: "20", name: "zhangsan"}

//接收参数方法2：接收参数的组件一定要是函数式声明的（class不可以）！！！
import { useSearchParams } from "react-router-dom";
const [searchParams, setSearchParams] = useSearchParams();
// console.log( searchParams.get("id")); // 12

//备注：获取到的search是urlencoded编码字符串(例如: ?age=20&name=zhangsan)，需要借助 url-parse 里面的 qs.parse 解析参数成对象

```

8.state参数 v5版本： 

```javascript
//路由链接(携带参数)：
<Link to={{pathname:'/demo/test',state:{name:'tom',age:18}}}>详情</Link>

//注册路由(无需声明，正常注册即可)：
 <Route path="/demo/test" component={Test}/>
    
//接收参数：
this.props.location.state

//备注：刷新也可以保留住参数

```

v6版本： 

```javascript
//通过Link的state属性传递参数
 <Link
     className="nav"
     to={`/b/child2`}
     state={{ id: 999, name: "i love merlin" }} 
 >
    Child2
</Link>

//注册路由(无需声明，正常注册即可)：
<Route path="/b/child2" component={Test}/>
    
//接收参数：接收参数的组件一定要是函数式声明的（class不可以）！！！
import { useLocation } from "react-router-dom";
const { state } = useLocation();
const { xx, xx } = state || {} 
//state参数 => {id: 999, name: "我是梅琳"}

//备注：刷新也可以保留住参数

```

9.编程式路由跳转 **v5版本** v5版本：push跳转携带params参数 :

```javascript
 props.history.push(`/b/child1/${id}/${title}`)

```

v5版本：push跳转携带search参数 

```javascript
props.history.push(`/b/child1?id=${id}&title=${title}`);
```

v5版本：push跳转携带state参数 

```javascript
props.history.push(`/b/child1`, { id, title });
```

v5版本：前进 

```javascript
this.props.history.goForward();
```

v5版本：后退 

```javascript
this.props.history.goBack();
```

v5版本：前进或回退 ( go ) 

```javascript
this.props.history.go(-2); //回退到前2条的路由
```

v5版本：replace跳转携带params参数 ：

```javascript
this.props.history.replace(`/home/message/detail/${id}/${title}`)
```

v5版本：replace跳转携带search参数 

```javascript
this.props.history.replace(`/home/message/detail?id=${id}&title=${title}`)
```

v5版本：replace跳转携带state参数 :

```javascript
this.props.history.replace(`/home/message/detail`, { id, title });
```

v5版本：在一般组件中使用编程式路由导航 (非路由组件) :

```javascript
import {withRouter} from 'react-router-dom'

class Header extends Component {
    // withRouter(Header)后，就可以在一般组件内部使用 this.props.history 
    //...
}

export default withRouter(Header)

```

**v6版本** v6版本： :

```javascript
// v6版本编程导航使用 useNavigate
import {  useNavigate } from "react-router-dom";
export default function A() {
  const navigate = useNavigate();
  //...
}

```

v6版本：push跳转携带params参数 :

```javascript
navigate(`/b/child1/${id}/${title}`);
```

v6版本：push跳转携带search参数 :

```javascript
navigate(`/b/child2?id=${id}&title=${title}`);
```

v6版本：push跳转携带state参数 :

```javascript
navigate("/b/child2", { state: { id, title }});
```

v6版本：前进 :

```javascript
<button onClick={() => navigate(1)}>Go Forword</button>
```

v6版本：后退 :

```javascript
<button onClick={() => navigate(-1)}>Go back</button>
```

v6版本：前进或后退几步 :

```javascript
<button onClick={() => navigate(2)}>Go</button>
```

v6版本：replace跳转携带params参数 :

```javascript
navigate(`/b/child1/${id}/${title}`,{replace: true});
```

v6版本：replace跳转携带search参数 :

```javascript
navigate(`/b/child2?id=${id}&title=${title}`,{replace: true});
```

v6版本：replace跳转携带state参数 

```javascript
navigate("/b/child2", { state: { id, title },replace: true});
```

10.函数组件使用生命周期需要用 useEffect :

```javascript
import React, { useEffect } from 'react'
import { useNavigate } from 'react-router-dom'

// 这里的作用是 componentDidMount 
export default function News() {
    const navigate = useNavigate()
    useEffect(() => {
        setTimeout(() => {
            navigate("/home/message")
        }, 2000)
    })
    return (
        <ul>
            <li>news001</li>
            <li>news002</li>
            <li>news003</li>
        </ul>
    )
}

```

11.withRouter（v6版本弃用）直接使用 useNavigate 即可 