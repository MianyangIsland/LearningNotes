# React.Compoment

## 概览

React的组件可以定义为class或函数的形式.class组件目前提供了更多的功能.如需定义class组件,需要继承React.Component:

```js
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

在React.Component的子类中有个必须定义的render()函数.

**React强烈建议不要创建自己的组件基类**.在React组件中,代码重用的主要方式是组合而不是继承.

# 组件的生命周期

每个组件都包含"生命周期方法",可以重写这些方法,以便于在运行过程中特定的阶段执行这些方法.

**组件的生命周期图:**

![1654517965348](C:\Users\22719\AppData\Local\Temp\1654517965348.png)

## 挂载

当组件实例被创建并插入DOM中时,其生命周期调用顺序如下:

constructor()  ---   static getDerviedStateFormProps() --- render()  ------    componentDidMount()

## 更新

当组件的props或state发生变化时会触发更新.组件更新的生命周期调用顺序如下:

static getDerivedStateFormProps() -----   shouldComponentUpdate()  ------    render()   -----  getSnapshotBeforeUpdate()   -------    componentdDidUpdate()

## 卸载

当组件从DOM中移除时会调用如下方法:

componentWillUnmount().

## 错误处理

当渲染过程,生命周期,或子组件的构造函数中抛出错误,会调用如下方法:

static getDerivedStateFromError() ----   componentDidCatch()

## 参考

**render**

```js
render()
```

render()方法是class组件中唯一必须实现的方法.

当render()被调用时,它会检查this.props和this.state的变化并返回以下类型之一:

- **React 元素**。通常通过 JSX 创建。例如，`<div />` 会被 React 渲染为 DOM 节点，`<MyComponent />` 会被 React 渲染为自定义组件，无论是 `<div />` 还是 `<MyComponent />` 均为 React 元素。
- **数组或 fragments**。 使得 render 方法可以返回多个元素 
- **Portals**。 可以渲染子节点到不同的 DOM 子树中。 
- **字符串或数值类型** .它们在 DOM 中会被渲染为文本节点。 
- **布尔类型或 null**。什么都不渲染。 （主要用于支持返回 `test && <Child />` 的模式，其中 test 为布尔类型。) 

render()函数应该为纯函数,这意味这在不修改组件state的情况下,每次调用时都返回相同的结果,并且它不会直接与浏览器交互.

如需与浏览器进行交互,咋子componentDidMount()或其他生命周期方法中执行相关操作.保持render()为纯函数,可以使组件更容易思考.

Warning:

如果shouldComponentUpdate()返回false,则不会调用render().

**constructor**

```js
constructor(props)
```

**如果不初始化state或不进行方法绑定,则不需要为React组件实现构造函数.**

在React组件挂载之前,会调用它的构造函数.在为React.Component子类实现构造函数时,应在其他语句之前调用super(props).否则,this.props在构造函数中肯会出现未定义的bug.

通常,在React中,构造函数仅用于以下两种情况:

通过给this.state赋值对象来初始化内部state.

为事件处理函数绑定实例.

在constructor()函数中**不要调用setState()方法.**如果你的组件需要使用内部state,请直接在构造函数中为**this.state赋值初始state:**

```js
constructor(props) {
  super(props);
  // 不要在这里调用 this.setState()
  this.state = { counter: 0 };
  this.handleClick = this.handleClick.bind(this);
}
```

只能在构造函数中直接为this.state赋值.如需在其他方法中赋值,应使用this.setState()替代.

要避免在构造函数中引入任何副作用或订阅。如遇到此场景，请将对应的操作放置在 `componentDidMount` 中。 

注意

**避免将 props 的值复制给 state！这是一个常见的错误：**

```js
constructor(props) {
 super(props);
 // 不要这样做
 this.state = { color: props.color };
}
```

如此做毫无必要（你可以直接使用 `this.props.color`），同时还产生了 bug（更新 prop 中的 `color` 时，并不会影响 state）。

### `componentDidMount()`

```js
componentDidMount()
```

`componentDidMount()` 会在组件挂载后（插入 DOM 树中）立即调用。依赖于 DOM 节点的初始化应该放在这里。如需通过网络请求获取数据，此处是实例化请求的好地方。 

这个方法是比较适合添加订阅的地方。如果添加了订阅，请不要忘记在 `componentWillUnmount()` 里取消订阅 

### `componentDidUpdate()`

```js
componentDidUpdate(prevProps, prevState, snapshot)
```

`componentDidUpdate()` 会在更新后会被立即调用。首次渲染不会执行此方法。 

当组件更新后，可以在此处对 DOM 进行操作。如果你对更新前后的 props 进行了比较，也可以选择在此处进行网络请求。（例如，当 props 未发生变化时，则不会执行网络请求）。 

```js
componentDidUpdate(prevProps) {
  // 典型用法（不要忘记比较 props）：
  if (this.props.userID !== prevProps.userID) {
    this.fetchData(this.props.userID);
  }

```

你也可以在 `componentDidUpdate()` 中**直接调用 setState()**，但请注意**它必须被包裹在一个条件语句里**，正如上述的例子那样进行处理，否则会导致死循环。它还会导致额外的重新渲染，虽然用户不可见，但会影响组件性能。 

如果组件实现了 `getSnapshotBeforeUpdate()` 生命周期（不常用），则它的返回值将作为 `componentDidUpdate()` 的第三个参数 “snapshot” 参数传递。否则此参数将为 undefined。 

Warning:

如果shouldComponentUpdate()返回值为false,则不会调用componentDidUpdate().

### `componentWillUnmount()`

```js
componentWillUnmount()
```

`componentWillUnmount()` 会在组件卸载及销毁之前直接调用。在此方法中执行必要的清理操作，例如，清除 timer，取消网络请求或清除在 `componentDidMount()` 中创建的订阅等。 

`componentWillUnmount()` 中**不应调用 setState()**，因为该组件将永远不会重新渲染。组件实例卸载后，将永远不会再挂载它。



##  不常用的生命周期方法

shouldCompoentUpdate()

```js
shouldComponentUpdate(nextProps, nextState)
```

根据 `shouldComponentUpdate()` 的返回值，判断 React 组件的输出是否受当前 state 或 props 更改的影响。默认行为是 state 每次发生变化组件都会重新渲染。大部分情况下，你应该遵循默认行为。 

当 props 或 state 发生变化时，`shouldComponentUpdate()` 会在渲染执行之前被调用。返回值默认为 true。首次渲染或使用 `forceUpdate()` 时不会调用该方法。 

此方法仅作为性能优化的方式而存在.不要企图依靠此方法来"阻止"渲染,因为这可能会产生bug.应该考虑使用内置的React.PureCompoent组件,而不是手动编写shouldComponentUpdate().PureComponent会对props和state进行浅层比较,并减少了跳过必要更新的可能性.

**React不建议在shouldComponentUpdate()中进行深层比较或使用JSON.stringify().这样非常影响效率,且会损害性能**

static getDerivedStateFromProps()

```js
static getDerivedStateFromProps(props, state)
```

getDerivedStateFromProps会在调用render方法之前调用,并且在初始挂载及后续更新时都会被调用.它应返回一个对象来更新state,如果返回null则不更新任何内容.

此方法适用于state的值在任何时候都取决于props.例如,实现<Transition>组件可能很方便,该组件会比较当前组件与下一组件,以决定针对那些组件进行专场动画.

### `getSnapshotBeforeUpdate()`

```js
getSnapshotBeforeUpdate(prevProps, prevState)
```

`getSnapshotBeforeUpdate()` 在最近一次渲染输出（提交到 DOM 节点）之前调用。它使得组件能在发生更改之前从 DOM 中捕获一些信息（例如，滚动位置）。此生命周期方法的任何返回值将作为参数传递给 `componentDidUpdate()`。 

此用法并不常见，但它可能出现在 UI 处理中，如需要以特殊方式处理滚动位置的聊天线程等。 

```js
class ScrollingList extends React.Component {
  constructor(props) {
    super(props);
    this.listRef = React.createRef();
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    // 我们是否在 list 中添加新的 items ？
    // 捕获滚动位置以便我们稍后调整滚动位置。
    if (prevProps.list.length < this.props.list.length) {
      const list = this.listRef.current;
      return list.scrollHeight - list.scrollTop;
    }
    return null;
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    // 如果我们 snapshot 有值，说明我们刚刚添加了新的 items，
    // 调整滚动位置使得这些新 items 不会将旧的 items 推出视图。
    //（这里的 snapshot 是 getSnapshotBeforeUpdate 的返回值）
    if (snapshot !== null) {
      const list = this.listRef.current;
      list.scrollTop = list.scrollHeight - snapshot;
    }
  }

  render() {
    return (
      <div ref={this.listRef}>{/* ...contents... */}</div>
    );
  }
}
```

在上述示例中，重点是从 `getSnapshotBeforeUpdate` 读取 `scrollHeight` 属性，因为 “render” 阶段生命周期（如 `render`）和 “commit” 阶段生命周期（如 `getSnapshotBeforeUpdate` 和 `componentDidUpdate`）之间可能存在延迟。 

### `static getDerivedStateFromError()`

```js
static getDerivedStateFromError(error)
```

**此生命周期会在后代组件抛出错误后被调用。**它将抛出的错误作为参数，并返回一个值以更新 state .

```js
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染可以显降级 UI
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      // 你可以渲染任何自定义的降级  UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}
```

Warning:

getDerivedStateFromError()会在渲染阶段调用,因此不允许出现副作用.如遇到此类情况,改用componentDidCatch().

## componentDidCatch()

```js
componentDidCatch(error, info)
```

此生命周期在后代组件抛出错误后被调用.它接受两个参数:

1.error------  抛出的错误.

2.info ------   带有componentStack key的对象.

componentDidCatch()会在"提交"阶段被调用,因此允许执行副作用.他应该用于记录错误之类的情况:

```js
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染可以显示降级 UI
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    // "组件堆栈" 例子:
    //   in ComponentThatThrows (created by App)
    //   in ErrorBoundary (created by App)
    //   in div (created by App)
    //   in App
    logComponentStackToMyService(info.componentStack);
  }

  render() {
    if (this.state.hasError) {
      // 你可以渲染任何自定义的降级 UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}
```

React 的开发和生产构建版本在 `componentDidCatch()` 的方式上有轻微差别。 

在开发模式下，错误会冒泡至 `window`，这意味着任何 `window.onerror` 或 `window.addEventListener('error', callback)` 会中断这些已经被 `componentDidCatch()` 捕获的错误。 

相反，在生产模式下，错误不会冒泡，这意味着任何根错误处理器只会接受那些没有显式地被 `componentDidCatch()` 捕获的错误。 

## 过时的生命周期(略)

## 其他API

不同于上述生命周期方法(React 主动调用),以下方法是你可以在组件中调用的方法.

只有两个方法:setState()和forceUpdate().

## setState()

```js
setState(updater, [callback])
```

`setState()` 将对组件 state 的更改排入队列，并通知 React 需要使用更新后的 state 重新渲染此组件及其子组件。这是用于更新用户界面以响应事件处理器和处理服务器数据的主要方式 .将 `setState()` 视为*请求*而不是立即更新组件的命令。为了更好的感知性能，React 会延迟调用它，然后通过一次传递更新多个组件。 

`setState()` 并不总是立即更新组件。它会批量推迟更新。这使得在调用 `setState()` 后立即读取 `this.state` 成为了隐患。为了消除隐患，请使用 `componentDidUpdate` 或者 `setState` 的回调函数（`setState(updater, callback)`），这两种方式都可以保证在应用更新后触发。如需基于之前的 state 来设置当前的 state，请阅读下述关于参数 `updater` 的内容。 

除非 `shouldComponentUpdate()` 返回 `false`，否则 `setState()` 将始终执行重新渲染操作。如果可变对象被使用，且无法在 `shouldComponentUpdate()` 中实现条件渲染，那么仅在新旧状态不一时调用 `setState()`可以避免不必要的重新渲染 

参数一为带有形式参数的updater函数:

```js
(state, props) => stateChange
```

state是对应用变化时组件状态的引用.当然,它不应直接被修改.应该使用基于state和props构建的新对象来表示变化.例如，假设我们想根据 `props.step` 来增加 state： 

```js
this.setState((state, props) => {
  return {counter: state.counter + props.step};
});
```

updater 函数中接收的 `state` 和 `props` 都保证为最新。updater 的返回值会与 `state` 进行浅合并。 

setState()的第二个参数为可选的回调函数,它将setState()完成合并并重新渲染组件后执行.通常,建议使用componentDidUpdate()来代替此方式.

`setState()` 的第一个参数除了接受函数外，还可以接受对象类型： 

```js
setState(stateChange[, callback])
```

`stateChange` 会将传入的对象浅层合并到新的 state 中，例如，调整购物车商品数： 

```js
this.setState({quantity: 2})
```

这种形式的 `setState()` 也是异步的，并且在同一周期内会对多个 `setState` 进行批处理。例如，如果在同一周期内多次设置商品数量增加，则相当于： 

```js
Object.assign(
  previousState,
  {quantity: state.quantity + 1},
  {quantity: state.quantity + 1},
  ...
)
```

后调用的 `setState()` 将覆盖同一周期内先调用 `setState` 的值，因此商品数仅增加一次。如果后续状态取决于当前状态，我们建议使用 updater 函数的形式代替： 

```js
this.setState((state) => {
  return {quantity: state.quantity + 1};
});
```

### `forceUpdate()`

```js
component.forceUpdate(callback)
```

默认情况下，当组件的 state 或 props 发生变化时，组件将重新渲染。如果 `render()` 方法依赖于其他数据，则可以调用 `forceUpdate()` 强制让组件重新渲染。 

调用 `forceUpdate()` 将致使组件调用 `render()` 方法，此操作会跳过该组件的 `shouldComponentUpdate()`。但其子组件会触发正常的生命周期方法，包括 `shouldComponentUpdate()` 方法。如果标记发生变化，React 仍将只更新 DOM。

通常你应该避免使用 `forceUpdate()`，尽量在 `render()` 中使用 `this.props` 和 `this.state`。

## Class 属性

### `defaultProps`

`defaultProps` 可以为 Class 组件添加默认 props。这一般用于 props 未赋值，但又不能为 `null` 的情况。例如： 

```js
class CustomButton extends React.Component {
  // ...
}

CustomButton.defaultProps = {
  color: 'blue'
};
```

如果未提供 `props.color`，则默认设置为 `'blue'` 

```js
  render() {
    return <CustomButton /> ; // props.color 将设置为 'blue'
  }
```

如果 `props.color` 被设置为 `null`，则它将保持为 `null` 

```js
  render() {
    return <CustomButton color={null} /> ; // props.color 将保持是 null
  }
```

