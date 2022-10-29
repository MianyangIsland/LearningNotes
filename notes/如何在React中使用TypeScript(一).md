# 如何在React中使用TypeScript?

## 一.组件声明

在React中,组件的声明方式有两种:**函数组件**和**类组件**,来看看这两种类型的组件声明是如何定义TS类型的.

#### 1.类组件

类组件的定义形式有两种:React.Component<p,s={}>和React.PureComponent<p,s={} ss={}>,它们都是泛型接口,接收两个参数,第一个是props类型的定义,第二个是state类型的定义,这两个参数都不是必须的,没有时可以省略:

```js
interface IProps {
  name: string;
}

interface IState {
  count: number;
}

class App extends React.Component<IProps, IState> {
  state = {
    count: 0
  };

  render() {
    return (
      <div>
        {this.state.count}
        {this.props.name}
      </div>
    );
  }
}

export default App;
```

React.PureComponent<P,s={},ss={}>也是差不多的:

```js
class App extends React.PureComponent<IProps, IState> {}
```

`React.PureComponent`是有第三个参数的，它表示`getSnapshotBeforeUpdate`的返回值。 

那PureComponent和Component 的区别是什么呢？它们的主要区别是PureComponent中的shouldComponentUpdate 是由自身进行处理的，不需要我们自己处理，所以PureComponent可以在一定程度上提升性能。 

有时候可能会见到这种写法，实际上和上面的效果是一样的： 

```js
import React, {PureComponent, Component} from "react";

class App extends PureComponent<IProps, IState> {}

class App extends Component<IProps, IState> {}
```

那如果定义时候我们不知道组件的props的类型,只有在调用时才知道组件类型,该怎么办呢?这时泛型就发挥作用了:

```js
// 定义组件
class MyComponent<P> extends React.Component<P> {
  internalProp: P;
  constructor(props: P) {
    super(props);
    this.internalProp = props;
  }
  render() {
    return (
    	 <span>hello world</span>
    );
  }
}

// 使用组件
type IProps = { name: string; age: number; };

<MyComponent<IProps> name="React" age={18} />;          // Success
<MyComponent<IProps> name="TypeScript" age="hello" />;  // Error
```

2.函数组件:

通常情况下,函数组件是这样写的:

```js
interface IProps {
  name: string
}

const App = (props: IProps) => {
  const {name} = props;

  return (
    <div className="App">
      <h1>hello world</h1>
      <h2>{name}</h2>
    </div>
  );
}

export default App;
```

除此之外，函数类型还可以使用`React.FunctionComponent<P={}>`来定义，也可以使用其简写`React.FC<P={}>`，两者效果是一样的。它是一个泛型接口，可以接收一个参数，参数表示props的类型，这个参数不是必须的。它们就相当于这样：

```js
type React.FC<P = {}> = React.FunctionComponent<P>
```

最终的定义形式如下： 

```js
interface IProps {
  name: string
}

const App: React.FC<IProps> = (props) => {
  const {name} = props;
  return (
    <div className="App">
      <h1>hello world</h1>
      <h2>{name}</h2>
    </div>
  );
}

export default App;
```

当使用这种形式来定义函数组件时，props中默认会带有children属性，它表示该组件在调用时，其内部的元素，来看一个例子，首先定义一个组件，组件中引入了Child1和Child2组件： 

```js
import Child1 from "./child1";
import Child2 from "./child2";

interface IProps {
  name: string;
}
const App: React.FC<IProps> = (props) => {
  const { name } = props;
  return (
    <Child1 name={name}>
      <Child2 name={name} />
      TypeScript
    </Child1>
  );
};

export default App;
```

Child1组件结构如下： 

```js
interface IProps {
  name: string;
}
const Child1: React.FC<IProps> = (props) => {
  const { name, children } = props;
  console.log(children);
  return (
    <div className="App">
      <h1>hello child1</h1>
      <h2>{name}</h2>
    </div>
  );
};

export default Child1;
```

我们在Child1组件中打印了children属性，它的值是一个数组，包含Child2对象和后面的文本： 

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7e833cb140aa41c7a0c38f4216ff5902~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp) 

使用 React.FC 声明函数组件和普通声明的区别如下：

- React.FC 显式地定义了返回类型，其他方式是隐式推导的；
- React.FC 对静态属性：displayName、propTypes、defaultProps 提供了类型检查和自动补全；
- React.FC 为 children 提供了隐式的类型（ReactElement | null）。

那如果我们在定义组件时不知道props的类型，只有调用时才知道，那就还是用泛型来定义props的类型。对于使用function定义的函数组件：

```js
// 定义组件
function MyComponent<P>(props: P) {
  return (
  	<span>
    	{props}
    </span>
  );
}

// 使用组件
type IProps = { name: string; age: number; };

<MyComponent<IProps> name="React" age={18} />;          // Success
<MyComponent<IProps> name="TypeScript" age="hello" />;  // Error

```

如果使用箭头函数定义的函数组件,直接这样调用是错误的:

```js
const MyComponent = <P>(props: P) {
  return (
  	<span>
    	{props}
    </span>
  );
}
```

必须使用extends关键字来定义泛型参数才能被成功解析:

```js
const MyComponent = <P extends any>(props: P) {
  return (
  	<span>
    	{props}
    </span>
  );
}
```

## 二.React内置对象



