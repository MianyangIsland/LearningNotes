# Hook API 索引

## 基础Hook:

**useState**:

```js
const [state,setState] = useState(initialState)
```

返回一个state,以及更新state的函数.

在初始渲染期间,返回的状态(state)与传入的第一个参数(initialState)值相同.

setState函数用于更新state.它接收一个新的state值,并将组件的一次重新渲染加入队列.

```js
setState(newState);
```

在后续的重新渲染中,useState返回的第一个值将始终是更新后最新的state.

Warning:React会确保函数的标识是稳定的,并且不会在组件重新渲染时发生变化.这就是为什么可以安全地从useEffect或useCallback地依赖表中省略setState.

函数式更新:

如果新的state需要通过使用先前的state计算得出,那么可以将函数传递给setState.该函数将接收先前的state,并返回一个更新后的值.

```js
function Counter({initialCount}) {
  const [count, setCount] = useState(initialCount);
  return (
    <>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>Reset</button>
      <button onClick={() => setCount(prevCount => prevCount - 1)}>-</button>
      <button onClick={() => setCount(prevCount => prevCount + 1)}>+</button>
    </>
  );
}
```

**如果你的更新函数返回值与当前state完全相同,则随后的渲染会被完全跳过**.

> 注意
>
> 与 class 组件中的 `setState` 方法不同，`useState` 不会自动合并更新对象。你可以用函数式的 `setState` 结合展开运算符来达到合并更新对象的效果。
>
> ```
> const [state, setState] = useState({});
> setState(prevState => {
>   // 也可以使用 Object.assign
>   return {...prevState, ...updatedValues};
> });
> ```
>
> `useReducer` 是另一种可选方案，它更适合用于管理包含多个子值的 state 对象。

#### 

###  惰性初始state:

initialState参数只会在组件的初始渲染中起作用,后续渲染时会被忽略.如果初始state需要通过复杂计算获得,则可以传入一个函数,在函数中计算并返回初始的state,此函数只在初始渲染时被调用.

```js
const [state, setState] = useState(() => {
  const initialState = someExpensiveComputation(props);
  return initialState;
});

```

### 跳过state更新:

如果你更新State Hook 后的state与当前的state相同时,React将跳过子组件的渲染并且不会触发effect的执行.(React 使用 object.js比较算法来比较state).

需要注意的是,React可能仍需要在跳过渲染前渲染该组件.不过由于React不会对组件树的"深层"节点进行不必要的渲染,所以不必担心.如果你在渲染期间执行了高开销的计算,则可以使用useMemo来进行优化.

### 批量状态更新 :

React可以将多个状态更新分组到单个重新渲染中以提高性能。通常，这可以提高性能，不会影响应用程序的行为。 在React 18之前，只对React事件处理程序中的更新进行批处理。从React 18开始，批处理默认为所有更新启用。请注意，React确保来自几个不同的用户发起事件(例如，两次单击按钮)的更新总是被单独处理，而不是批处理。这可以防止逻辑错误。 在极少数情况下，您需要强制同步应用DOM更新，您可以将其封装在flushSync中。但是，这可能会影响性能，所以只在需要的地方这样做。

## useEffect:

```js
useEffect(didUpdate);
```

该Hook接收一个包含命令式,且可能有副作用代码的函数.

在函数组件主体内（这里指在 React 渲染阶段）改变 DOM、添加订阅、设置定时器、记录日志以及执行其他包含副作用的操作都是不被允许的，因为这可能会产生莫名其妙的 bug 并破坏 UI 的一致性。 使用 `useEffect` 完成副作用操作。赋值给 `useEffect` 的函数会在组件渲染到屏幕之后执行。

默认情况下,effect将在每轮渲染结束后执行,但你可以选择让它在只有某些值改变的时候才执行.

**清除effect**:通常,组件卸载时需要清除effect创建的诸如订阅或计时器ID等资源.要实现这印第安,useEffect函数需要返回一个清除函数.

```js
useEffect(() => {
  const subscription = props.source.subscribe();
  return () => {
    // 清除订阅
    subscription.unsubscribe();
  };
});
```

为防止内存泄露,清除函数会在组件卸载前执行.另外,如果组件多次渲染(通常如此),则在执行下一个effect之前,上一个effect就已被清除.在上述实例中,意味着组件的每一次更新都会创建新的订阅.

**effect的执行时机**:

与 `componentDidMount`、`componentDidUpdate` 不同的是，传给 `useEffect` 的函数会在浏览器完成布局与绘制**之后**，在一个延迟事件中被调用。这使得它适用于许多常见的副作用场景，比如设置订阅和事件处理等情况，因为绝大多数操作不应阻塞浏览器对屏幕的更新。 然而,并非所有effect都可以被延迟执行.然而，并非所有 effect 都可以被延迟执行。例如，一个对用户可见的 DOM 变更就必须在浏览器执行下一次绘制前被同步执行，这样用户才不会感觉到视觉上的不一致。（概念上类似于被动监听事件和主动监听事件的区别。） React为此提供了一个额外的useLayoutEffect Hook来处理.这类effect.它和useEffetc的解构相同,区别只是调用时机不同.此外，从 React 18 开始，当它是离散的用户输入（如点击）的结果时，或者当它是由 [`flushSync`](https://zh-hans.reactjs.org/docs/react-dom.html#flushsync) 包装的更新结果时，传递给 `useEffect` 的函数将在屏幕布局和绘制**之前**同步执行。这种行为便于事件系统或 [`flushSync`](https://zh-hans.reactjs.org/docs/react-dom.html#flushsync) 的调用者观察该效果的结果。 

Warning: 这只影响传递给useEffect的函数被调用时 ----  在这些effect中执行的更新仍会被推迟.这与useLayoutEffect不同,后者会立即启动该函数该函数并处理其中的更新.即使在useEffect被推迟到浏览器绘制之后的情况下,它也能保证在任何新的渲染前启动.React在开始性的更新前,总会先刷新之前的渲染的effect.

**effect的条件执行**:

默认情况下,effect会在每轮组件渲染完成后执行.这样的话,一旦effect的依赖发生变化,它就会被重新创建.然而,在某些场景下不需要在每次组件更新时都执行里面的函数.而是在source prop 改变时重新创建.

要实现这一点,可以给useEffect传递第二个参数,它是effect所依赖的值数组.

```js
useEffect(
  () => {
    const subscription = props.source.subscribe();
    return () => {
      subscription.unsubscribe();
    };
  },
  [props.source],
);
```

此时,只有当props.source改变后才会重新创建订阅.

Warning:  如果你要使用此优化方式请确保数组中包含了所有外部作用域中会发生变化且在effect中使用的变量,否则你的代码会引用到先前渲染中的旧变量.

如果想执行只运行一次的 effect（仅在组件挂载和卸载时执行），可以传递一个空数组（`[]`）作为第二个参数。这就告诉 React 你的 effect 不依赖于 props 或 state 中的任何值，所以它永远都不需要重复执行。这并不属于特殊情况 —— 它依然遵循输入数组的工作方式。 如果你传入了一个空数组（`[]`），effect 内部的 props 和 state 就会一直持有其初始值。尽管传入 `[]` 作为第二个参数有点类似于 `componentDidMount` 和 `componentWillUnmount` 的思维模式.

依赖项数组不会作为参数传给effect函数.虽然从概念上来说它表现为:所有effect函数中引用的值都应该出现在依赖项数组中.

## useContext:

```js
const value = useContext(MyContext);
```

接收一个context对象(React.createContext的返回值)并返回该context的当前值.当前的context值由上层组件中距离当前组件最近的<MyContext.Provider>的value prop决定.

当组件上层最近的<MyContext.Provider>更新时,该Hook会触发重渲染,并使用最新传递给MyContext provider的context value值.即使祖先使用React.memo或shouldComponentUpdate,也会在组件本身使用useContext时重新渲染.

别忘记 `useContext` 的参数必须是 *context 对象本身*：

- **正确：** `useContext(MyContext)`
- **错误：** `useContext(MyContext.Consumer)`
- **错误：** `useContext(MyContext.Provider)

调用了useContext的组件总会在context值变化时重新渲染.如果重渲染组件的开销较大，你可以 [通过使用 memoization 来优化](https://github.com/facebook/react/issues/15156#issuecomment-474590693)。 

如果你在接触 Hook 前已经对 context API 比较熟悉，那应该可以理解，`useContext(MyContext)` 相当于 class 组件中的 `static contextType = MyContext` 或者 `<MyContext.Consumer>`。

`useContext(MyContext)` 只是让你能够*读取* context 的值以及订阅 context 的变化。你仍然需要在上层组件树中使用 `<MyContext.Provider>` 来为下层组件*提供* context。

```js
const themes = {
  light: {
    foreground: "#000000",
    background: "#eeeeee"
  },
  dark: {
    foreground: "#ffffff",
    background: "#222222"
  }
};

const ThemeContext = React.createContext(themes.light);

function App() {
  return (
    <ThemeContext.Provider value={themes.dark}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return (
    <button style={{ background: theme.background, color: theme.foreground }}>
      I am styled by theme context!
    </button>
  );
}
```

