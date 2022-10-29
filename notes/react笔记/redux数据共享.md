 一.数据共享：

​     (1)定义一个Person组件，和Count组件通过redux共享数据.

​      (2)为Person组件编写：reducer,action,配置constant常量.

​      (3)重点：Person的reducer和Count的Reducer要使用combineReducers进行合并.合并后的总状态是一个对象!!!

​      (4)交给store的是总reducer,最后注意在组件中去除状态的时候，记得“取到位”.

#### combineReducers(reducers)

随着应用变得复杂，需要对 reducer 函数 进行拆分，拆分后的每一块独立负责管理 state 的一部分。

combineReducers 辅助函数的作用是，把一个由多个不同 reducer 函数作为 value 的 object，合并成一个最终的 reducer 函数，然后就可以对这个 reducer 调用 createStore。

合并后的 reducer 可以调用各个子 reducer，并把它们的结果合并成一个 state 对象。state 对象的结构由传入的多个 reducer 的 key 决定。

最终，state 对象的结构会是这样的：

```javascript
{
  reducer1: ...
  reducer2: ...
}
```

通过为传入对象的 reducer 命名不同来控制 state key 的命名。例如，你可以调用 combineReducers({ todos: myTodosReducer, counter: myCounterReducer }) 将 state 结构变为 { todos, counter }。

通常的做法是命名 reducer，然后 state 再去分割那些信息，因此你可以使用 ES6 的简写方法：combineReducers({ counter, todos })。这与 combineReducers({ counter: counter, todos: todos }) 一样。

Flux 用户使用须知

本函数可以帮助你组织多个 reducer，使它们分别管理自身相关联的 state。类似于 Flux 中的多个 store 分别管理不同的 state。在 Redux 中，只有一个 store，但是 combineReducers 让你拥有多个 reducer，同时保持各自负责逻辑块的独立性。

参数

reducers (Object): 一个对象，它的值（value） 对应不同的 reducer 函数，这些 reducer 函数后面会被合并成一个。下面会介绍传入 reducer 函数需要满足的规则。

之前的文档曾建议使用 ES6 的 import * as reducers 语法来获得 reducer 对象。这一点造成了很多疑问，因此现在建议在 reducers/index.js 里使用 combineReducers() 来对外输出一个 reducer。下面有示例说明。

返回值

(Function)：一个调用 reducers 对象里所有 reducer 的 reducer，并且构造一个与 reducers 对象结构相同的 state 对象。

注意:

本函数设计的时候有点偏主观，就是为了避免新手犯一些常见错误。也因些我们故意设定一些规则，但如果你自己手动编写根 redcuer 时并不需要遵守这些规则。

每个传入 combineReducers 的 reducer 都需满足以下规则：

所有未匹配到的 action，必须把它接收到的第一个参数也就是那个 state 原封不动返回。

永远不能返回 undefined。当过早 return 时非常容易犯这个错误，为了避免错误扩散，遇到这种情况时 combineReducers 会抛异常。

如果传入的 state 就是 undefined，一定要返回对应 reducer 的初始 state。根据上一条规则，初始 state 禁止使用 undefined。使用 ES6 的默认参数值语法来设置初始 state 很容易，但你也可以手动检查第一个参数是否为 undefined。

虽然 combineReducers 自动帮你检查 reducer 是否符合以上规则，但你也应该牢记，并尽量遵守。

二.纯函数：

 1.一类特别的函数：只要是同样的输入（实参），必定得到同样的输出（返回）.

 2.必须遵循以下一些约束：

​       1) 不得改写参数数据.

​        2)  不会产生任何副作用，例如网络请求，输入输出设备.

​       3) 不能调用Date.now()或者Math.random()等不纯的方法.

 3.redux的reducer函数不许是一个纯函数.

三.redux开发者工具:

  Redux DevTools  还需要下载  redux-devtools-extension  这个库.

然后在store.js中引入 redux-devtools-extension.

```
import {composeWithDevTools} from 'redux-devtools-extension'
```

