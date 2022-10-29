## React事件机制

### 一.是什么?

React基于浏览器的事件机制自身实现了一套事件机制,包括事件注册,事件的合成,事件冒泡,事件派发等.

在React中这套事件机制被称之为合成事件.

**合成事件**

合成事件是React模拟原生DOM事件所有能力的一个事件对象,即浏览器原生事件的跨浏览器包装器.

根据W3C规范来定义合成事件,兼容所有浏览器,拥有与浏览器原生事件相同的接口,例如:

```js
const button = <button onClick={handleClick}>按钮</button>
```

如果想要获得原生DOM事件,可以通过e.nativeEvent属性获取.

```js
const handleClick = (e) => console.log(e.nativeEvent);;
const button = <button onClick={handleClick}>按钮</button>
```

从上面可以看到React事件和原生事件也非常相似,但也有一定的区别:

事件名称命名方式不同:

```js
// 原生事件绑定方式
<button onclick="handleClick()">按钮命名</button>
      
// React 合成事件绑定方式
const button = <button onClick={handleClick}>按钮命名</button>
```

事件处理函数书写不同

```js
// 原生事件 事件处理函数写法
<button onclick="handleClick()">按钮命名</button>
      
// React 合成事件 事件处理函数写法
const button = <button onClick={handleClick}>按钮命名</button>
```

虽然onclick看似绑定到DOM元素上,但实际并不会把事件代理函数直接绑定到真实的节点上,而是把所有的事件绑定到结构的最外层,使用一个统一的事件去监听.

这个事件监听器上维持了一个映射来保存所有组件内部的事件监听和处理函数.当组件挂在或卸载时,只是在这个统一的事件监听器上插入或删除一些对象.

当事件发生时,首先被这个统一的事件监听器处理,然后在映射里找到真正的事件处理函数并调用.这样做简化了事件处理和回收机制,效率也有很大提升.

### 二.执行顺序

关于React合成事件与原生事件执行顺序,可以看看下面一个例子:

```js
import  React  from 'react';
class App extends React.Component{

  constructor(props) {
    super(props);
    this.parentRef = React.createRef();
    this.childRef = React.createRef();
  }
  componentDidMount() {
    console.log("React componentDidMount！");
    this.parentRef.current?.addEventListener("click", () => {
      console.log("原生事件：父元素 DOM 事件监听！");
    });
    this.childRef.current?.addEventListener("click", () => {
      console.log("原生事件：子元素 DOM 事件监听！");
    });
    document.addEventListener("click", (e) => {
      console.log("原生事件：document DOM 事件监听！");
    });
  }
  parentClickFun = () => {
    console.log("React 事件：父元素事件监听！");
  };
  childClickFun = () => {
    console.log("React 事件：子元素事件监听！");
  };
  render() {
    return (
      <div ref={this.parentRef} onClick={this.parentClickFun}>
        <div ref={this.childRef} onClick={this.childClickFun}>
          分析事件执行顺序
        </div>
      </div>
    );
  }
}
export default App;
```

输出顺序为:

```js
原生事件: 子元素 DOM 事件监听
原生事件: 父元素 DOM 事件监听
React事件: 子元素事件监听
React事件: 父元素事件监听
原生事件: documnet DOM 事件监听
```

可以得出以下结论:

React所有事件都挂载在document对象上.

当真实DOM元素触发事件,会冒泡到document对象后,再处理React事件.

所以会先执行原生事件,然后处理React事件

最后真正执行document上挂在的事件.

![图片](https://mmbiz.qpic.cn/mmbiz_png/gH31uF9VIibRjRBibQvZI8Q6u0v507nnZWvULQAvicDQcGKU4yUvn1ZjTibQyuRtJYYu0yg0mPIDrEN1WFG33J0dvg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1) 

所以想要阻止不同事件段的冒泡行为对应使用不同的方法,对应如下:

阻止合成事件间的冒泡,用e.stopPropagation().

阻止合成事件与最外层document上的事件间的冒泡,用e.nativeEvent.stopImmediatePropagation().

阻止合成事件与最外层document上的原生事件上的冒泡,通过e.target来避免.

```js
document.body.addEventListener('click', e => {   
    if (e.target && e.target.matches('div.code')) {  
        return;    
    }    
    this.setState({   active: false,    });   }); 
}
```

## 三.总结

React事件机制总结如下:

React上注册的事件最终会绑定在document这个DOM上,而不是React组件对应的DOM(减少内存开销就是因为所有的事件都绑定在document上,其他节点没有绑定事件).

React自身实现了一套事件冒泡机制,所以这也就是为什么我们event.stopPropagation()无效的原因.

React通过队列的形式,从触发的组件向父组件回溯,然后调用他们JSX中定义的callback.

React有一套自己的合成事件SyntheticEvent.

## setState执行机制

### 一.是什么

一个组件的显示形态可以由数据状态和外部参数所决定,而数据状态就是state.

当需要修改里面的值的状态需要通过调用setState来改变,从而达到更新组件内部数据的作用.

```js
import React, { Component } from 'react'

export default class App extends Component {
    constructor(props) {
        super(props);

        this.state = {
            message: "Hello World"
        }
    }

    render() {
        return (
            <div>
                <h2>{this.state.message}</h2>
                <button onClick={e => this.changeText()}>面试官系列</button>
            </div>
        )
    }

    changeText() {
        this.setState({
            message: "JS每日一题"
        })
    }
}
```

通过点击按钮触发onclick事件,执行this.setState方法更新state状态,然后重新执行render函数,从而导致页面的视图更新.

如果直接修改state的状态,如下:

```js
changeText() {
    this.state.message = "JS每日一题";
}
```

我们会发现页面并不会有任何反应,但是state的状态是已经发生了变化.

这是因为React并不像vue2中调用Object.defineProperty数据想要或者vue3调用proxy监听数据的变化.

必须通过setState方法来告知react组件state已经发生了变化.

关于state方法的定义是从React.Component中继承,定义的源码如下:

```js
Component.prototype.setState = function(partialState, callback) {
  invariant(
    typeof partialState === 'object' ||
      typeof partialState === 'function' ||
      partialState == null,
    'setState(...): takes an object of state variables to update or a ' +
      'function which returns an object of state variables.',
  );
  this.updater.enqueueSetState(this, partialState, callback, 'setState');
};
```

从上面可以看到setState第一个参数可以是一个对象,或者是一个函数,而第二个参数是一个回调函数,用于可以实时的获取到更新之后的数据.

### 二.更新类型

在使用setState更新数据的时候,setState的更新类型分成:

异步更新

同步更新

## 异步更新

先举一个例子:

```js
changeText() {
  this.setState({
    message: "你好啊"
  })
  console.log(this.state.message); // Hello World
}
```

从上面可以看到,最终打印结果为Hello world , 并不能在执行完setState之后立马拿到最新的state的结果.

如果想要立刻获取更新后的值,在第二个参数的回调中更新后会执行

```js
changeText() {
  this.setState({
    message: "你好啊"
  }, () => {
    console.log(this.state.message); // 你好啊
  });
}
```

### 同步更新

同样先给出一个在setTimeout中更新的例子:

```js
changeText() {
  setTimeout(() => {
    this.setState({
      message: "你好啊
    });
    console.log(this.state.message); // 你好啊
  }, 0);
}
```

再来举一个原生DOM事件的例子:

```js
componentDidMount() {
  const btnEl = document.getElementById("btn");
  btnEl.addEventListener('click', () => {
    this.setState({
      message: "你好啊"
    });
    console.log(this.state.message); // 你好啊
  })
}
```

小结:

在组件生命周期或React合成事件中,setState是异步的.

在setTimeout或者原生dom事件中,setState是同步.

### 三.批量更新

同样先给出一个例子:

```js
handleClick = () => {
    this.setState({
        count: this.state.count + 1,
    })
    console.log(this.state.count) // 1

    this.setState({
        count: this.state.count + 1,
    })
    console.log(this.state.count) // 1

    this.setState({
        count: this.state.count + 1,
    })
    console.log(this.state.count) // 1
}
```

点击按钮触发事件,打印的都是1,页面显示count的值为2.

对同一个值进行多次setState,setState的批量更新策略会对其进行覆盖,取最后以此的执行结果.

上述的例子,实际等价于如下:

```js
Object.assign(
  previousState,
  {index: state.count+ 1},
  {index: state.count+ 1},
  ...
)
```

由于后面的数据会覆盖前面的更改,所以最终只加了一次.

如果是下一个state依赖前一个state的话,推荐给setState一个参数传入一个function,如下:

```js
onClick = () => {
    this.setState((prevState, props) => {
      return {count: prevState.count + 1};
    });
    this.setState((prevState, props) => {
      return {count: prevState.count + 1};
    });
}
```

而在setTimeout或者原生dom事件中,由于是同步的操作,所以并不会进行覆盖现象.

