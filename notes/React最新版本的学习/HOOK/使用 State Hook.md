# 使用State Hook:



**Hook和函数组件:**

例子代码:

```js
import React,{useState} from "react";
function Example(){
    const [count,setCount] = useState(0);

    return(
        <div>
            <p>You clicked {count} times</p>
            <button onClick={() => setCount(count + 1)}>
              Click me
            </button>
        </div>
    )
}
export default Example;
```



复习一下,React的函数组件是这样的:

```js
const Example = (props) => {
  // 你可以在这使用 Hook
  return <div />;
}


function Example(props) {
  // 你可以在这使用 Hook
  return <div />;
}
```

之前这样的组件叫做"无状态组件".但现在React官方为其引入了使用 React state的能力,所以叫他"函数组件".

### Hook是什么

Hook是一个特殊的函数,它可以让你"钩入"React的特性.例如,useState是允许你在React函数组件中添加state的Hook.

### 什么时候会用Hook?:

如果在编写函数组件并意识到需要向其添加一些state,以前的做法是必须将其转换为class,现在可以在现有的函数组件中使用Hook.

注意:在组件中有些特殊的规则,规定什么地方能使用Hook,生什么地方不能使用.建筑该Hook规则中学习.

## 声明State变量:

在class中,通过在构造函数中设置this.state为{count:0}来初始化count state为0.

```js
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }
```

在函数组件中,由于没有this,使用不能分配或读取this.state.我们直接在组件中调用useState Hook:

```js
import React, { useState } from 'react';

function Example() {
  // 声明一个叫 “count” 的 state 变量
  const [count, setCount] = useState(0);
```

## 调用useState方法的时候做了什么?:

它定义了一个'state变量'.变量的名字可以起为任意的.这是一种在函数调用时保存变量的方式----useState是一种新方法,它与class里面的this.state提供的功能完全相同.一般来说,在函数执行完退出以后变量就会消失,而state中的变量会被React保留.

## useState需要那些参数?:

sueState()方法里面唯一的参数就是初始state.不同于class的是,可以按照需要使用数字或字符串对其进行赋值,而不一定是对象,

## useState方法返沪的值是什么?

返回值:当前state以及更新state的函数.这就是写const [count,setCount] = useState()的原因.这与class里面this.state.count和this.setState类似,唯一的区别就是需要成对的获取它们.

所以:

```js
mport React, { useState } from 'react';

function Example() {
  // 声明一个叫 "count" 的 state 变量
  const [count, setCount] = useState(0);
```

这段代码就是声明了一个叫count的变量,然后把它的初值设为0,React会在重复渲染时记住它当前的值,并且提供最新的值给我们函数.可以通过调用setCount来更新当前的count.

## 读取State:

当我们想在class中显示当前的count,我们读取this.state.count:

```js
  <p>You clicked {this.state.count} times</p>
```

但在函数中,可以直接使用count:

```js
  <p>You clicked {count} times</p>
```

更新State:

在class中,我们需要调用this.setState()来更新count值:

```js
  <button onClick={() => this.setState({ count: this.state.count + 1 })}>
    Click me
  </button>
```

在函数中,已经有了setCount和count变量,使用不需要this:

```js
 <button onClick={() => setCount(count + 1)}>
    Click me
  </button>
```

## 使用多个state变量:

将 state 变量声明为一对 `[something, setSomething]` 也很方便，因为如果我们想使用多个 state 变量，它允许我们给不同的 state 变量取不同的名称： 

```js
function ExampleWithManyStates() {
  // 声明多个 state 变量
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: '学习 Hook' }]);
```

在以上组件中,我们有局部变量age,friut,yodos,并且我们可以单独更新它们:

```js
 function handleOrangeClick() {
    // 和 this.setState({ fruit: 'orange' }) 类似
    setFruit('orange');
  }
```

不必使用多个state变量,State变量可以很好的存储对象和数组,因此,任然可以将相关数据分为一组.然而,不像class中的this.setState,更新state变量总是替换它们而不是合并它们.