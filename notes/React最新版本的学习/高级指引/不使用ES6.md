# 不使用ES6:

###### 通常我们会使用js的class关键字来定义React组件:

```js
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

也可以使用create-react-class模块:

```js
var createReactClass = require('create-react-class');
var Greeting = createReactClass({
  render: function() {
    return <h1>Hello, {this.props.name}</h1>;
  }
});
```

ES6中的class与createReactClass()方法十分相似,但有以下几个区别值得注意.

## 声明默认属性

无论是函数组件还是class组件,都拥有defaultProps属性:

```js
class Greeting extends React.Component {
  // ...
}

Greeting.defaultProps = {
  name: 'Mary'
};
```

如果使用createReactClass()方法创建组件,那就需要在组件中定义getDefaultProps()函数:

```js
var Greeting = createReactClass({
  getDefaultProps: function() {
    return {
      name: 'Mary'
    };
  },

  // ...

});
```

## 初始化State

如果使用ES6的class关键字创建组件,可以通过给this.state赋值的方式来定义组件的初始state:

```js
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: props.initialCount};
  }
  // ...
}
```

如果使用createReactClass()方法来创建组件,需要提供一个单独的getInitialState方法,让其返回初始state:

```js
var Counter = createReactClass({
  getInitialState: function() {
    return {count: this.props.initialCount};
  },
  // ...
});
```

## 自动绑定

对于使用ES6的class关键字创建的React组件,组件中的方法遵循与常规ES6class相同的语法规则.这意味着这些方法不会自动绑定this到这个组件实例.需要在constructor中显示地调用.bind(this):

```js
class SayHello extends React.Component {
  constructor(props) {
    super(props);
    this.state = {message: 'Hello!'};
    // 这一行很重要！
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    alert(this.state.message);
  }

  render() {
    // 由于 `this.handleClick` 已经绑定至实例，因此我们才可以用它来处理点击事件
    return (
      <button onClick={this.handleClick}>
        Say hello
      </button>
    );
  }
}
```

如果使用createReactClass()方法创建组件,组件中地方法会自动绑定至实例,所以不需要像上面那样做:

```js
var SayHello = createReactClass({
  getInitialState: function() {
    return {message: 'Hello!'};
  },

  handleClick: function() {
    alert(this.state.message);
  },

  render: function() {
    return (
      <button onClick={this.handleClick}>
        Say hello
      </button>
    );
  }
});
```

这意味着,如果使用ES6 class关键字创建组件,在处理事件回调时就要多写一部分代码.但对于大型项目来说,这样可以提升运行效率.

如果你觉得上述写法很繁琐，那么可以尝试使用**目前还处于试验性阶段**的 Babel 插件 [Class Properties](https://babeljs.io/docs/plugins/transform-class-properties/)。 

```js
class SayHello extends React.Component {
  constructor(props) {
    super(props);
    this.state = {message: 'Hello!'};
  }
  // 警告：这种语法还处于试验性阶段！
  // 在这里使用箭头函数就可以把方法绑定给实例：
  handleClick = () => {
    alert(this.state.message);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Say hello
      </button>
    );
  }
}
```

请注意，上面这种语法**目前还处于试验性阶段**，这意味着语法随时都可能改变，也存在最终不被列入框架标准的可能。 

为了保险起见,以下三种方法都是可以的:

- 在 constructor 中绑定方法。
- 使用箭头函数，比如：`onClick={(e) => this.handleClick(e)}`。
- 继续使用 `createReactClass`。

