# Hook概览:

## State Hook:

```js
import React, { useState } from 'react';

function Example() {
  // 声明一个叫 “count” 的 state 变量。
  const [count, setCount] = useState(0);

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

在这里,useState就是一个Hook.通过在函数组件里调用它来给组件添加一些内部 state。React 会在重复渲染时保留这个 state。 useState会返回一对值:当前状态和一个让你更新它的函数,你可以在事件处理函数中或其他一些地方调用这个函数.它类似class组件的this.setState,但它不会把新的state和旧的state进行合并.

useState唯一的参数就是初始state.在上面例子中,我梦的计数器是从零开始的,所以初始state就是0.值得注意的是,不同于this.state,这里的state不一定要是一个对象,如果有需要,它也可以是.这个初始state参数只有在第一次渲染时会被用到.

### 声明多个state变量:

可以在一个组件中多次使用State Hook:

```js
unction ExampleWithManyStates() {
  // 声明多个 state 变量！
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
  // ...
}
```



数组机解构的语法让我们在调用 `useState` 时可以给 state 变量取不同的名字。当然，这些名字并不是 `useState` API 的一部分。React 假设当你多次调用 `useState` 的时候，你能保证每次渲染时它们的调用顺序是不变的。

那么,什么是Hook ?

Hook 是一些可以让你在函数组件里"钩入"React state及生命周期等特性的函数.Hook不能在class组件中使用 -- 这使得你不使用class也能使用React.

Effect Hook:

在React组件中执行过数据获取,订阅或者手动修改过DOM.统一把这些操作称为"副作用",或者简称为"作用".

useEffect就是一个Effect Hook,给函数组件增加了操作副作用的能力.它跟class组件中:

`componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount` 具有相同的用途 ,只不过被合并成立一个API.

```js
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // 相当于 componentDidMount 和 componentDidUpdate:
  useEffect(() => {
    // 使用浏览器的 API 更新页面标题
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



当你调用 `useEffect` 时，就是在告诉 React 在完成对 DOM 的更改后运行你的“副作用”函数。由于副作用函数是在组件内声明的，所以它们可以访问到组件的 props 和 state。默认情况下，React 会在每次渲染后调用副作用函数 —— **包括**第一次渲染的时候。 

副作用函数还可以通过返回一个函数来指定如何"清除"副作用.

```js
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```



跟useState一样,可以在组件中多次使用useEffect:

```js
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }
  // ...
```

通过使用Hook,可以把组件内相关的副作用组织在一起,而不把它们拆分到不同的生命周期函数里.

## Hook使用规则:

Hook 就是js函数,但是使用它们会有两个额外的规则:

1.只能在函数最外层调用Hook.不要在循环,条件判断或者子函数中调用.

2.只能在React的函数组件中调用Hook.不要在其他js函数中调用.还有一个地方可以调用 Hook —— 就是自定义的 Hook 中，我们稍后会学习到。） 

同时,react提供了linter插件来自动执行这些规则.

## 自定义Hook:

有时候我们会想要在组件之间重用一些状态逻辑.目前为止,有两种主流方案来解决这个问题:

高级组件和render props.自定义Hook 可以让你在不增加组件的情况下达到同样的目的.

前面，我们介绍了一个叫 `FriendStatus` 的组件，它通过调用 `useState` 和 `useEffect` 的 Hook 来订阅一个好友的在线状态。假设我们想在另一个组件里重用这个订阅逻辑。

首先，我们把这个逻辑抽取到一个叫做 `useFriendStatus` 的自定义 Hook 里：

```js
import React, { useState, useEffect } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```

它将 `friendID` 作为参数，并返回该好友是否在线：

现在我们可以在两个组件中使用它：

```js
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}

function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

每个组件间的 state 是完全独立的。Hook 是一种复用*状态逻辑*的方式，它不复用 state 本身。事实上 Hook 的每次*调用*都有一个完全独立的 state —— 因此你可以在单个组件中多次调用同一个自定义 Hook。 

自定义 Hook 更像是一种约定而不是功能。如果函数的名字以 “`use`” 开头并调用其他 Hook，我们就说这是一个自定义 Hook。 `useSomething` 的命名约定可以让我们的 linter 插件在使用 Hook 的代码中找到 bug。 

其他Hook 

除此之外，还有一些使用频率较低的但是很有用的 Hook。比如，[`useContext`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usecontext) 让你不使用组件嵌套就可以订阅 React 的 Context。 

```js
function Example() {
  const locale = useContext(LocaleContext);
  const theme = useContext(ThemeContext);
  // ...
}
```

另外 [`useReducer`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usereducer) 可以让你通过 reducer 来管理组件本地的复杂 state。 

```js
function Todos() {
  const [todos, dispatch] = useReducer(todosReducer);
  // ...
```

