一.redux是什么?

  1.redux是一个专门用于做状态管理的js库(不是react插件库).

  2.它可以用在react,angular,vue等项目中,但基本与react配合使用.

 3.作用:集中式管理react应用中对各组件共享的状态.

 二.什么情况下需要使用redux:

  1.某个组件的状态需要让其他组件可以随时拿到（共享）.

 2.一个组件需要改变宁一个组件的状态（通信）.

 3.总体原则：能不用就不用，如果不用比较吃力才考虑使用.

三.redux原理图：



 ![img](https://img-blog.csdnimg.cn/20210510095912805.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NzgzNjYw,size_16,color_FFFFFF,t_70) 

 三.redux的三个核心概念：

  1.action:  a.动作对象   b.包含2个属性:  type:  标识属性,值为字符串,唯一,必要属性.       data:数据属性,值类型任意可选属性.

  c.例子:  {type:'ADD_STUDENT',data:{name:'tom',age:18}}

2.reducer:

   a.用于初始化状态,加工状态.   b.加工时，根据旧的state和action,产生新的state的纯函数.

3.store:

a.将state,action,reducer联系在一起的对象.   

b.如何得到这个对象：

  1)  import {createStore}  from 'redux';

  2)  import reducer from './reducers';

  3)  const store = createStore(reducer);

c.此对象的功能：

  1) getState():得到state.

   2)  dispatch(action):分发action,触发reducer调用,产生新的state.

 3) subscribe(listener):注册监听，当产生新的state时，自动调用.

四.求和案例精简版：

  1.在src下建立redux文件夹,在redux里面建立store.js和count_reducer.js.

  a.store.js详情：

 ```javascript
import {createStore} from 'redux';//引入createStore,专门用于创建redux中最为核心的store
import countReducer from './count_reducer';//引入为Count组件服务的reducer

const store = createStore(countReducer);

export default store;//暴露store
 ```

b.count_reducer.js详情：

```javascript
import store from "./store";

/*
  1.该文件用于创建一个为Count组件服务的reducer,reducer的本质就是一个函数。
  2.reducer函数会接到两个参数,分别为：之前的状态（preState）,动作对象（action）.
 */
const initState = 0;
export default function countReducer(preState=initState,action)
{
   const {type,data}  = action;
   switch (type) {
       case 'increment':
           return preState + data;
        case 'decrement':
            return preState - data;
        default:
           return preState;
   }
}
```

三.相关API:

```javascript
 <h1>当前求和为:{store.getState()}</h1>//store.getState()用于获取store里面的状态
```

```javascript
componentDidMount(){
        //检测redux中状态的变化，只要变化，就调用redux.
        store.subscribe(()=>{
             this.setState({})
        })
    }            
    
    
    
    //在index,js里面检测，整个App都重新调用render,那么其子组件都会调用render.
store.subscribe(()=>{
    ReactDom.render(<App/>,document.getElementById('root'));
})
//store.subscribe用于检测store里面的状态改变.
```

备注：redux只负责管理状态,至于状态的改变驱动着页面的展示，要靠我们自己写.

2.新曾两个文件：

   1.count_action.js  :专门用于创建action对象.

  2.constant.js  放置容易写错action中的type.

a.count_action.js:

```javascript
/*
   该文件专门为Count组件生成action对象.
 */
import {INCREMENT,DECREMENT} from './constant'
export const  createIncrmentAction =(data)=>({type:INCREMENT,data})
export const createDecrmentAction =(data) => ({type:DECREMENT,data})
```



b.constant.js:

```javascript
/*
 该模块用于定义action对象中type类型的常量值
 */
const INCREMENT = 'increment';
const DECREMENT = 'decrement';
```

3.异步action:

  1.明确：延迟的动作不想交给组件自身,想交给action.

 2.何时需要异步action:想要对状态进行操作，但是具体的数据靠异步任务返回.

3.具体编码：

 1) 

```javascript
npm install redux-thunk
```

2)关于thunk的使用：

 ```javascript
//首先需要在redux中引入applyMiddleware;
import {createStore,applyMiddleware} from 'redux';
//然后引入thunk
import  thunk from 'redux-thunk';//引入redux-thunk,用于支持异步action;
//然后在createStore中作为参数使用：
const store = createStore(countReducer,applyMiddleware(thunk));

 ```

3).创建action的函数不在返回一般对象，而是一个函数，该函数写异步任务.

4)异步任务有结果，会分发一个同步的action去真正操作数据据.

4.备注：异步action不是必须要写的，完全可以自己等待异步任务的结果再去分发同步action.