# useMemo 与 useCallback

## 回顾react中的性能优化:

 在hooks诞生之前,如果组件包含内部state,我们都是基于class的形式来创建组件.当时我们也知道,react中,性能的优化点在于:

1.    调用setSate,就会触发组件的重新渲染,无论前后的state是否不同.
2.  父组件更新,子组件也会更新.

基于上面的两点,我们通常的解决方案是: 使用immutable进行比较,在不相等的时候调用setState;在shouldComponentUpdate判断前后的props和state,如果没有变化,则返回false来阻止更新.

在hooks出来之后,我们能够使用function的形式来创建包含内部state的组件.但是,使用function的形式,失去了上面的shouldComponentUpdate,我们无法通过判断前后状态来决定是否更新.而且,在函数组件中,react不再区分mount和uodate两个状态,这意味着函数组件的每一次调用都会执行器内部的所有逻辑,那么会带来较大的性能损耗.因此useMemo与useCallback就是解决性能问题最好的方式.

## 对比

**useCallback和useMemo的参数跟useEffect一致,它们之间最大的区别是useEffect会用于处理副作用,而前两个hooks不能.**

**useCallback和useMemo都会在组件第一次渲染的时候执行,之后会在器依赖的变量改变时候再次执行;并且这两个hooks都返回缓存的值,useMemo返回缓存的变量,useCallback返回缓存的函数.**

##  useMemo:

```js
import React from 'react';
 
 
export default function WithoutMemo() {
    const [count, setCount] = useState(1);
    const [val, setValue] = useState('');
 
    function expensive() {
        console.log('compute');
        let sum = 0;
        for (let i = 0; i < count * 100; i++) {
            sum += i;
        }
        return sum;
    }
 
    return <div>
        <h4>{count}-{val}-{expensive()}</h4>
        <div>
            <button onClick={() => setCount(count + 1)}>+c1</button>
            <input value={val} onChange={event => setValue(event.target.value)}/>
        </div>
    </div>;
}
```

这里创建了两个state，然后通过expensive函数，执行一次昂贵的计算，拿到count对应的某个值。我们可以看到：无论是修改count还是val，由于组件的重新渲染，都会触发expensive的执行(能够在控制台看到，即使修改val，也会打印)；但是这里的昂贵计算只依赖于count的值，在val修改的时候，是没有必要再次计算的。在这种情况下，我们就可以使用useMemo，只在count的值修改时，执行expensive计算：

```js
export default function WithMemo() {
    const [count, setCount] = useState(1);
    const [val, setValue] = useState('');
    const expensive = useMemo(() => {
        console.log('compute');
        let sum = 0;
        for (let i = 0; i < count * 100; i++) {
            sum += i;
        }
        return sum;
    }, [count]);
 
    return <div>
        <h4>{count}-{expensive}</h4>
        {val}
        <div>
            <button onClick={() => setCount(count + 1)}>+c1</button>
            <input value={val} onChange={event => setValue(event.target.value)}/>
        </div>
    </div>;
}
```

上面我们可以看到，使用useMemo来执行昂贵的计算，然后将计算值返回，并且将count作为依赖值传递进去。这样，就只会在count改变的时候触发expensive执行，在修改val的时候，返回上一次缓存的值。 



## useCallback

讲完了useMemo,接下来useCallback.useCallback跟useMemo比较类似,但它返回的是缓存的函数.

```js
const fnA = useCallback(fnB, [a])
```

上面的useCallback会将我们传递给它的函数fnb返回,并且将这个结果缓存,会返回新的函数.既然返回的是函数,我们无法很好的判断返回的函数是否变更,所以我们可以借助ES6新增的数据类型Set来判断,具体如下:

```js
import React, { useState, useCallback } from 'react';
 
const set = new Set();
 
export default function Callback() {
    const [count, setCount] = useState(1);
    const [val, setVal] = useState('');
 
    const callback = useCallback(() => {
        console.log(count);
    }, [count]);
    set.add(callback);
 
 
    return <div>
        <h4>{count}</h4>
        <h4>{set.size}</h4>
        <div>
            <button onClick={() => setCount(count + 1)}>+</button>
            <input value={val} onChange={event => setVal(event.target.value)}/>
        </div>
    </div>;
}
```

我们可以看到,每次修改count,set.size就会+1,这就说明useCallback依赖变量count,count变更时会返回新的函数;而val变更时,set.size不会变,说明返回的是缓存的旧版本函数.

知道useCallback有什么样的特点,那有什么作用呢?

使用场景是: 有一个父组件,其中包含子组件,子组件接收一个函数作为props;通常而言,如果父组件更新了,子组件也会执行更新;但是大多数场景下,更新是没有必要的,我们可以借助useCallback来返回函数,然后把这个函数作为props传递给子组件;这样,子组件就能避免不必要的更新.

```js
import React , {useState , useCallback , useEffect} from "react";
function Person(){
  const [count,setCount] = useState(1);
  const [val,setVal] = useState('');
  const callback = useCallback(()=>{
    return count;
  },[count]);
  return (
    <div>
      <h4>{count}</h4>
      <Child callback={callback}/>
      <div>
        <button onClick={()=> setCount(count + 1)}>+</button>
        <input value={val} onChange={event => setVal(event.target.value)}></input>
      </div>
    </div>
  )
}

function Child({callback}){
  const [count,setCount] = useState(()=> callback());
  useEffect(()=>{
    console.log('Child callback!')
    setCount(callback());
  },[callback]);
  return(
    <div>
      {count}
    </div>
  )
}

export default Person;
```

不仅是上面的例子,所有依赖本地状态或props来创建函数,需要使用到缓存函数的地方,都是useCallback的应用场景.



useEffect,useMemo,useCallback都是自带闭包的.也就是说,每一次组件的渲染,其都会捕获当前组件函数上下文中的状态(state,props),所以每一次这三种hooks的执行,反映的也都是当前的状态,你无法使用他们来捕获上一次的状态.对于这种情况,我们应该使用ref来访问.



总结:

useMemo和useCallback都是react Hook提供的两个API,用于缓存数据,性能优化;两者接收的参数都是一样的,第一个参数表示一个回调函数,第二个表示依赖的数据.

 共同作用:

 在依赖数据发生变化的时候,才会调用传进去的回调函数去重新计算结果,起到一个缓存的作用.

  两者的区别:

1. useMemo 缓存的结果是回调函数中return 回来的值,主要用于缓存计算结果的值,应用场景如需要计算的状态.
2. useCallback 缓存的结果是函数,主要用于缓存函数,应用场景如需要缓存的函数,因为函数式组件的每次任何一个state发生变化,都会触发整个组件的更新,一些函数是没有必要更新的,此时就应该缓存起来,提高性能,较少对资源的浪费.

