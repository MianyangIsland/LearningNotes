# State和生命周期:

State 与 props 类似，但是 state 是私有的，并且完全受控于当前组件。 

class组件应该始终使用props参数来调用父类的构造函数.

## 生命周期方法:

`componentDidMount()` 方法会在组件已经被渲染到 DOM 中后运行 .

## 正确地使用 State:

关于 `setState()` 你应该了解三件事：

1.不要直接修改State,而是应该使用setState(): 构造函数是唯一可以给this.state赋值的地方.

## State的更新可能是异步的:

出于性能考虑,React可能会把多个setState()调用并合成一个调用.

因为this.props和this.state可能会异步更新,所以不要依赖它们的值来更新下一个状态.

要解决这个问题,可以让serState()接收到一个函数而不是一个对象.这个函数用上一个state作为第一个参数,将此次更新被应用时的props做为第二个参数.

```js
this.setState((state,props)=>{
    counter:state.counter + props.increment
})
```

### State 的更新会被合并:

当你调用setState()的时候,React会把你提供的对象合并到当前的state.

eg: state包含几个独立的变量:

```js
 constructor(props) {
    super(props);
    this.state = {
      posts: [],
      comments: []
    };
  }
```

然后你可以分别调用 `setState()` 来单独地更新它们： 

```js
 componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    });
  }
```

这里的合并时浅合并,所以this.setState({comments})完整保留了this.state.posts,但时完全替换了this.state.commnets.简而言之,就是setState的时候,只会改变你传入的对象的值,其他的state的值并不会改变.

## 数据是向下流动的:

不管是父组件或是子组件都无法知道某个组件是有状态的还是无状态的，并且它们也并不关心它是函数组件还是 class 组件.这就是为什么称 state 为局部的或是封装的的原因。除了拥有并设置了它的组件，其他组件都无法访问。

组件可以选择把它的state作为props向下传递到它的子组件中:

```js
<FormattedDate date={this.state.date} />
```

`FormattedDate` 组件会在其 props 中接收参数 `date`，但是组件本身无法知道它是来自于 `Clock` 的 state，或是 `Clock` 的 props，还是手动输入的：

这通常会被叫做"自上而下"或是"单向"的数据流.任何的state总是所属于特定的组件,而且从该state派生的任何数据或UI只能影响DOM树中"低于"它们的组件.

如果你把一个以组件构成的树想象成一个 props 的数据瀑布的话，那么每一个组件的 state 就像是在任意一点上给瀑布增加额外的水源，但是它只能向下流动。 在 React 应用中，组件是有状态组件还是无状态组件属于组件实现的细节，它可能会随着时间的推移而改变。你可以在有状态的组件中使用无状态的组件，反之亦然。 