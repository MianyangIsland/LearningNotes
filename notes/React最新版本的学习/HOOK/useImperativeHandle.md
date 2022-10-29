## useImperativeHandle 介绍

```js
useImperativeHandle(ref, createHandle, [deps])
```

useImperativeHandle可以让你在使用ref时自定义暴露父组件的实例值.在大多数情况下,应当避免使用ref这样的命令式代码.useImperativeHandle应当与forwardRef一起使用.

  ref: 定义current对象的ref.

 createHandle: 一个寒素,返回值是一个对象,即这个ref的current.

  对象[deps]: 即依赖列表,当监听的依赖发生变化,useImperativeHandle才会重新将子组件的实例属性输出到父组件.

  ref 的current属性上,如果为空数组,则不会重新输出.

useImperativeHandle使用

在介绍useImerativeHandle之前一定要清除React关于ref转发(也叫透传)的知识点,是使用React.forwardRef方法实现的,该方法返回一个组件,参数为函数(props,callback,并不是函数组件),函数的第一个参数为父组件传递的props,第二个参数为父组件传递的ref,其目的就是希望可以封装组件时,外层组件可以通过ref直接控制内层组件或元素的行为.

```js
import React, { useCallback, useRef } from 'react';


// 实现 ref 的转发
const FancyButton = React.forwardRef((props, ref) => (
  <div>
    <input ref={ref} type="text" />
    <button>{props.children}</button>
  </div>
));

// 父组件中使用子组件的 ref
function App() {
  const ref = useRef();
  const handleClick = useCallback(() => ref.current.focus(), [ ref ]);

  return (
    <div>
      <FancyButton ref={ref}>Click Me</FancyButton>
      <button onClick={handleClick}>获取焦点</button>
    </div>
  )
}

export default App;
```

上面例子中创建了一个FancyButton组件,内部渲染了一个button元素,我们希望在父元素App中渲染FancyButton,并通过传递给FancyButton的ref直接操作内部的button.

一个官方的useImperativeHandle例子.

```js
import React, { useRef, useImperativeHandle } from 'react';
import ReactDOM from 'react-dom';

const FancyInput = React.forwardRef((props, ref) => {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));

  return <input ref={inputRef} type="text" />
});

const App = props => {
  const fancyInputRef = useRef();

  return (
    <div>
      <FancyInput ref={fancyInputRef} />
      <button
        onClick={() => fancyInputRef.current.focus()}
      >父组件调用子组件的 focus</button>
    </div>
  )
}

export default App;

```

上面这个例子与直接转发ref不同,直接转发ref是将React.forwardRef中函数上的ref参数直接应用在了返回元素的ref属性上,其实父,子组件引用的是同一个ref的current对象,官方不建议使用这样的ref透传,而使用useImperativeHandle后,可以让父,子组件风别有自己的ref,通过React.forwardRef将父组件的ref透传过来,通过useImperativeHandle方法来自定义开始给父组件的current.



useImperativeHandle的第一个参数是定义current对象的ref,第二个参数是一个函数,返回值是一个对象,即这个ref的current对象,这样可以像上面的案例一样,通过自定义父组件的ref来使用子组件ref的某些方法.

useImperativeHandle和React.forwardRef必须是配合使用的,这也是为什么在开头要介绍ref的转发.

```js
import React , {useState , useRef , useImperativeHandle , useCallback} from 'react';

const FancyInut = React.forwardRef((props,ref)=>{
  const [fresh,setFresh] = useState(false);
  const attRef = useRef(0);
  useImperativeHandle(ref,()=>({
    attRef,
    fresh
  }),[fresh]);

  const handleClick = useCallback(()=>{
    attRef.current ++;
  },[]);

  return(
    <div>
      {attRef.current}
      <button onClick={handleClick}>Fancy</button>
      <button onClick={()=> setFresh(!fresh)}>刷新</button>
    </div>
  )
})

const App = props =>{
  const fancyInputRef = useRef();

  return(
    <div>
      <FancyInut ref={fancyInputRef}/>
      <button onClick={()=> console.log(fancyInputRef.current)}>
        父组件访问子组件的实例属性
      </button>
    </div>
  )
}

export default App;
```

上面的案例相对于官方的例子意图更明显一写,通过useImperativeHandle将子组件的实例属性输出到父组件,而子组件内部通过ref更改current对象后,组件不会重新渲染,需要改变useState设置的状态才能更改.

