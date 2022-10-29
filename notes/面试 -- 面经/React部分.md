## 1.useEffect()第二个参数的传值有以下4种情况,决定页面render的时机.

1. 不传递:

   useEffect不传递第二参数会导致每次渲染都会运行useEffect.然后,当它运行时,它获取数据并更新状态.然后,一旦状态更新,组件将重新呈现,这将再次触发useEffect,这就是问题所在.

   ```js
   //1
   useEffect(()=>{
       console.log(props.number)
       setNumber(props.number)
   }) //所有更新都执行
   ```

2. 传递空数组

   仅在挂在和卸载的时候执行

   ```js
   //2
   useEffect(()=>{
     console.log(props)
   },[]) //仅在挂载和卸载的时候执行
   ```

3. 传递一个值

   在count更新时执行

   ```js
   //3
   useEffect(()=>{
     console.log(count)
   },[count]) //count更新时执行
   ```

4. 传递多个

   ```js
   //4
   const Asynchronous : React.FC=({number})=>{
       const [number2,setNumber2] = useState(number);
       useEffect(()=>{
           console.log(number)
           setNumber2(number)
       },[number,setNumber2]) //监听props对象number的更改
       //setNumber2是useState返回的setter，所以不会在每次渲染时重新创建它，因此effect只会运行一次
   }
   ```

5. 执行时机:

   useEffect的回调函数是[异步宏任务],在下一轮事件循环才会执行.更具JS线程与GUI渲染线程互斥原则,在JS中页面的渲染线程需要当前事件循环的宏任务与微任务都执行完,才会执行渲染线程,渲染页面后,退出渲染线程,控制权交给JS线程,再执行下一轮事件循环.

   好处: 这使得它适用于许多常见的副作用场景,比如设置订阅和事件处理等情况,因为绝大多数操作不应阻塞浏览器对屏幕的渲染更新.

   坏处:产生二次渲染问题,第一次渲染的是旧状态,接着下一个事件循环中,执行改变状态的函数,组件又携带新的状态渲染,在视觉上,就是二次渲染.

   useLayoutEffect:

   而useLayoutEffect与componentDidMount,componentDidUpdate生命周期钩子是[异步微任务],在渲染线程被调用之前就执行.这意味着回调内部执行完才会更新渲染页面,没有二次渲染问题.

   好处: 没有二次渲染问题,页面视觉行为一致.

   坏处: 在回调内部有一些运行耗时很长的代码或者循环时,页面因为需要等JS执行完之后才会交给渲染线程绘制页面,等待时期就是白屏效果,即阻塞了渲染.

   