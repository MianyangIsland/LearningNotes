## useEffect和useLayoutEffect的区别

### 使用方式

这两个函数的使用方式其实非常简单,它们都接受一个函数和一个数组,只有在数组里面的值改变的情况下才会再次执行effect.

### 差异

 useEffect是异步执行的,而useLayoutEffect是同步执行的.

useEffect的执行时机是浏览器完成渲染之后,而useLayoutEffect的执行时机是浏览器把内容真正渲染到界面之前,和componentDidMount等价.

```js
import React , {useEffect , useLayoutEffect , useState} from 'react';

function App(){
  const [state , setState] = useState('hello world');
  useEffect(()=>{
    let i =0;
    while(i <= 100000000)
    {
      i++;
    }
    setState('world Hello');
  },[])


  // useLayoutEffect(()=>{
  //   let i=0;
  //   while(i<=100000000){
  //     i++;
  //   }
  //   setState('world hrllo')
  // })

  return(
    <>
    <div>{state}</div>
    </>
  )
};

export default App;
```

换成useLayoutEffect之后闪烁现象就消失了.

因为useEffect是渲染完之后异步执行的,所以会导致hello world先被渲染到了屏幕上,再变成world hello,就会出现出现闪烁现象.而useLayoutEffect是渲染之前同步执行的,所以会等它执行完再渲染上去,就避免了闪烁现象.也就是说我们最好把操作dom的相关操作放到useLayoutEffect中去,避免导致闪烁.

## 总结:

1. 优先使用useEffect ,因为它是异步执行的,不会阻塞渲染.
2. 会影响到渲染的操作尽量放到useLayoutEffect中去,避免出现闪烁问题.
3. useLayoutEffect和componentDidMount是等价的,会同步调用,阻塞渲染.
4. useLayoutEffect在服务端渲染时使用会有一个warning,因为它可能导致首屏实际内容和服务端渲染出来的内容不一致.

