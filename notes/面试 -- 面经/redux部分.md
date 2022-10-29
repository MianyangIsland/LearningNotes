## 1.Redux定义

Redux是JavaScript应用的状态容器,提供可预测的状态管理.

当遇到如下问题时,建议开始使用Redux:

 你有很多数据随事件而变化.

 你希望状态有一个唯一确定的来源

 你发现将所有状态放在顶层组件中管理已不可维护.

### 2.为什么React要用Redux

React是DOM的一个抽象层(UI库),并不是Web应用的完整解决方案.因此react在涉及到数据的处理以及组件之间的通信时会比较复杂.

对于大型的复杂应用来说,这两方面恰恰是最关键的.因此,只用React,写大型应用比较吃力.

主要区别: 组件之间的通讯问题

不使用Redux:(图左边)

只能使用父子组件通讯,状态提升等React自带机制.

处理远方亲戚(非父子)关系的组件通讯时乏力.

组件之间的数据流混乱,出现BUG时难定位.

使用Redux(图右边)

集中式存储和管理应用的状态

处理组件通讯问题时,无视组件之间的层级关系

简化大型复杂应用中组件之间的通讯问题.

数据流清晰,易于定位BUG.

### 3.Redux三个核心概念

### 1.action

动作。

一个js对象，包含两个属性：

- **type**： 标识属性，值是字符串。多个type用action分开
- payload：数据属性，可选。表示本次动作携带的数据

actions 只是描述了**\*有事情发生了***这一事实，并没有描述应用如何更新 state。

特点：

- 只描述做什么
- JS 对象，必须带有 `type` 属性，用于区分动作的类型
- 根据功能的不同，可以携带额外的数据，配合该数据来完成相应功能

 ```js
{ type: 'add1' } 
{ type: 'addN', payload:10 }
 ```

### 2.reducer

- 一个**纯函数**

- 作用

  - 1. **初始化状态**
    2. **修改状态**

- 修改状态

  根据传入的旧状态和action，返回新状态

  公式：`(previousState, action) => newState`

  ```js
  const reducer = (state = 0, action) => {
    switch (action.type) {
    
    // 执行动作add  让state 加1
      case 'add':
        // 返回新的state
        return state + 1
      case 'addN':
        // 返回新的state
        return state + action.payload
      default:
        return state
    }
  }
  ```

### 3.store

store：仓库，Redux 的核心，整合 action 和 reducer

特点：

- **一个应用只有一个 store**
- 维护应用的状态，获取状态：`store.getState()`
- 创建 store 时**接收 reducer 作为参数**：`const store = createStore(reducer)`
- 发起状态更新时，需要分发 action：`store.dispatch(action)` 其他 API:这里一般使用`react-redux` 库代替):
   — 订阅(监听)状态变化： `const unSubscribe = store.subscribe(() => {})`

— 取消订阅状态变化： `unSubscribe()`

 ```js
import { createStore } from 'redux'
import reducer from './reducer'
export default createStore(reducer)
 ```

## 4.React-redux

 react-redux是 Redux 官方提供的 React 绑定库

1. React 和 Redux 是两个独立的库，两者之间职责独立。
2. Redux可以和其他的js库，框架一起使用，而并不专门用于react。
3. 为了实现在 React 中使用 Redux 进行状态管理 ，就需要一种机制，将这两个独立的库关联在一起。这时候就用到 React-Redux 这个绑定库了
4. 作用: 为 React 接入 Redux，实现在 React 中使用 Redux 进行状态管理。

 react-redux-基本使用

1. 安装 `npm i react-redux`
2. 使用
   1. 按redux的要求，创建好store, reducer,action等等
   2. 从react-redux中引入 provider, useSelector, useDispatch来 操作 redux

API:

Provider

- 用法：直接包装在根组件上。`<Provider store={store}>`
- 好处：相比react + redux，这样就不需要每个组件都引入store了

useSelector

- 用法：获取公共状态
- 好处：
  - 相比react + redux，不需要使用store.getState()了
  - state变化了，它会自动更新
- 格式：`const 状态 = useSelector(store的数据 => 你需要的部分)`

useDispatch

- 用法：派发action，修改数据
- 格式： `const dispatch = useDispatch(); dispatch(action)`

```js
import React from 'react'
import ReactDom from 'react-dom'
import App from './App'
import store from './store'
import { Provider } from 'react-redux'
ReactDom.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

需要使用和修改数据的组件中: 

```js
import React from 'react'

import { useDispatch, useSelector } from 'react-redux'

export default function Uncle () {
  // 拿到dispath修改数据
  const dispatch = useDispatch()

  // 获取数据
  // state:就是 redux中的数据
  const num = useSelector((state) => {
    return state
  })
  
  // ....
```

##  6.action creator

因为我们在创建 Action 的时候，有时候有些内容是固定了，我们可能会要在多个地方可以 `dispatch` 这个 Action，那么我们每次都需要写下面长长的一串 ： 

```js
dispatch({type: 'book/add', payload: '三国演义'})
dispatch({type: 'book/add', payload: '红楼梦'})
```

此时我们只需要定义一个函数，然后传入需要变化的参数就可以了。这种接收一些需要修改的参数，返回一个 Action 的函数在 Redux 中被称为 Action Creators（动作创建器）。

当我们使用 Action Creators 来创建 Action 之后，我们再想要修改 Store 的状态就变成了下面这样：

 ```js
// Action Creators
const addAction = (payload) => ({type: 'book/add', payload})

// 调用函数传入需要变化的参数
dispatch(addAction('三国演义'))
dispatch(addAction('红楼梦'))
 ```

## 7. Action Type的使用

在reducer和action createor中都用到了一个字符串的 action type,当项目过大,内容过多,就会导致这些字符串容易写错，并且不利于统一修改。
 **可以集中处理 action type，保持项目中 action type 的一致性** 处理方式：

1. 在 store 目录中创建 `actionTypes` 目录或者 `constants` 目录，集中处理
2. 使用**常量**来存储 action type。例如:

```js
export const SET_NAME = 'user/setName'
export const SUB_MORE = 'money/subMore'
复制代码
```

3.将项目中用到 action type 的地方替换为这些常量，从而保持项目中 action type 的一致性

## 8.redux-thunk 中间件

Redux 中间件执行时机：**在 dispatching action 和 到达 reducer 之间**。 

使用中间件：

- `dispatch(action) => 执行中间件代码 => reducer`。dispatch() 就是 中间件 封装处理后的 dispatch，但是，最终一定会调用 Redux 库自己提供的 dispatch 方法

![redux中间件-触发时机2.jpg](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a28baec450634fb29c2a24ba4783dfa6~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?) 

### redux-thunk-基本使用

`redux-thunk` 中间件可以处理`函数形式的 action`。因此，在函数形式的 action 中就可以执行异步操作代码，完成异步操作。 

```js
const action1 = async (dispatch) =>{
  const res = await 异步动作()
  dispatch({type: 'todos/add', payload: res.data})
}

dispatch(action1)
```



## 完整目录结构

```js
├── src
   ├── store                      # redux目录，一般约定叫store
   |     ├── index.js             # 定义并导出store. 其中会导入reducer
   |
   |     └── actions              # 多个模块的action
   |         ├── action1.js       # 模块1的 相关action creator
   |         ├── action2.js       # 模块2的 相关action creator
   |         └── index.js         # 合并  action creator
   |          
   |     └── reducers             # 多个模块的reducer
   |         ├── reducer1.js      # 模块1的reducer
   |         ├── reducer2.js      # 模块2的reducer
   |         └── index.js         # reducer的整体入口，会导入reducer1, reducer2
   |
   |     └── actionTypes
   |         ├── actionType1.js   # 模块1的actionType
   |         ├── actionType2.js   # 模块2的actionType
   |
   ├── index.js                   # 项目的入口文件，会导入并渲染App.js
   ├── App.js                     # 根组件，引入模块1 和模块2 组件
   |
   |── Pages 
         ├── 模块1.js       # 模块1 组件
         └── 模块2.js       # 模块2 组件
复制代码
```

```js
安装redux:              npm i redux
安装:                   npm i react-redux
thunk中间件:            npm i redux-thunk
查看redux的操作日志:     npm i redux-logger  

chrome开发者工具调试跟踪redux状态(需要先安装浏览器redux插件):
npm i redux-devtools-extension -D   

// 合并一次性下载
 npm i redux react-redux redux-thunk redux-devtools-extension
复制代码
```

`入口文件index.js`

```js
import App from './App'
import ReactDOM from 'react-dom'
import './styles/index.css'

// Provider包裹根组件App就不需要每个组件都引入store了
import { Provider } from 'react-redux'
import store from './store'
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
复制代码
```

`store/index.js`

```js
// applyMiddleware(中间件1,中间件2)使用中间件
import { createStore, applyMiddleware } from 'redux'

// composeWithDevTools()  redux调试工具
import { composeWithDevTools } from 'redux-devtools-extension'

// thunk中间件  dispath()能够传入函数执行异步请求
import thunk from 'redux-thunk'

// 合并后的reducer
import reducer from './reducers'

// 创建store 传入合并后的reducer
const store = createStore(reducer, composeWithDevTools(applyMiddleware(thunk)))

export default store
复制代码
```

`reducer/index.js`

```js
// channel和newList组件的reducer
import channel from './channel'
import newList from './newList'

// combineReducers 合并各个reducer
import { combineReducers } from 'redux'

const rootReducer = combineReducers({
  channel,
  newList
})
export default rootReducer
```

 

 

 

 



 

 

 

 

 

 

 

 

 

 