## React生命周期

在React中,对于每一次由状态改变导致页面视图的改变,都会经历两个阶段:render , commit阶段.

只有class组件才有生命周期,因为class组件会创建对应的实例,而函数组件不会.组件实例从被创建至到销毁的过程称为[**组件的生命周期**].

由class组件创建的实例具有生命周期,它的render函数在render阶段执行,并在此阶段进行DOM节点的diff(diff算法就是在此阶段进行的),找出需要改变的DOM操作.然后在commit阶段将对应的DOM操作提交至视图中.

而class组件实例的所有生命周期函数,都会在render阶段和cmmoit阶段执行.

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210403095846563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80ODA2NTI2Mg==,size_16,color_FFFFFF,t_70) 

**加载阶段**

1.constructor

2.static getDerivedStateFromProps(props,state)

```txt
无法访问this , props , state 是更新后的
必须返回一个对象,用来更新state或者null不更新
必须要初始化state.
场景: state的值在任何时候都取决于props时
```

3.render

4.componentDidMount 挂在完成后执行.

**更新阶段**

1.渲染前static getDerivedStateFromProps(props,state)

```txt
无法访问this , props , state 是更新后的
必须返回一个对象,用来更新state或者null不更新
必须要初始化state
场景: state的值在任何时候都取决于props时
```

2.shouleComponentUpdate().

3.render.

4.getSnapshortBeforeUpdate(prevProps,prevState).

```js
组件能在发生更改之前从DOM中捕获一些信息(dom渲染前的状态).
返回值 | null 会给componentDidUpdate
prevProps , prevState更新前this.props,this.state更新后
```

5.componentDidUpdate.

**卸载时**

componentWillUnmount组件即将卸载执行.

## 使用React Hooks模拟生命周期

hook 可以让我们在不编写class的情况下使用state以及其他的react特性,例如生命周期.

**constructor**

class组件:

```js
class Example extends Component {
    constructor() {
        super();
        this.state = {
            count: 0
        }
    }
    render() {
      return null;
  }
}
```

函数组件不需要构造函数,可以通过useState来初始化state.

```js
function Example() {
  const [count, setCount] = useState(0);
  return null;
}
```

**componentDidMount**

class组件访问componentDidMount

```js
class Example extends React.Component {
  componentDidMount() {
    console.log('I am mounted!');
  }
  render() {
    return null;
  }
}
```

使用hooks模拟componentDidMount

```js
function Example() {
  useEffect(() => console.log('mounted'), []);
  return null;
}
```

useEffect拥有两个参数,第一个参数作为回调函数会在浏览器布局和绘制完成后调用,因此它不会阻碍浏览器的渲染进程.

第二个参数是一个数组.

当数组存在并有值时,如果数组中的任何值发生更改,则每次渲染后都会触发回调.

当它不存在时,每次渲染后都会触发回调函数.

当它是一个空列表时,回调只会被触发一次,类似于componentDidMount.

**shouldComponentUpdate**

class组件访问shouldComponentUpdate.

```js
shouldComponentUpdate(nextProps, nextState){
  console.log('shouldComponentUpdate')
  // return true 更新组件
  // return false 则不更新组件
}
```

hooks模拟shouldComponentUpdate

```js
const MyComponent = React.memo(
    _MyComponent, 
    (prevProps, nextProps) => nextProps.count !== prevProps.count
)
```

React.memo包裹一个组件来对它的props进行浅比较,但这不是一个hooks,因为它的写法和hooks不同,其实React.memo等效于PureCompenent,但它只会比较props.

**componentDidUpdate**

class组件访问componentDidUpdate

```js
componentDidMount() {
  console.log('mounted or updated');
}

componentDidUpdate() {
  console.log('mounted or updated');
}
```

使用hooks模拟componentDidUpdate.

```js
useEffect(() => console.log('mounted or updated'));
```

值得注意的是,这里的回调函数会在每次渲染后调用,因此不仅可以访问componentDidUpdate,还可以访问componentDidMpount,如果指向模拟componentDidUpdate,我们可以这样来实现.

```js
const mounted = useRef();
useEffect(() => {
  if (!mounted.current) {
    mounted.current = true;
  } else {
   console.log('I am didUpdate')
  }
});
```

useRef在组件中创建"实例变量".它作为一个标志来指示组件是否处于挂在或更新阶段.当组件更新完成后才会执行else里面的内容,以此来单独模拟componentDidUpdate.

**componentWillUnmount**

class组件访问componentWillUmmount

```js
componentWillUnmount() {
  console.log('will unmount');
}
```

hooks模拟componentWillUnmout

```js
useEffect(() => {
  return () => {
    console.log('will unmount');
  }
}, []);
```

当在useEffect 的回调函数中返回一个函数时,这个函数会在组件卸载前被调用.我们可以在这里面清除定时器或事件监听器.

**总结**

引入hooks 的函数组件功能越来越完善,在多数情况下,我们完全可以使用hook来替代class组件.并且使用函数组件也有以下几点好处.

 纯函数概念,同样的props会得到同样的渲染结果.

 可以使用函数组合,嵌套,实现功能更强大的组件

 组件不会被实例化,整体渲染性能得到提升.

