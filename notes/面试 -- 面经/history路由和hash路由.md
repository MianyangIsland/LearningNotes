## history模式和hash模式

对于前端路由来说,hash和history 都可以用于前后端分离项目,且两者都有各自的特点和各自的使用场景,在使用过程中主要要了解当前项目所处的场景,以便更好地判断使用哪一种路由模式更佳.

### 一.前端路由原理

**1.SPA**

SPA,即单页面应用.所谓单页web应用,就是只有一张web页面的应用.单页应用程序(SPA)是加载单个HTML页面并在**用户与应用程序交互时**动态更新该页面的web应用程序.浏览器一开始会加载必需的HTML,CSS和JavaScript,所有的操作都在这张页面上完成,都由JavaScript来控制.

**2.什么时候需要路由**

对于现代开发的项目来说,稍微复杂一点的SPA,都需要用到**路由**.而vue-router正是vue的路由标配,且vue-router有两种模式: hash 和 history.

### 二.Hash模式

1.定义

hash模式是一种把前端路由的路径用井号#拼接在真实url后面的模式.当井号#后面的路径发生变化时,浏览器并不会重新发起请求,而是会触发onhashchange事件.

2.网页url组成部分

(1)了解url组成部分:

| 属性               | 含义     |
| ------------------ | -------- |
| location.protocal  | 协议     |
| location.hostname  | 主机名   |
| location.host      | 主机     |
| location.port      | 端口号   |
| location.patchname | 访问页面 |
| location.search    | 搜索内容 |
| location.hash      | 哈希值   |

 (2)演示

下面用一个网址来演示以上属性:

```js
//http://127.0.0.1:8001/01-hash.html?a=100&b=20#/aaa/bbb
location.protocal // 'http:'
localtion.hostname // '127.0.0.1'
location.host // '127.0.0.1:8001'
location.port //8001
location.pathname //'01-hash.html'
location.serach // '?a=100&b=20'
location.hash // '#/aaa/bbb'
```

3.hash的特点

hash变化会触发网页跳转,即浏览器的前进和后退.

hash可以改变url,但是不会触发页面重新加载(hash的改变是记录在window.history中),即不会刷新页面.也就是说,所有页面的跳转都是在客户端进行操作.因此,这并不算是依次http请求,所以这种模式下不利于SEO优化.hash只能修改#后面的部分,所以只能跳转到与当前url同文档的url.

hash通过window.onhashchange的方式,来监听hash的改变,借此实现无刷新跳转的功能.

hash永远不会提交到server端(可以理解为只在前端自生自灭).

### 三.History模式

1.定义

history API 是 H5 提供的新特性,允许开发者**直接更改前端路由**,即更新浏览器URL地址而**不重新发起请求**

2.与hash的区别

我们用一个例子来演示,hash与history在浏览器下刷新的区别.**具体如下:**

正常页面浏览.

```js
https://github.com/xxx 刷新页面

https://github.com/xxx/yyy 刷新页面

https://github.com/xxx/yyy/zzz 刷新页面
```

改造H5 history模式

```js
https://github.com/xxx 刷新页面

https://github.com/xxx/yyy 前端跳转，不刷新页面

https://github.com/xxx/yyy/zzz 前端跳转，不刷新页面
```

3.history的API.

下面阐述几种HTML5新增的history API.

| API                                       | 定义                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| history.pushState(data, title [, url])    | pushState主要用于**往历史记录堆栈顶部添加一条记录**。各参数解析如下：**①data**会在onpopstate事件触发时作为参数传递过去；**②title**为页面标题，当前所有浏览器都会忽略此参数；③**url**为页面地址，可选，缺少时表示为当前页地址 |
| history.replaceState(data, title [, url]) | 更改当前的历史记录，参数同上； 上面的pushState是添加，这个更改 |
| history.state                             | 用于存储以上方法的data数据，不同浏览器的读写权限不一样       |
| window.onpopstate                         | 响应pushState或者replaceState的调用                          |

4.history的特点

对于history来说,主要有以下特点:

新的url可以是与当前url同源的任意url,也可以是与当前url一样的地址,但是这样会导致的一个问题是,会把**重复的这一次操作**记录到栈当中.

通过history.state,添加任意类型的数据到记录中.

可以额外设置title属性,以便后续使用.

通过pushState,replaceState来实现无刷新跳转的功能.

5.存在问题

对于history来说,确实解决了不少hash存在的问题,但是也带来了新的问题.**具体如下**

使用history模式时,在对当前的页面进行刷新时,此时浏览器会重新发起请求.如果nginx没有匹配得到当前的url,就会出现404的页面.

而对于hash模式来说,它虽然看着是改变了url,但不会被包括在http请求中.所以,它算是被用来指导浏览器的动作,并不影响服务器端.因此,改变hash并没有真正地改变url.所以页面路径还是之前的路径,nginx也就不会拦截.

因此,在使用history模式时,需要**通过服务端来允许地址可访问**,如果没有设置,就很容易导致出现404的局面.

