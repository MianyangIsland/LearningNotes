# React RouterV6版本的基本使用:

## 一.安装路由:

```js
npm i react-router-dom
```

## 二.路由的基本使用,一共需要用到4个组件:

 (1)`<BrowserRouter>或<HashRouter>`:包裹在需要使用组件路由的最外层 (2)`<Routes>与<Route>`:`<Routes>`与`<Route>`需要配合使用,且必须用`<Routes>`包裹`<Route>`(3)`<Link>`:跳转到某个组件

```js
import ReactDOM from 'react-dom';
import { BrowserRouter, Route, Routes, Link } from 'react-router-dom'
function App() {
  return (
    // 1.最外层必须使用BrowserRouter或者HashRouter包裹,
    <BrowserRouter>
      {/* Link:跳转到home组件 */}
      <Link to='/home'>跳转到Home组件</Link>
      {/* 必须使用Routes包裹route*/}
      <Routes>
        {/* <Route caseSensitive>:匹配时是否区分大小写,默认为false */}
        <Route path='/Home' element={<Home />} />
      </Routes>
    </BrowserRouter>
  );
}
function Home() {
  return (<div>home组件</div>)
}
ReactDOM.render(<App />, document.getElementById('root'))
```

## 三.`<NavLink>`:导航高亮组件 

(1)如果高亮的类名是active,直接写<NavLink to="xx">即可.

(2)如果高亮的类名不是active,则需要使用<NavLink to="xx" className={函数}这种写法

(3)<NavLink end>:当添加上了end属性之后,如果子路由匹配到了高亮,那么父级路由就不再高亮.

```js
import React from 'react';
import './App.css';
import { BrowserRouter, Route, Routes, NavLink } from 'react-router-dom';

export default class App extends React.Component{

  computedClassName=({ isActive })=>{
    return isActive ? 'setActives' : 'normal'
  }
  
  render()
  {
    return(
      <>
        <BrowserRouter>
          <NavLink to={'/login'} className={this.computedClassName}>跳转到Login组件</NavLink>
          <NavLink to={'/home'} className={this.computedClassName}>跳转到Active组件</NavLink>
          <Routes>
             <Route path='/login' element={<Login/>}></Route>
             <Route path='/home' element={<Home/>}></Route>
             <Route path='*' element={<Home/>}></Route>
          </Routes>
        </BrowserRouter>
      </>
    )
  }
}

function Home(){
  return(
    <>
    <div>home组件</div>
    </>
  )
}

function Login(){
  return(
    <>
   <div>Login组件</div>
    </>
  )
}



//在index.css中
.setActives{
  background-color: aqua;
}
.normal{
  background-color: black;
}
```

## 四.<Navigate/>重定向组件

​    (1)语法:<Navigate to='/路径' />

​    (2)<Navigate replace>replace表示跳转模式,默认是false,表示push,如果为true,表示replace.

```js
import ReactDOM from 'react-dom';
import { BrowserRouter, Route, Routes, Navigate } from 'react-router-dom'
function App() {
  return (
    <BrowserRouter>
      <Routes>
        {/* 一开始进入页面路径是'/',我们重定向到/home页面 */}
        <Route path='/' element={<Navigate to='/home' />} />
        <Route path='/home' element={<Home />} />
      </Routes>
    </BrowserRouter>
  );
}
function Home() {
  return <div>home组件</div>
}
ReactDOM.render(<App />, document.getElementById('root'))
```

## 五.路由嵌套:

```js
import ReactDOM from 'react-dom';
import { BrowserRouter, Route, Routes, Link } from 'react-router-dom'
function App() {
  return (
    <BrowserRouter>
      <Routes>
        {/* 父路由后面加/*      */}
        <Route path='/home/*' element={<Home />} />
      </Routes>
    </BrowserRouter>
  );
}
function Home() {
  return <div>
    <p>home组件</p>
    <Link to='news'>去news组件</Link>
    <Routes>
      {/* 子路由直接加名称即可,不需要加/home */}
      <Route path='news' element={<News />} />
    </Routes>
  </div>
}
function News() {
  return <div>news组件</div>
}
ReactDOM.render(<App />, document.getElementById('root'))

```

下面我们学习Router中几个非常重要的钩子函数.

其实在实际开发中,我们如果总是用这种写法:

```js
 <Routes>
 <Route path='/Home' element={<Home />} />
 </Routes>
```

进行开发的话,意味着每次使用Route都要包裹一个Routes是比较麻烦的,因此就有了路由表useRoutes.

## 六.useRoutes():路由表

  (1)语法:const element = useRoutes( [  { path:'路径' ; element:'组件' ; children:'子路由'}  ] )

  (2)在路由区直接使用{element},不需要再反复写<Routes><Route></Route></Routes> .

  (3)在路由组件呈现的位置使用`<Outlet>`组件,进行渲染,相当于vue的`<Router-view>`

```js
import React from 'react';
import './App.css';
import { BrowserRouter, Link, useRoutes, Navigate, Outlet } from 'react-router-dom'

function App(){
  const element = useRoutes([
    {
      path: '/home', element: <Home />, children: [
        {
          path: 'news',
          element: <News />
        }
      ]
    },
    { path: '/', element: <Navigate to='/home' /> },
  ])
  // 在路由区直接使用{element},不需要写`<Routes><Route></Route></Routes>`
  return  <div>{element}</div>
}


function Home() {
  return <div>home组件
    <Link to='news'>跳转到new组件</Link>
    {/* 因为是home的子组件,所以在这里使用<Outlet>组件, */}
    <Outlet />
  </div>
}
function News() {
  return <div>news组件</div>
}


export default App;
```

## 七.useNavigate():编程式导航

语法:  (1) const navigate = useNavigate();  (2)  navigate('/跳转路径',{replace:跳转模式(push或者replace,默认是push),state:{参数}}) (3)  navigate(-1):后退一步,navigate(1):前进一步

```js
import React from "react";
import {  useRoutes, Navigate,useNavigate } from 'react-router-dom'

function App()
{
  const element = useRoutes([
    { path: '/home', element: <Home /> },
    { path: '/news', element: <News /> },
    { path: '/', element: <Navigate to='/home' /> },
])
  return <div>{element}</div>
}

function Home(){
  const navigate = useNavigate();
  function handleClick(){
    navigate('/news');
  }

  return(
    <>
    Home组件
     <button onClick={handleClick}>跳转到new组件</button>
    </>
  )
}

function News(){
  const navigate = useNavigate();
  function handleClick(){
    navigate('/home')
  }
  return (
    <>
    news组件
    <button onClick={handleClick}>跳转到Home组件</button>
    </>
  )
  
}

export default App;
```

## 八.路由组件传递参数

  **1.params参数传参**

 ```js
import ReactDOM from 'react-dom';
import { BrowserRouter, Link, useRoutes, useParams, useMatch } from 'react-router-dom'
function App() {
  const element = useRoutes([
    // 注册路由(声明接受):<Route path='/xxx/xx/:key/:key'/>
    { path: '/home/:id', element: <Home /> },
  ])
  return (
    <div>
      {/* 路由链接(携带参数):<Link to='xx/value/value'></Link> */}
      <Link to='/home/10' >传递id为10</Link>
      {element}
    </div>
  );
}
function Home() {
  // 获取路由参数有两种方法useParams()或者useMatch()
  const { id } = useParams()
  // const {params:{id}}=useMatch({path:'/home/:id'})
  return <div>id是{id}
  </div>
}
ReactDOM.render(<BrowserRouter><App /></BrowserRouter>, document.getElementById('root'))
 ```

**2.search参数传参**

```js
import { BrowserRouter, Link, useRoutes, useSearchParams, useLocation } from 'react-router-dom'
function App() {
  const element = useRoutes([
    // 2.注册路由(无需声明,正确注册即可)
    { path: '/home', element: <Home /> },
  ])
  return (
    <div>
      {/* 1.路由链接(携带参数):<Link to='/xxx/xx?key=value&key=value'></Link> */}
      <Link to='/home?age=100&name=jack' >传递search参数</Link>
      {element}
    </div>
  );
}
function Home() {
  /* 3.接受参数也有两种
        1.通过const [search,setSearch] = useSearchParams() 
          1.调用search.get(key)
          2.setSearch:更新收到的search参数
        2.通过 const {search} = useLocation()
          1.获取到的search是urlencoded编码字符串,需要借助querystring解析
   */
  const [search, setSearch] = useSearchParams()
  const name = search.get('name')
  const age = search.get('age')
  // const {search} = useLocation()
  return <div>名字是{name}年龄是{age}
  </div>
}
ReactDOM.render(<BrowserRouter><App /></BrowserRouter>, document.getElementById('root'))
```

**3.state传参**

```js
import ReactDOM from 'react-dom';
import { Link, useRoutes, useLocation, Outlet } from 'react-router-dom'
function App() {
  // 2.注册路由(无需声明,正确注册即可)
  const element = useRoutes([
    { path: '/home', element: <Home /> },
  ])
  return (
    <div>
      {/* 1.路由链接(携带参数):<Link to='/xx' state={{key:value}}></Link> */}
      <Link to='/home' state={{ age: 10, name: 'jack' }} >state参数传参</Link>
      {element}
    </div>
  );
}
function Home() {
  // 3.接受参数:const {state} = useLocation()
  const { state } = useLocation()
  return <div>名字{state.name}年龄{state.age}
    <Outlet />{/*渲染匹配任何子路由*/}
  </div>
}
export default App;
```

这些就是reactRouterV6的大致用法,上述仅为个人理解,如果有错误,还请大佬斧正!