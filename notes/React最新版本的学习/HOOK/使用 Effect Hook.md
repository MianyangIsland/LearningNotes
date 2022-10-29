# 使用 Effect Hook:

*Effect Hook*可以在函数组件中执行副作用操作:

```js
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // Similar to componentDidMount and componentDidUpdate:
  useEffect(() => {
    // Update the document title using the browser API
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

数据获取,设置订阅以及手动更改React组件中的DOM都属于副作用.可以把useEffect Hook 看做`componentDidMount`，`componentDidUpdate` 和 `componentWillUnmount` 这三个函数的组合。 

## 两种常见副作用操作:

### 无需清除的effect:

有时候,只想在React更新DOM之后运行一些额外的代码.比如发网络请求,手动更新DOM,记录日志,这些都是常见的无需清除的操作.因为我们在执行完这些操作之后,就可以忽略它们了.

### 使用 class 的示例

在 React 的 class 组件中，`render` 函数是不应该有任何副作用的。一般来说，在这里执行操作太早了，我们基本上都希望在 React 更新 DOM 之后才执行我们的操作。

这就是为什么在 React class 中，我们把副作用操作放到 `componentDidMount` 和 `componentDidUpdate` 函数中。回到示例中，这是一个 React 计数器的 class 组件。它在 React 对 DOM 进行操作之后，立即更新了 document 的 title 属性

```js
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }
  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}
```

注意，**在这个 class 中，我们需要在两个生命周期函数中编写重复的代码。** 

这是因为很多情况下，我们希望在组件加载和更新时执行同样的操作。从概念上说，我们希望它在每次渲染之后执行 —— 但 React 的 class 组件没有提供这样的方法。即使我们提取出一个方法，我们还是要在两个地方调用它。

现在让我们来看看如何使用 `useEffect` 执行相同的操作。

### 

 使用 Hook 的示例

```js
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

## useEffect做了什么?

通过使用这个Hook,你可以告诉React罪案需要在渲染后执行某些操作.React会保存你传递的函数(将它称之为'effect'),并且在执行DOM更新之后调用它.在这个effect中,我们设置了 document 的 title 属性，不过我们也可以执行数据获取或调用其他命令式的 API。 

## 为什么在组件内部调用useEffect?:

将useEffect放在组件内部让我们可以在effect中直接访问count state变量(或其他props).我们不需要特殊的API来读取它------因为它已经保存在函数作用域中.Hook 使用js的闭包机制,而不用在js已近提供了解决方案的情况下,还引入特定的React API.

## useEffect会在每次渲染后都执行:

默认情况下,它在第一次渲染之后和每次更新之后都会执行,我们更容易接受effect发生在"渲染之后"这种概念,不用去考虑"挂载"还是"更新".React保证了每次运行effect同时,DOM都已经更新完毕.

注意:传递给 `useEffect` 的函数在每次渲染中都会有所不同，这是刻意为之的。事实上这正是我们可以在 effect 中获取最新的 `count` 的值，而不用担心其过期的原因。每次我们重新渲染，都会生成*新的* effect，替换掉之前的。某种意义上讲，effect 更像是渲染结果的一部分 —— 每个 effect “属于”一次特定的渲染。 与 `componentDidMount` 或 `componentDidUpdate` 不同，使用 `useEffect` 调度的 effect 不会阻塞浏览器更新屏幕，这让你的应用看起来响应更快。大多数情况下，effect 不需要同步地执行。在个别情况下（例如测量布局），有单独的 [`useLayoutEffect`](https://zh-hans.reactjs.org/docs/hooks-reference.html#uselayouteffect) Hook 供你使用，其 API 与 `useEffect` 相同。 

## 需要清除的effect:

还有一些副作用是需要清除的.例如订阅外部数据源.这种情况下,清除工作是十分重要的可以防止引起内存泄露!

### 使用 Class 的示例

在 React class 中，你通常会在 `componentDidMount` 中设置订阅，并在 `componentWillUnmount` 中清除它。例如，假设我们有一个 `ChatAPI` 模块，它允许我们订阅好友的在线状态。以下是我们如何使用 class 订阅和显示该状态： 

```js
class FriendStatus extends React.Component {
  constructor(props) {
    super(props);
    this.state = { isOnline: null };
    this.handleStatusChange = this.handleStatusChange.bind(this);
  }

  componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
  handleStatusChange(status) {
    this.setState({
      isOnline: status.isOnline
    });
  }

  render() {
    if (this.state.isOnline === null) {
      return 'Loading...';
    }
    return this.state.isOnline ? 'Online' : 'Offline';
  }
}
```

你会注意到 `componentDidMount` 和 `componentWillUnmount` 之间相互对应。使用生命周期函数迫使我们拆分这些逻辑代码，即使这两部分代码都作用于相同的副作用。 

### 使用 Hook 的示例

你可能认为需要单独的 effect 来执行清除操作。但由于添加和删除订阅的代码的紧密性，所以 `useEffect` 的设计是在同一个地方执行。如果你的 effect 返回一个函数，React 将会在执行清除操作时调用它： 

```js
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // Specify how to clean up after this effect:
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

### 为什么要在effect中返回一个函数?

### 这是effect可选的清除机制.每个effect都可以返回一个清除函数.如vi可以添加和移除订阅的逻辑放在一起.它们都属于effect的一部分.

### React何时清除effect?:

React会在组件卸载的时候执行清除操作.之前学到,effect在每次渲染的时候都会执行.这就是为什么React在执行当前effect之前对上一个effect进行清除.

Warning:并不是必须为effect中返回的函数命名.这里我们将其命名为cleanup是为了表明此函数的目的,但其实也可以返回一个箭头函数或者起一个别的名字.

小结:

了解了useEffect可以在组件渲染后实现各种不同的副作用.有些副作用可能需要清除,所以需要返回一个函数,其他的effect可能不必清除,所以不需要返回.

effect Hook 使用同一个API来满足这两种情况.

## 使用Effect的提示:

### 提示:使用多个Effect实现关注点分离:

使用 Hook 其中一个[目的](https://zh-hans.reactjs.org/docs/hooks-intro.html#complex-components-become-hard-to-understand)就是要解决 class 中生命周期函数经常包含不相关的逻辑，但又把相关逻辑分离到了几个不同方法中的问题。 

```js
class FriendStatusWithCounter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0, isOnline: null };
    this.handleStatusChange = this.handleStatusChange.bind(this);
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }

  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  handleStatusChange(status) {
    this.setState({
      isOnline: status.isOnline
    });
  }
  // ...
```

可以发现设置 `document.title` 的逻辑是如何被分割到 `componentDidMount` 和 `componentDidUpdate` 中的，订阅逻辑又是如何被分割到 `componentDidMount` 和 `componentWillUnmount` 中的。而且 `componentDidMount` 中同时包含了两个不同功能的代码。 

Hook的解决办法:(可以使用像State的Hook一样),可以使用多个effect.这会将不同逻辑分离到不同的effect中:

```js
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
  // ...
}
```

Hook允许我们按照代码的用途分离它们.而不是像生命周期函数一样.React将按照effect声明的顺序依次调用组件中的每一个effect.

## 解释:为社么每次更新的时候要运行Effect.

为什么effect的清除阶段在每次重新渲染时都会执行,而不是只在卸载组件的时候执行一次?

介绍了一个用于显示好友是否在线的 `FriendStatus` 组件。从 class 中 props 读取 `friend.id`，然后在组件挂载后订阅好友的状态，并在卸载组件的时候取消订阅： 

```js
  componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
```

**但是当组件已经显示在屏幕上时，friend prop 发生变化时会发生什么？** 我们的组件将继续展示原来的好友状态。这是一个 bug。而且我们还会因为取消订阅时使用错误的好友 ID 导致内存泄露或崩溃的问题。 

在 class 组件中，我们需要添加 `componentDidUpdate` 来解决这个问题： 

```js
  componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentDidUpdate(prevProps) {
    // 取消订阅之前的 friend.id
    ChatAPI.unsubscribeFromFriendStatus(
      prevProps.friend.id,
      this.handleStatusChange
    );
    // 订阅新的 friend.id
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
```

忘记正确地处理 `componentDidUpdate` 是 React 应用中常见的 bug 来源。 

Hook的版本:

```jjs
function FriendStatus(props) {
  // ...
  useEffect(() => {
    // ...
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
```

并不需要特定的代码来处理更新逻辑,因为useEffect默认就会处理.它会在调用一个新的effect之前对一个effect进行处理.

也就是说,当我们的props在更新的时候,如果没有正确的使用componentDidUpdate的话,就会导致一直订阅的是第一个好友的isOnline,因此需要在componentDidUpdate的时候,取消订阅上一次的好友id,而订阅下一个当前状态的好友id.这是在class里面的bug.但是在effect里面的话,由于effect相当于三个生命周期的合并,所以会自动的将上一个id取消订阅.

## 提示:通过跳过Effect进行性能优化:

在某些情况下,每次渲染后都执行清理或执行effect可能会导致性能问题.在class组件中,我们可以通过在componentDidUpdate中添加preProps或prevState的比较逻辑解决:

```js
componentDidUpdate(prevProps, prevState) {
  if (prevState.count !== this.state.count) {
    document.title = `You clicked ${this.state.count} times`;
  }
}
```

这是很常见的需求,所以它被内置到了useEffect的Hook API中.如果某些特定值在两次重渲染之间没有发生变化,你可以通知React对effect的调用,只要传递数组作为useEffect的第二个可选值即可.

```js
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // 仅在 count 更改时更新
```

在上面这个例子中,传入[count]作为第二个参数.如果count的值是5,而且我们的组件重渲染的时候count还是等于5,React将对前一次渲染的[5]和后一次渲染的[5]进行比较.因为数组中的所有的元素都是相等的 (5 === 5),React 会跳过这个effect,这就实现了性能的优化.

当渲染时,如果count的值更新成了6,React将会把前一次渲染时的数组[5]和这次渲染的数组[6]中的元素进行比较.这次因为 5 !== 6,React就会再次调用effect.如果数组中有多个元素,即使只有一个元素发生变化,React也会执行effect.

对于有清除操作的effect同样使用:

```js
useEffect(() => {
  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
  return () => {
    ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  };
}, [props.friend.id]); // 仅在 props.friend.id 发生变化时，重新订阅
```

### warning:

如果你要使用此优化方式,请确保数组中包含了所有外部作用域中会随时间变化并且在effect中使用变量,否则你的代码会引用到当前先渲染中的旧变量.

如果要执行只运行一次的effect(仅在组件挂载和卸载时执行),可以传递一个空数组([])作为第二个参数.这就告诉React你的effect不依赖于props或state中的任何值,所以它永远都不需要重复执行.这并不属于特殊情况------它依然遵循数组的工作方式.

如果你传入了一个空数组（`[]`），effect 内部的 props 和 state 就会一直拥有其初始值。尽管传入 `[]` 作为第二个参数更接近大家更熟悉的 `componentDidMount` 和 `componentWillUnmount` 思维模式， 但在后面的Hooks FAQ中会学到更好的办法来避免过于频繁的重复调用effect.除此之外,React会等待浏览器完成画面渲染之后才会延迟调用useEffect,因此会使得额外操作很方便.