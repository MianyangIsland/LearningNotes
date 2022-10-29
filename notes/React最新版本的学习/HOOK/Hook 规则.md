# Hook 规则:

Hook 本质就是JS函数,但是在使用它时需要遵循两条规则.React为我们提供了一个 [linter 插件](https://www.npmjs.com/package/eslint-plugin-react-hooks)来强制执行这些规则：

补充: (linter插件):

这个ESLint插件强制了钩子规则。 它是React的Hooks API的一部分。

```js
npm install eslint-plugin-react-hooks --save-dev
```

然后扩展推荐的eslint配置: 

```
{
  "extends": [
    // ...
    "plugin:react-hooks/recommended"
  ]
}
```



自定义配置 

如果你想要更细粒度的配置，你可以在ESLint配置文件中添加如下代码片段:

```js
{
  "plugins": [
    // ...
    "react-hooks"
  ],
  "rules": {
    // ...
    "react-hooks/rules-of-hooks": "error",
    "react-hooks/exhaustive-deps": "warn"
  }
}
```

...............

## 只在最顶层使用Hook:

**不要在循环,条件或嵌套函数中调用hook:**,确保总是在你的React函数的最顶层以及任何return 之前调用它们.遵守这条规则,你就能确保在每一次渲染中都按照同样的顺序被调用.这让React能够在多次的useState和useEffect调用之间保持hook状态的正确.

## 只在React函数中调用Hook:

不要在普通的js函数中调用,可以:

在React的函数组件中调用Hook,在自定义Hook中调用其他Hook.

## 说明:

在单个组件中使用多个 tate Hook或 Effect Hook,那么 React 怎么知道哪个 state 对应哪个 `useState`？ 答案是React靠的是Hook调用的顺序.

Hook的调用顺序在每次渲染中都是相同的,所以它能够正常工作.

```js
// ------------
// 首次渲染
// ------------
useState('Mary')           // 1. 使用 'Mary' 初始化变量名为 name 的 state
useEffect(persistForm)     // 2. 添加 effect 以保存 form 操作
useState('Poppins')        // 3. 使用 'Poppins' 初始化变量名为 surname 的 state
useEffect(updateTitle)     // 4. 添加 effect 以更新标题

// -------------
// 二次渲染
// -------------
useState('Mary')           // 1. 读取变量名为 name 的 state（参数被忽略）
useEffect(persistForm)     // 2. 替换保存 form 的 effect
useState('Poppins')        // 3. 读取变量名为 surname 的 state（参数被忽略）
useEffect(updateTitle)     // 4. 替换更新标题的 effect

// ...
```

只要 Hook 的调用顺序在多次渲染之间保持一致，React 就能正确地将内部 state 和对应的 Hook 进行关联。 

但如果我们将一个 Hook (例如 `persistForm` effect) 调用放到一个条件语句中会发生什么呢？ 

```js
 // 🔴 在条件语句中使用 Hook 违反第一条规则
  if (name !== '') {
    useEffect(function persistForm() {
      localStorage.setItem('formData', name);
    });
  }
```

在第一次渲染中 `name !== ''` 这个条件值为 `true`，所以我们会执行这个 Hook。但是下一次渲染时我们可能清空了表单，表达式值变为 `false`。此时的渲染会跳过该 Hook，Hook 的调用顺序发生了改变： 

```js
useState('Mary')           // 1. 读取变量名为 name 的 state（参数被忽略）
// useEffect(persistForm)  // 🔴 此 Hook 被忽略！
useState('Poppins')        // 🔴 2 （之前为 3）。读取变量名为 surname 的 state 失败
useEffect(updateTitle)     // 🔴 3 （之前为 4）。替换更新标题的 effect 失败
```

React 不知道第二个 `useState` 的 Hook 应该返回什么。React 会以为在该组件中第二个 Hook 的调用像上次的渲染一样，对应的是 `persistForm` 的 effect，但并非如此。从这里开始，后面的 Hook 调用都被提前执行，导致 bug 的产生。 

**这就是为什么Hook需要在我们组件的最顶层调用**.如果我们想要有条件地执行一个effect,可以将判断放到Hook内部:

```js
 useEffect(function persistForm() {
    // 👍 将条件判断放置在 effect 中
    if (name !== '') {
      localStorage.setItem('formData', name);
    }
  });
```

总结:

1.在使用多个Hook的时候,React会按照顺序去进行调用,然后做了一个类似于调用队列的缓存在这个组件中.

2.因此,React应当在组件的最顶层调用Hook,并且只能在React函数中调用Hook.

3.不能在循环,条件或者嵌套函数中调用Hook.确保在react函数的最顶层以及任何return 之前调用它们.