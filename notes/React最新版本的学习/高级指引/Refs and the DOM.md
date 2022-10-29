# Refs and the DOM

Refs提供了一种方法,允许访问DOM节点或在render方法中创建React元素.

在典型的 React 数据流中，props是父组件与子组件交互的唯一方式。要修改一个子组件，你需要使用新的 props 来重新渲染它。但是，在某些情况下，你需要在典型数据流之外强制修改子组件。被修改的子组件可能是一个 React 组件的实例，也可能是一个 DOM 元素。对于这两种情况，React 都提供了解决办法。

## 何时使用Refs

下面是几个适合使用refs的情况:

- 管理焦点，文本选择或媒体播放。
- 触发强制动画。
- 集成第三方 DOM 库。

避免使用 refs 来做任何可以通过声明式实现来完成的事情。

举个例子，避免在 `Dialog` 组件里暴露 `open()` 和 `close()` 方法，最好传递 `isOpen` 属性。

## 勿过度使用Refs 

## 创建Refs

Refs是使用React.createRef()创建的,并通过ref属性附加到React元素.在构造组件时,通常将Refs分配给实例属性,以便可以在整个组件中引用它们.

```js
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```

## 访问Refs

当ref被传递给render中的元素时,对该节点的引用可以在ref的current属性中被访问.

```js
const node = this.myRef.current;
```

ref的值根据节点的类型而有所不同:

当ref属性用于HTML元素时,构造函数中使用React.createRef()创建的ref接收底层DOM元素作为其current属性.

当ref属性用于自定义class组件时,ref对象接收组件的挂载实例作为其current属性.

**不能在函数组件上使用ref属性,因为它们没有实例**

以下例子说明了这些差异:

### 为DOM元素添加ref去存储DOM节点的引用:

```js
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    // 创建一个 ref 来存储 textInput 的 DOM 元素
    this.textInput = React.createRef();
    this.focusTextInput = this.focusTextInput.bind(this);
  }

  focusTextInput() {
    // 直接使用原生 API 使 text 输入框获得焦点
    // 注意：我们通过 "current" 来访问 DOM 节点
    this.textInput.current.focus();
  }

  render() {
    // 告诉 React 我们想把 <input> ref 关联到
    // 构造器里创建的 `textInput` 上
    return (
      <div>
        <input
          type="text"
          ref={this.textInput} />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

React会在组件挂载时给current属性传入DOM元素,并在组件卸载时传入null值.ref会在componentDidMount()或componentDidUpdate()生命周期钩子触发前更新.

## 为class组件添加Ref

如果我们想包装上面的CustomTextInput,来模拟它挂载之后立即被点击的操作,可是使用ref来获取这个自定义的input组件并手动调用它的focusTextInput()方法.

```js
class AutoFocusTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }

  componentDidMount() {
    this.textInput.current.focusTextInput();
  }

  render() {
    return (
      <CustomTextInput ref={this.textInput} />
    );
  }
}
```

请注意,这仅在CustomTextInput声明为class时才有效:

```js
class CustomTextInput extends React.Component {
  // ...
}

```

## Refs与函数组件

默认情况下,**不能在函数组件上使用ref属性**,因为它们没有实例:

```js
function MyFunctionComponent() {
  return <input />;
}

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }
  render() {
    // This will *not* work!
    return (
      <MyFunctionComponent ref={this.textInput} />
    );
  }
}
```

如果要在函数组件中使用ref,你可以使用forwardRef.

不管怎样,可以在函数组件内部使用ref属性,只要它指向一个DOM元素或class组件:

```js
function CustomTextInput(props) {
  // 这里必须声明 textInput，这样 ref 才可以引用它
  const textInput = useRef(null);

  function handleClick() {
    textInput.current.focus();
  }

  return (
    <div>
      <input
        type="text"
        ref={textInput} />
      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  );
}
```

## 将DOM Refs暴露给父组件:

在极少数情况下，你可能希望在父组件中引用子节点的 DOM 节点。通常不建议这样做，因为它会打破组件的封装，但它偶尔可用于触发焦点或测量子 DOM 节点的大小或位置。 

虽然你可以[向子组件添加 ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html#adding-a-ref-to-a-class-component)，但这不是一个理想的解决方案，因为你只能获取组件实例而不是 DOM 节点。并且，它还在函数组件上无效。

如果你使用 16.3 或更高版本的 React, 这种情况下我们推荐使用 [ref 转发](https://zh-hans.reactjs.org/docs/forwarding-refs.html)。**Ref 转发使组件可以像暴露自己的 ref 一样暴露子组件的 ref**。关于怎样对父组件暴露子组件的 DOM 节点，在 [ref 转发文档](https://zh-hans.reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components)中有一个详细的例子。

如果你使用 16.2 或更低版本的 React，或者你需要比 ref 转发更高的灵活性，你可以使用[这个替代方案](https://gist.github.com/gaearon/1a018a023347fe1c2476073330cc5509)将 ref 作为特殊名字的 prop 直接传递。

可能的话，我们不建议暴露 DOM 节点，但有时候它会成为救命稻草。注意这个方案需要你在子组件中增加一些代码。如果你对子组件的实现没有控制权的话，你剩下的选择是使用 [`findDOMNode()`](https://zh-hans.reactjs.org/docs/react-dom.html#finddomnode)，但在[`严格模式`](https://zh-hans.reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage) 下已被废弃且不推荐使用。

## 回调Refs

React也支持另一种设置refs的方式,称为"回调 refs".它能助你更精细地控制何时refs被设置和解除.

不同于传递createRef()创建的ref属性,你会传递一个函数.这个函数中接受React组件实例或HTML DOM元素作为参数,以使它们能在其他地方被存储和访问.

```js
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);

    this.textInput = null;

    this.setTextInputRef = element => {
      this.textInput = element;
    };

    this.focusTextInput = () => {
      // 使用原生 DOM API 使 text 输入框获得焦点
      if (this.textInput) this.textInput.focus();
    };
  }

  componentDidMount() {
    // 组件挂载后，让文本框自动获得焦点
    this.focusTextInput();
  }

  render() {
    // 使用 `ref` 的回调函数将 text 输入框 DOM 节点的引用存储到 React
    // 实例上（比如 this.textInput）
    return (
      <div>
        <input
          type="text"
          ref={this.setTextInputRef}
        />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

React 将在组件挂载时，会调用 `ref` 回调函数并传入 DOM 元素，当卸载时调用它并传入 `null`。在 `componentDidMount` 或 `componentDidUpdate` 触发前，React 会保证 refs 一定是最新的。 

可以在组件间传递回调形式的refs,就像可以传递通过React.createRef()创建的对象refs一样.

```
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    );
  }
}
```

在上面的例子中，`Parent` 把它的 refs 回调函数当作 `inputRef` props 传递给了 `CustomTextInput`，而且 `CustomTextInput` 把相同的函数作为特殊的 `ref` 属性传递给了 `<input>`。结果是，在 `Parent` 中的 `this.inputElement` 会被设置为与 `CustomTextInput` 中的 `input` 元素相对应的 DOM 节点。

## 过时API:String类型的Refs

## 关于回调refs的说明

如果ref回调函数是以内联函数的方式定义的,在更新过程中它会被执行两次,第一次传入参数null,然后第二次会传入参数DOM元素.这是因为在每次渲染时会创建一个新的函数实例,所以React清空旧的ref并且设置新的.通过将ref的回调函数定义成class的绑定函数的方式可以避免上述问题,但是在大多数情况下是无关紧要的.