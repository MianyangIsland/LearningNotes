# Refs转发

Ref转发是一项将ref自动地通过组件传递到其一子组件的技巧.对于大多数应用的组件来说,这通常不是必须的.但对某些组件,尤其是可重用的组件库是很有用的.

## 转发refs到DOM组件:

考虑这个渲染原生DOM元素button的FacyButton组件

```js
function FancyButton(props) {
  return (
    <button className="FancyButton">
      {props.children}
    </button>
  );
}
```

React 组件隐藏其实现细节，包括其渲染结果。其他使用 `FancyButton` 的组件**通常不需要**获取内部的 DOM 元素 `button` 的 ref这很好，因为这防止组件过度依赖其他组件的 DOM 结构。 

虽然这种封装对类似 `FeedStory` 或 `Comment` 这样的应用级组件是理想的，但其对 `FancyButton` 或 `MyTextInput` 这样的高可复用“叶”组件来说可能是不方便的。这些组件倾向于在整个应用中以一种类似常规 DOM `button` 和 `input` 的方式被使用，并且访问其 DOM 节点对管理焦点，选中或动画来说是不可避免的。 

Ref转发是一个可选特性,其允许某些组件接收ref,并将其向下转递(换句话说,"转发"它给子组件).

在下面的示例中,FancyButton使用React.forwardRef来获取传递给它的ref,然后转发到它渲染的DOM button:

```js
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 你可以直接获取 DOM button 的 ref：
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

这样，使用 `FancyButton` 的组件可以获取底层 DOM 节点 `button` 的 ref ，并在必要时访问，就像其直接使用 DOM `button` 一样。 

对上述实示例发生情况的逐步解释:

1.通过调用React.createRef创建了一个React ref并将其赋值给ref变量.

2.通过指定ref为jsx属性,将其向下传递给<FancyButton ref={ref}>.

3.React传递ref给fowardRef内函数(props.ref)=> ....  ,作为其第二个参数.

4.向下转发该ref参数到<button ref={ref}>,将其指定为JSX属性.

5.当ref挂载完成,ref.current将指向<button>DOM节点.

### Warning:

**第二个参数ref只在使用React.forwardRef定义组件时存在.常规函数和class组件不接受ref参数,且props中也不存在ref.**

**Ref转发不仅限于DOM组件,也可以转发refs到class组件实例中.**

# 在高阶组件中转发refs

这个技巧对高阶组件(也被称为HOC)特别有用.

```js
function logProps(WrappedComponent) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props:', prevProps);
      console.log('new props:', this.props);
    }

    render() {
      return <WrappedComponent {...this.props} />;
    }
  }

  return LogProps;
}
```

"logProps"HOC透传所有props到其包裹的组件,所以渲染结果将是相同的.

```js
class FancyButton extends React.Component {
  focus() {
    // ...
  }

  // ...
}

// 我们导出 LogProps，而不是 FancyButton。
// 虽然它也会渲染一个 FancyButton。
export default logProps(FancyButton);
```

注意:refs将不会透传下去.这是因为ref不是props属性.就像key一样,其被React进行了特殊处理.如果你对HOC添加了ref,该ref将引用最外层的容器组件,而不是被包裹的组件.

这意味着用于我们FancyButton组件的refs实际上将被挂载到LogProps组件:

```js
import FancyButton from './FancyButton';

const ref = React.createRef();

// 我们导入的 FancyButton 组件是高阶组件（HOC）LogProps。
// 尽管渲染结果将是一样的，
// 但我们的 ref 将指向 LogProps 而不是内部的 FancyButton 组件！
// 这意味着我们不能调用例如 ref.current.focus() 这样的方法
<FancyButton
  label="Click Me"
  handleClick={handleClick}
  ref={ref}
/>;
```

我们可以使用 `React.forwardRef` API 明确地将 refs 转发到内部的 `FancyButton` 组件。`React.forwardRef` 接受一个渲染函数，其接收 `props` 和 `ref` 参数并返回一个 React 节点。 

```js
function logProps(Component) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props:', prevProps);
      console.log('new props:', this.props);
    }

    render() {
      const {forwardedRef, ...rest} = this.props;

      // 将自定义的 prop 属性 “forwardedRef” 定义为 ref
      return <Component ref={forwardedRef} {...rest} />;
    }
  }

  // 注意 React.forwardRef 回调的第二个参数 “ref”。
  // 我们可以将其作为常规 prop 属性传递给 LogProps，例如 “forwardedRef”
  // 然后它就可以被挂载到被 LogProps 包裹的子组件上。
  return React.forwardRef((props, ref) => {
    return <LogProps {...props} forwardedRef={ref} />;
  });
}
```

## 在DevTools中显示自定义名称

React.forwardRef接受一个渲染函数.React DevTools使用该杉树来决定为ref转发组件显示的内容.

```js
const WrappedComponent = React.forwardRef((props, ref) => {
  return <LogProps {...props} forwardedRef={ref} />;
});
```

如果你命名了渲染函数,DevTools也将包含其名称(例如"ForwardRefFunction"):

```js
const WrappedComponent = React.forwardRef(
  function myFunction(props, ref) {
    return <LogProps {...props} forwardedRef={ref} />;
  }
);
```

自己的示例:

```js
//App.js
import React , { createRef } from 'react';
import './App.css';
import FancyButton from './Context/FancyButton';

export default class App extends React.Component{
   ref = createRef();
   HandleClick=()=>{
     console.log(this.ref.current.value)
   }
  render()
  {
    return(
       <>
       <FancyButton ref={this.ref} ></FancyButton>
       <button onClick={this.HandleClick}>点击处理</button>
       </>
    )
  }
}

```

```js
//FancyButton.js
import React from 'react';
const FancyButton = React.forwardRef((props, ref) => (
    <input type={'text'} ref={ref} className="FancyButton" >
    </input>
  ));

  export default FancyButton
  
```

