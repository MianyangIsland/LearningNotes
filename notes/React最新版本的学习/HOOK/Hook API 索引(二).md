# 额外的Hook

## useReducer:

```js
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

useState的替代方案.它接收一个形如( state , action ) => newState 的reducer,并返回当前的state以及与其配套的dispatch方法.

在某些场景下,useReducer会比useState更适用,例如state逻辑较复杂且包含多个子值,或者下一个state依赖于之前的state等.并且使用useReducer还能给那些会触发深更新的组件做性能优化,因为可以向子组件传递dispatch而不是回调函数.

```js
const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

分为父子组件:

```js
//父组件
import React , {useReducer}from "react";
import Count from "./Count";

const initialState = {count:0};

function reducer(state,action){
    switch (action.type){
        case 'increment':
            return {count:state.count + 1 };
        case 'decrement':
            return {count:state.count - 1 };
        default:
            throw new Error();
    }
}

function Example() {
    const [state,dispatch] = useReducer(reducer,initialState);
    return (
      <>
       <Count data={state.count} Control={dispatch}/>
      </>
    );
  }
export default Example;


//子组件
export default function Count(props){
    return(
        <>
        Count:{props.data}
       <button onClick={()=>props.Control({type:'increment'})}>+</button>
       <button onClick={()=>props.Control({type:'decrement'})}>-</button>
        </>
    )
}
```

Warning:

React会确保dispatch函数的标识是稳定的,并且不会在组件重新渲染时改变.因此可以安全地从useEffect或useCallback地依赖列表中省略.

**指定初始state**:

有两种不同初始化 `useReducer` state 的方式，你可以根据使用场景选择其中的一种。将初始 state 作为第二个参数传入 `useReducer` 是最简单的方法： 

```js
  const [state, dispatch] = useReducer(
    reducer,
    {count: initialCount}
  );
```

Warning:

React不使用state = initialState这一由Redux推广开来的参数约定.有时候初始值依赖于props,因此需要在调用Hook时指定.如果你特别喜欢上述的参数约定,可以通过调用useReducer(reducer,undefiend,reducer)来模拟Redux的行为.

**惰性初始化**:

可以选择惰性创建初始state.为此,需要讲init函数作为useReducer的第三个参数传入,这样初始state将被设置为init(initialArg).这么做可以将用于计算state的逻辑提取到reducer外部,这也为将来对重置state的action做处理提供了便利:

```js
function init(initialCount) {
  return {count: initialCount};
}

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    case 'reset':
      return init(action.payload);
    default:
      throw new Error();
  }
}

function Counter({initialCount}) {
  const [state, dispatch] = useReducer(reducer, initialCount, init);
  return (
    <>
      Count: {state.count}
      <button
        onClick={() => dispatch({type: 'reset', payload: initialCount})}>
        Reset
      </button>
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}

```

**跳过dispatch:**如果Reducer Hook 的返回值于当前值与当前state相同,React将跳过子组件的渲染及副作用的执行.(React 使用 object.js 比较算法来比较 state)

需要注意的是,React可能仍需要在跳过渲染前再次渲染该组件.不过由于React不会对组件树的"深层"节点进行不必要的渲染,所以不必担心.

## useCallback:

```js
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

返回一个memoized回调函数.

把内联回调函数及依赖项数组作为参数传入useCallback,它将返回该回调函数的memoized版本.该回调函数仅在某个依赖项改变时才会更新.当把回调函数传递给经过优化的并使用引用相等性去避免非必要渲染(例如 shouldComponentUpdate)的子组件时,它将非常有用.

useCallback(fn,deps)相当于useMemo(()=>fn,deps).

Warning:依赖项数组不会作为参数传给回调函数.虽然从概念上来说它表现为:所有回调函数中引用的值都应该出现在依赖项数组中.

注:memoized函数有记住函数的计算解构,提高函数的计算性能的功能.

## useMemo:

```js
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

返回一个memoized值.

把"创建"函数和依赖项数组作为参数传入useMemo,它仅会在某个依赖改变时才重新计算memoized值,.这种优化有助于避免在每次渲染时都进行高开销的计算.

传入useMemo的函数会在渲染期间执行.不要在这个函数内部执行不应该在渲染期间内执行的操作,诸如副作用这类的操作属于useEffect的适用范畴,而不是useMemo.

如果没有提供依赖项数组,useMemo在每次渲染时都会计算新的值.可以把useMemo作为性能优化的手段,但不要把它当成语义上的保证.

Warning:依赖项数组不会作为参数传给"创建"函数.虽然从概念上来说它表现为:所有"创建"函数中引用的值都应该出现在依赖项数组中.

## useRef:

```js
const refContainer = useRef(initialValue);
```

useRef返回一个可变的ref对象,其.current属性被初始化为传入的参数(initialValue).返回的ref对象在组件的整个生命周期内持续存在.

常见的用例是命令式地访问子组件:

```js
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

本质上,useRef就像是可以在其.current属性中保存一个可变值的"盒子".如果将ref对象<div  ref={myRef} />形式传入组件,则无论该节点如何改变,React都会将ref对象的.current属性设置为相应的DOM节点.

然而,useRef()比ref属性更有用.它可以很方便的保存任何可变值,其类似于在class中使用实例字段的方式.这是因为它创建的是一个普通js对象.而useRef()和自建一个{current:...}对象的唯一区别是,useRef会在每次渲染时返回同一个ref对象.当ref对象内容发生变化时,useRef并不会通知你.变更.current属性不会引发组件重新渲染.如果想要在React绑定或解绑DOM节点的ref时运行某些代码,则需要使用回调ref来实现.

## **useImperativeHandle:

```js
useImperativeHandle(ref, createHandle, [deps])
```

`useImperativeHandle` 可以让你在使用 `ref` 时自定义暴露给父组件的实例值。在大多数情况下，应当避免使用 ref 这样的命令式代码。`useImperativeHandle` 应当与 [`forwardRef`](https://zh-hans.reactjs.org/docs/react-api.html#reactforwardref) 一起使用： 

```js
function FancyInput(props, ref) {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));
  return <input ref={inputRef} ... />;
}
FancyInput = forwardRef(FancyInput);
```

在本例中，渲染 `<FancyInput ref={inputRef} />` 的父组件可以调用 `inputRef.current.focus()`。

## **useLayoutEffect:

其函数签名与useEffect相同,但它会在所有的DOM变更之后同步调用effect.可以使用它来读取DOM布局并同步触发重渲染.在浏览器执行绘制之前,useLayoutEffect内部的更新计划将被同步刷新.

**尽可能使用标准useEffect以避免阻塞视觉更新**

Warning: useLayoutEffect与componnetDidMount,componentDidUpdate的调用阶段是一样的.但是,React推荐一开始先用useEffect,只有当它出现问题的时候再尝试使用useLayoutEffect.

如果使用服务端渲染无论useLayoutEffect还是useEffect都无法在js代码加载完成之前执行.这就是为什么在服务端渲染组件中引入useLayoutEffect代码时会触发React警告.解决这个问题,需要将代码逻辑移至useEffect中(如果首次渲染不需要这段逻辑的情况下),或是将该组件延迟到客户端渲染完成后再显示(如果直到useLayoutEffect执行之前HTML都显示错乱的情况下).

若要从服务端渲染的HTML中排除依赖布局effect的组件,可与通过使用showChild && <Child/>进行条件渲染,并使用useEffect(()=> {setshowChild(true);},[])延迟展示组件.这样,在客户端渲染完成之前,UI就不会像之前那也显示错乱了.

## useDebugValue:

```js
useDebugValue(value)
```

useDebugValue可用于在React开发者工具中显示自定义hook的标签.

