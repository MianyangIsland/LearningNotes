### Hook 能否覆盖 class 的所有使用场景？

React给Hook设定的目标是尽早覆盖class的所有使用场景.目前暂时还没有对应不常用的getSnapshotBeforeUpdate,getDerivedStateFromError和componentDidCatch生命周期的Hook等价写法.

## 从Class迁移到Hook:

生命周期方法要如何对应到Hook?

- `constructor`：函数组件不需要构造函数。你可以通过调用 [`useState`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usestate) 来初始化 state。如果计算的代价比较昂贵，你可以传一个函数给 `useState`。
- `getDerivedStateFromProps`：改为 [在渲染时](https://zh-hans.reactjs.org/docs/hooks-faq.html#how-do-i-implement-getderivedstatefromprops) 安排一次更新。
- `shouldComponentUpdate`：详见 `React.memo`.
- `render`：这是函数组件体本身。
- `componentDidMount`, `componentDidUpdate`, `componentWillUnmount`：useEffect Hook可以表达所有这些的组合。
- `getSnapshotBeforeUpdate`，`componentDidCatch` 以及 `getDerivedStateFromError`：目前还没有这些方法的 Hook 等价写法，但很快会被添加。

### 该如何使用Hook进行数据获取:

```js
import React, { useState, useEffect } from "react";
import ReactDOM from "react-dom";
import axios from 'axios';

function SearchResults() {
  const [data, setData] = useState({ hits: [] });
  const [query, setQuery] = useState('react');

  useEffect(() => {
    let ignore = false;

    async function fetchData() {
      const result = await axios('https://hn.algolia.com/api/v1/search?query=' + query);
      if (!ignore) setData(result.data);
    }

    fetchData();
    return () => { ignore = true; }
  }, [query]);

  return (
    <>
      <input value={query} onChange={e => setQuery(e.target.value)} />
      <ul>
        {data.hits.map(item => (
          <li key={item.objectID}>
            <a href={item.url}>{item.title}</a>
          </li>
        ))}
      </ul>
    </>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<SearchResults />, rootElement);

```

## 如何获取上一轮的props或state?

可以通过ref来手动实现:

```js
import React , {useRef , useState , useEffect}from "react";

function usePreviuos(value){
  const ref = useRef();
  useEffect(()=>{
    ref.current = value;
    console.log(ref.current)
  })
  console.log(ref.current);
  return ref.current
}

function Example() {

  const [count,setCount] = useState(0);
  const prevCount = usePreviuos(count);
    return (
      <>
      <h1>Now: {count}, before: {prevCount}</h1>
      <button onClick={()=>setCount(count + 1)}>点击+1</button>
      </>
    );
  }
export default Example;
```

## 有类似forceUpdate的东西吗?

如果前后两次的值相同,useState和useReducer Hook都会放弃更新.原地修改state并调用setState不会引起重新渲染.



通常,不应该在React中修改本地state.然而,作为一条出路,可以用一个增长的计数器来在state没变的时候依然强制一次重新渲染:

```js
  const [ignored, forceUpdate] = useReducer(x => x + 1, 0);

  function handleClick() {
    forceUpdate();
  }
```

可能的话尽量避免这种模式.

## 在依赖列表中省略函数是否安全?

一般来说,不安全.

```js
function Example({ someProp }) {
  function doSomething() {
    console.log(someProp);
  }

  useEffect(() => {
    doSomething();
  }, []); // 🔴 这样不安全（它调用的 `doSomething` 函数使用了 `someProp`）
}
```

要记住effect外部的函数使用了那些props和state很难.这也是为什么通常想要在effect内部去声明它所需要的函数.这样就能容易的看出那个effect依赖了组件作用域中的那些值:

```js
function Example({ someProp }) {
  useEffect(() => {
    function doSomething() {
      console.log(someProp);
    }

    doSomething();
  }, [someProp]); // ✅ 安全（我们的 effect 仅用到了 `someProp`）
}
```

如果这样任然没有用到组件作用域中的任何值,就可以安全地把它指定为[]:

```js
useEffect(() => {
  function doSomething() {
    console.log('hello');
  }

  doSomething();
}, []); // ✅ 在这个例子中是安全的，因为我们没有用到组件作用域中的 *任何* 值
```

如果指定了一个依赖表作为useEffect,useLayoutEffect,useMemo,useCallback或useImperativeHandle地最后一个参数,它必须包含回调中的所有值,并参与React数据流.这就包含props,state,以及任何有它们衍生而来的东西.

只有当函数(以及它所调用的函数)不引用props,state以及由它们衍生而来的值时,才能放心地把它们从依赖列表中省略.下面这个案例有一个bug:

```js
function ProductPage({ productId }) {
  const [product, setProduct] = useState(null);

  async function fetchProduct() {
    const response = await fetch('http://myapi/product/' + productId); // 使用了 productId prop
    const json = await response.json();
    setProduct(json);
  }

  useEffect(() => {
    fetchProduct();
  }, []); // 🔴 这样是无效的，因为 `fetchProduct` 使用了 `productId`
  // ...
}
```

**推荐的修复方案是把那个函数移动到你的 effect 内部**。这样就能很容易的看出来你的 effect 使用了哪些 props 和 state，并确保它们都被声明了： 

```js
function ProductPage({ productId }) {
  const [product, setProduct] = useState(null);

  useEffect(() => {
    // 把这个函数移动到 effect 内部后，我们可以清楚地看到它用到的值。
    async function fetchProduct() {
      const response = await fetch('http://myapi/product/' + productId);
      const json = await response.json();
      setProduct(json);
    }

    fetchProduct();
  }, [productId]); // ✅ 有效，因为我们的 effect 只用到了 productId
  // ...
}
```

如何实现shouldComponentUpdate?

可以用React.memo包裹一个组件来对它的props进行浅比较:

```js
const Button = React.memo((props) => {
  // 你的组件
});
```



