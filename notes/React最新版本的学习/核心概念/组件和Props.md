# 组件和Props:

组件允许将UI拆分为可复用的代码片段,并对每个片段进行对构思.从概念上类似于JavaScript函数.它接受任意的入参(即"props"),并返回用于描述页面展示内容的React元素.



### 函数组件与class组件:

定义组件最简单的方式就是编写JavaScript函数:

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

该函数是一个有效的 React 组件，因为它接收唯一带有数据的 “props”（代表属性）对象与并返回一个 React 元素。这类组件被称为“函数组件”，因为它本质上就是 JavaScript 函数。 

同时还可以使用ES6的class来定义组件:

```js
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

上述两个组件在 React 里是等效的。 

## 渲染组件:

React元素除了DOM标签之外,还可以是用户自定义的组件:

```js
const element = <Welcome name="Sara" />;
```

当React元素为用户自定义组件时,它会将JSX所接收的属性(attributes)以及子组件(children)转转为单个对象传递给组件,这个对象被称之为"props".

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

中间的过程:

1.调用ReactDOM.render()函数,并传入`<Welcome name="Sara" />` 作为参数。 

2.React调用Welcom组件,并将{name:'Sara'}作为props传入.

3.Welcome组件将<h1>hello,Sara</h1>元素作为返回值.

4.React DOM将DOM高效地更新为<h1>Hello,Sara</h1>

#### Warning:组件名必须以大写字母开头.

React会将以小写字母开头地组件视为原生DOM标签.

## 组合组件:

组件可以在其输出中引用其他组件.

### Props地只读性:

组件无论是使用函数声明还是通过class声明,都决不能修改自身的props.

纯函数:不会改变自己的入参.

React非常灵活,但它也有以恶搞严格的规则:

**所有React组件都必须像纯函数一样保护它们的props不被更改**.

