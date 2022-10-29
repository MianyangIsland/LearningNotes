# 二.React Hooks

**1. useState**:

默认情况下,React会为根据设置的state的初始值来自动推导state以及更新函数的类型:

如果已知state的类型,可以通过以下形式来自定义state的类型:

```js
const [count, setCount] = useState<number>(1)
```

如果初始值为null，需要显式地声明 state 的类型： 

```js
const [count, setCount] = useState<number | null>(null); 
```

如果state是一个对象，想要初始化一个空对象，可以使用断言来处理： 

```js
const [user, setUser] = React.useState<IUser>({} as IUser);
```

实际上，这里将空对象{}断言为IUser接口就是欺骗了TypeScript的编译器，由于后面的代码可能会依赖这个对象，所以应该在使用前及时初始化 user 的值，否则就会报错。 

**2. useEffect**

useEffect的主要作用就是处理副作用,它的第一个参数是一个函数,表示要清除副作用的操作,第二个参数是一组值,当这组值改变时,第一个参数的函数才会执行,这让我们可以控制何时运行函数来处理副作用:

```js
useEffect(
  () => {
    const subscription = props.source.subscribe();
    return () => {
      subscription.unsubscribe();
    };
  },
  [props.source]
);

```

当函数的返回值不是函数或者effect函数中未定义内容时,如下:

```js
useEffect(
    () => {
      subscribe();
      return null; 
    }
);

```

TypeScript就会报错.因为,useEffect的第一个参数只允许返回一个函数.

**3.useRef**

当使用 useRef 时，我们可以访问一个可变的引用对象。可以将初始值传递给 useRef，它用于初始化可变 ref 对象公开的当前属性。当我们使用useRef时，需要给其指定类型： 

```js
const nameInput = React.useRef<HTMLInputElement>(null)
```

这里给实例的类型指定为了input输入框类型。

当useRef的初始值为null时，有两种创建的形式，第一种：

```js
const nameInput = React.useRef<HTMLInputElement>(null)
nameInput.current.innerText = "hello world";
```

这种形式下，ref1.current是只读的（read-only），所以当我们将它的innerText属性重新赋值时会报以下错误： 

```js
Cannot assign to 'current' because it is a read-only property.
```

当初始值为null的第二种定义形式： 

```js
const nameInput = React.useRef<HTMLInputElement | null>(null);
```

这种形式下，nameInput.current就是可写的。不过两种类型在使用时都需要做类型检查： 

```js
nameInput.current?.innerText = "hello world";
```

### 6. useContext

useContext需要提供一个上下文对象，并返回所提供的上下文的值，当提供者更新上下文对象时，引用这些上下文对象的组件就会重新渲染： 

```js
const ColorContext = React.createContext({ color: "green" });

const Welcome = () => {
  const { color } = useContext(ColorContext);
  return <div style={{ color }}>hello world</div>;
};
```

在使用useContext时，会自动推断出提供的上下文对象的类型，所以并不需要我们手动设置context的类型。当前，我们也可以使用泛型来设置context的类型： 

```js
interface IColor {
	color: string;
}

const ColorContext = React.createContext<IColor>({ color: "green" });
```

### 7. useReducer

有时我们需要处理一些复杂的状态，并且可能取决于之前的状态。这时候就可以使用useReducer，它接收一个函数，这个函数会根据之前的状态来计算一个新的state。其语法如下：

```
const [state, dispatch] = useReducer(reducer, initialArg, init);
复制代码
```

来看下面的例子：

```
const reducer = (state, action) => {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

const Counter = () => {
  const initialState = {count: 0}
  const [state, dispatch] = useReducer(reducer, initialState);
  
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
    </>
  );
}
复制代码
```

当前的状态是无法推断出来的，可以给reducer函数添加类型，通过给reducer函数定义state和action来推断 useReducer 的类型，下面来修改上面的例子：

```
type ActionType = {
  type: 'increment' | 'decrement';
};

type State = { count: number };

const initialState: State = {count: 0}
const reducer = (state: State, action: ActionType) => {
  // ...
}
复制代码
```

这样，在Counter函数中就可以推断出类型。当我们视图使用一个不存在的类型时，就会报错：

```
dispatch({type: 'reset'});
// Error! type '"reset"' is not assignable to type '"increment" | "decrement"'
复制代码
```

除此之外，还可以使用泛型的形式来实现reducer函数的类型定义：

```
type ActionType = {
  type: 'increment' | 'decrement';
};

type State = { count: number };

const reducer: React.Reducer<State, ActionType> = (state, action) => {
  // ...
}
复制代码
```

其实dispatch方法也是有类型的：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/10417c4c14704f3eabde9e658535fbc7~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

可以看到，dispatch的类型是：React.Dispatch，上面示例的完整代码如下：

```
import React, { useReducer } from "react";

type ActionType = {
  type: "increment" | "decrement";
};

type State = { count: number };

const Counter: React.FC = () => {
  const reducer: React.Reducer<State, ActionType> = (state, action) => {
    switch (action.type) {
      case "increment":
        return { count: state.count + 1 };
      case "decrement":
        return { count: state.count - 1 };
      default:
        throw new Error();
    }
  };

  const initialState: State = {count: 0}
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
    </>
  );
};

export default Counter;
```

 

 

 

 