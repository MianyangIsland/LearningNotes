# 不使用JSX

React并不强制要求使用JSX.当不想在构建环境中配置有关JSX编译时,不在React中使用JSX会更加方便.

每个JSX元素只是调用React.createElement(component,props,...children)的语法糖.因此使用JSX可以完成的任何事情都可以通过纯JavaScript完成.

```js
class Hello extends React.Component {
  render() {
    return <div>Hello {this.props.toWhat}</div>;
  }
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<Hello toWhat="World" />);
```

可以编写为不使用JSX的代码:

```js
class Hello extends React.Component {
  render() {
    return React.createElement('div', null, `Hello ${this.props.toWhat}`);
  }
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(React.createElement(Hello, {toWhat: 'World'}, null));
```

组件可以是字符串,也可以是React.Component的子类,它还能是一个普通的函数.

如果不想每次都键入React.createElement,通常的做法是创建快捷方式:

```js
const e = React.createElement;

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(e('div', null, 'Hello World'));
```

如果使用了React.createElement的快捷方式,那么在没有JSX的情况下使用React几乎一样方便.

