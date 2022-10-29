一.浏览器在默认情况下用的push方法,利用压栈的形式来保存浏览记录.replace模式开启以后不会留下历史记录.

  eg:

```javascript
replace={true}  或    replace
```

二.编程式路由导航：

​    1.含义：利用 this.props.history所带的自带属性实现路由的跳转:

​    1.pushShow:

```javascript
//push跳转+携带params参数
this.props.history.push(`/home/message/detail/${id}/${title}`)
//push跳转+携带search参数
this.props.history.push(`/home/message/detail?id=${id}&title={title}`)
//push跳转+携带state参数：
this.props.history.push(`/home/message/detail`,{id,title})
```

2.replace:

```javascript
this.props.history.replace();
this.props.history.goBack();
this.props.history.goForward();
this.props.history.go();
```

三.withRouter的使用：

withRouter可以加工一般组件,让一般组件具备路由组件所特有的API.   withRouter的返回值是一个新组件.

eg:

```javascript
class Header extends Component{}

export default withRouter(Header)
```

四.BrowserRouter与HashARouter的区别：

   1.底层原理不一样：

​                    BrowserRouter使用的是H5的history APi ,不兼容 IE9以下版本.

​                    HashRouter使用的是URL的哈希值.

  2.url的表现形式不一样:

​                  BrowserRouter的路径中没有#,例如：localhost:3000/demo/test

​                  HashRouter的路径包含#,例如localhost:3000/#/demo/test

3.刷新后对路由state参数的影响

​                 1.BrowserRouter没有任何影响.因为state保存在history对象中.

​                  2.HashRouter刷新后会导致路由state参数的丢失

4.备注：HashRouter可以用于解决一些路径错误相关的问题.