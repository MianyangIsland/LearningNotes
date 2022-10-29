## React组件间通信

### 父组件向子组件通信

父组件更新组件状态,通过props传递给子组件,子组件得到后进行更新.

### 子组件向父组件通信

子组件更新组件状态,通过回调函数的方式传递给父组件.

子组件调用父组件通过props传给它的函数更新父组件state,进而完成子组件向父组件通讯.

父组件:

```js
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';

//导入子组件
import Child from './child.js'; 

class App extends Component {
  constructor(props){
    super(props);
    this.state = {
      msg: '父组件初始msg'
    }
  }

  //父组件回调函数，更新state，进而更新父组件。
  callback=(msg)=>{
    // setState方法,修改msg参数,值是由子组件传过来。
    this.setState({msg});
  }

  render() {
    return (
      <div className="App">
        <p>子组件传值实验: {this.state.msg}</p>
        <Child callback={this.callback} ></Child>
      </div>
    );
  }
}

export default App;
```

子组件:

```js
import React from "react";

class Child extends React.Component{
	constructor(props){
      	super(props);
	    this.state={
	    	msg: '子组件msg传值'
	    }
    }
    //通过props调用回调函数传值
    trans=()=>{
        this.props.callback(this.state.msg);
    }
    render(){
        return(
            <div>
                <button onClick={this.trans}>激发trans事件，传值给父组件</button>
            </div>
        )
    }
}

export default Child;
```

### 兄弟组件之间的通信原理

React 项目中的两个兄弟组件若要传递数据,则首先需要将数据从一个兄弟组件传递给它们的共同父组件,然后再由这个公共父组件将数据传递给另一个兄弟组件.

### 非嵌套组件间通信

非嵌套组件: 就是没有任何包含关系的组件,包括兄弟组件以及不在同一个父级的非兄弟组件. 使用事件订阅,即一个发布者,一个或多个订阅者.

**安装 event**

```js
npm install event -save
```

新建Evt.js , 导入events;

```js
import {EventEmitter} from 'events';
export default new EventEmitter();   
```

发布者

通过emit 事件触发方法 , 发布订阅消息给订阅者.

```js
import React, { Component } from 'react';
import './App.css';
import PropTypes from 'prop-types';
import Custom1 from './Custom1.js';
import Custom2 from './Custom2.js';
import emitter from './Evt.js';

class App extends Component {

  constructor(){
    super();
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    //emit事件触发方法,过事件名称找对应的事件处理函数callCustom，将事件处理函数作为参数传入
    emitter.emit('callCustom', 'Hello 我来发消息了');
  }

  render() {    
    return(
      <div>
        <br/>
        <button onClick = {this.handleClick}>点击发布事件</button>
        <Custom1 />
        <Custom2 />
      </div>
     
    )  
  }
}

export default App;
```

**订阅者**

Custom1 和Custom2 

通过emitter.addListener(事件名称,函数名)方法,进行事件监听(订阅).

通过emitter.removeListener(事件名称,函数名)方法,进行事件销毁(取消订阅).

订阅者1

```js
import React from 'react';
import ReactDOM from 'react-dom';
import emitter from './Evt.js';

class Custom1 extends React.Component {

  constructor(){
    super();
    this.state= {
      msg:''
    }
  }

  componentDidMount () { //在组件挂载完成后声明一个自定义事件
    emitter.addListener('callCustom', (msg) => {
      this.setState({
        msg: 'Custom1收到消息--'+msg
      });
    })
  }

  componentWillUnmount () { //组件销毁前移除事件监听
    emitter.removeListener('callCustom', (msg) => {
      this.setState({
        msg: 'Custom1即将销毁此消息--'+ msg
      });
    })
  }

  //订阅者1消息显示
  render () {
     return(<p style={{color:'red'}}>
        {this.state.msg}
      </p>) 
  }
}
export default Custom1;
```

订阅者2

```js
import React from 'react';
import ReactDOM from 'react-dom';
import emitter from './Evt.js';

class Custom2 extends React.Component {

  constructor(){
    super();
    this.state= {
      msg:''
    }
  }

  componentDidMount () { //在组件挂载完成后声明一个自定义事件
    emitter.addListener('callCustom', (msg) => {
      this.setState({
        msg: 'Custom2收到消息--'+msg
      })
    })
  }

  componentWillUnmount () { //组件销毁前移除事件监听
    emitter.removeListener('callCustom', (msg) => {
      this.setState({
        msg: 'Custom2即将销毁此消息--'+ msg
      })
    })
  }

  //订阅者2消息显示
  render () {
    return(<p style={{color:'blue'}}>{this.state.msg}</p>) 
  }
}
export default Custom2;
```

 **跨级组件间的通信** -- React Context 的使用

一.概述

Context提供了一种在组件之间共享值的方式,而不必显示地通过组件树的逐层传递props.如果获取值和使用值的层级相隔很远,或者需要使用这个值的组件很多分散,则可以使用Context 来共享数据,避免使用大量重复的props来传递值.如果只是一个组件需要使用这个值,可以在产生这个值的位置生成这个组件,然后用props层层传递到组件实际展示的位置.

二.基本使用

1.自定义Context

```js
import React from 'react';
 
const ThemeContext = React.createContext('light');
 
export default ThemeContext;
```

2.在需要的位置使用Context 的 Provider

```js
import ThemeContext from './context/ThemeContext.js';
import ThemedButton from './ThemedButton.js';
import './App.css';
 
function App() {
  return (
    <ThemeContext.Provider value='dark'>
      <div className="App">
        <header className="App-header">
          <ThemedButton />
        </header>
      </div>
    </ThemeContext.Provider>
  );
}
 
export default App;
```

在组件的最外层使用了自定义Context的Provider , 传入value覆盖了默认值,之后子组件读到的ThemeContext 的值就是'dark'而不是默认值'light' . 如果Provider右value定义就会使用value的值(即使值是undefined , 即未传入value) , 只有当Provider未提供时才会使用定义时的默认值.

3.定义contextType , 使用获取到的Context上的值

```js
import React, { Component } from 'react';
import ThemeContext from "./context/ThemeContext.js";
 
class ThemedButton extends Component {
	static contextType = ThemeContext;
	render() {
		return <button>{this.context}</button>;
	}
}
 
export default ThemedButton;
```

ThemedButton声明了contextType是ThemeContext , 因此this.context的值就是**最接近**的ThemeContext提供的value,也就是'dark'.

**三.Context更新机制**

如果Provider的value属性是一个变量,当值变更时,消费这个Provider的子组件会触发更新机制,从而也实现更新.值是否变更使用Object.js的判断方法进行判断,所以不要使用动态生成的对象来作为context的值,如:

```js
<ThemeContext.Provider value={{ theme: 'light' }}><ThemeContext.Provider />
```

由于每次渲染的时候value所指向的值都是新生成的对象,内存指向总是不同,导致每次都出发消费这个Provider的组件更新.所以要用变量去承载这个值.

1.Context更新触发的声明周期

Context的更新不受父组件和自身shouldComponentUpdate的影响,经过的声明周期是render -> componentDidUpdate.

2.props更新和Context同时更新

props更新触发的生命周期是shouldComponentUpdate -> render -> componentDidUpdate , 如果碰上Context更新的话,会触发两者重合的生命周期,即render->componentDidUpdate . 所以如果有触发shouldComponentUpdate生命周期的需要,应避免和Context更新同时触发.

### 四.使用Consumer支持获取多个Context上的值.

**1.基本使用方法**

如果需要使用多个Context上的值,只有一个contextType就不够用了,需要使用Context提供的Consumer来获取对应Context上的值.

声明两个Context类型:

```js
import React from 'react';
 
const ThemeContext = React.createContext('light');
ThemeContext.displayName = 'ThemeContext';
 
export default ThemeContext;
```

```js
import React from 'react';
 
const UserContext = React.createContext('guest');
UserContext.displayName = 'UserContext';
 
export default UserContext;
```

使用Provider赋值

```js
import React, { Component } from 'react';
import ThemeContext from './context/ThemeContext.js';
import UserContext from './context/UserContext.js';
import ThemedButton from './ThemedButton.js';
import './App.css';
 
class App extends Component {
 
  constructor(props) {
    super(props);
  }
 
  render() {
    return (
        <ThemeContext.Provider value={'dark'}>
          <div className="App">
            <UserContext.Provider value={'user'}>
              <header className="App-header">
                <ThemedButton />
              </header>
            </UserContext.Provider>
          </div>
        </ThemeContext.Provider>
    );
  }
}
 
export default App;
```

在ThemeButton中使用这两个Context提供的值:

```js
import React, { Component } from 'react';
import ThemeContext from "./context/ThemeContext.js";
import UserContext from "./context/UserContext.js";
 
class ThemedButton extends Component {
 
	render() {
		return (
			<>
				<ThemeContext.Consumer>
					{theme => <div>{theme}</div>}
				</ThemeContext.Consumer>
				<UserContext.Consumer>
					{user => <div>{user}</div>}
				</UserContext.Consumer>
			</>
		);
	}
}
 
export default ThemedButton;
```

Consumer包裹范围内,可以使用对应Provider提供的值,用这种方式来实现一个组件中消费多个种类Provider提供的值.

**2.contextType 与 Consumer的比较**

contextType在一个组件中只能指向一个Context , 然后可以通过this.context在组件的任意位置使用对应Provider提供的值.缺点就是只能使用一个Context.Consumer可以使用多个,获取不同类型Provider的值.但是由于使用了标签的语法,,只能在render和相关的范围内使用.组件中应尽可能使用Consumer来获取Context上的值,吧contextType的位置留出来,避免后续必须要用到contextType的使用没有contextType可以用了.

**六.useContext的使用**

React在16.8版本中引入Hook,可以在不编写class的情况下使用state以及其他的React特性.useContext是其中一个原生的Hook, 是函数组件也可以使用Context , 而且支持使用多个不同类型的Context . 所以如果使用函数组件,可以使用useContext来支持使用多个类型的Context的值.

提供Context的值还是一样的代码:

```js
import React, { Component } from 'react';
import { ThemeProvider, THEMES } from './context/ThemeContext.js';
import UserContext from './context/UserContext';
import ThemeAndUser from './ThemeAndUser';
import './App.css';
 
class App extends Component {
 
  render() {
    return (
      <ThemeProvider theme={THEMES.LIGHT}>
        <UserContext.Provider value={'user'}>
          <div className="App">
            <header className="App-header">
              <ThemeAndUser />
            </header>
          </div>
        </UserContext.Provider>
      </ThemeProvider>
    );
  }
}
 
export default App;
```

使用useContext获取Context上的值:

```js
import { useContext } from 'react';
import { ThemeContext } from './context/ThemeContext';
import UserContext from './context/UserContext';
 
const ThemeAndUser = () => {
	const theme = useContext(ThemeContext);
	const user = useContext(UserContext);
	return (
		<>
			<div>{theme}</div>
			<div>{user}</div>
		</>
	);
};
 
export default ThemeAndUser;
```

### 七.小结

1. context用于提供全局可获取的内容,有可控的更新机制.但非必要的情况下尽量不要使用.
2. context不仅可以传值,还可以传递函数.可以把值和更新值的函数一起放在context中传递.
3. contextType只能指向一个Context,使用一个Context的值.使用Context.Consumer可以使用多个Context的值.但只能在render及其相关函数中.
4. useContext也可以使用多个Context的值,但是只能在函数组件中使用.





