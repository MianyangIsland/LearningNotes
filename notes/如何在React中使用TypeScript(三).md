## 四.事件处理

1.Event事件类型:

在开发中我们会经常在事件处理函数中使用event事件对象，比如在input框输入时实时获取输入的值；使用鼠标事件时，通过 clientX、clientY 获取当前指针的坐标等等。 

我们知道，Event是一个对象，并且有很多属性，这时很多人就会把 event 类型定义为any，这样的话TypeScript就失去了它的意义，并不会对event事件进行静态检查，如果一个键盘事件触发了下面的方法，也不会报错： 

```js
const handleEvent = (e: any) => {
    console.log(e.clientX, e.clientY)
}
```

由于Event事件对象中有很多的属性，所以我们也不方便把所有属性及其类型定义在一个interface中，所以React在声明文件中给我们提供了Event事件对象的类型声明。 

常见的Event 事件对象如下： 

**剪切板事件对象**：ClipboardEvent<T = Element>

**拖拽事件对象**：DragEvent<T = Element>

**焦点事件对象**：FocusEvent<T = Element>

**表单事件对象**：FormEvent<T = Element>

**Change事件对象**：ChangeEvent<T = Element>

**键盘事件对象**：KeyboardEvent<T = Element>

**鼠标事件对象**：MouseEvent<T = Element, E = NativeMouseEvent>

**触摸事件对象**：TouchEvent<T = Element>

**滚轮事件对象**：WheelEvent<T = Element>

**动画事件对象**：AnimationEvent<T = Element>

**过渡事件对象**：TransitionEvent<T = Element>

 可以看到，这些Event事件对象的泛型中都会接收一个Element元素的类型，这个类型就是我们绑定这个事件的标签元素的类型，标签元素类型将在下面的第五部分介绍。 

```js
import * as React from 'react';

interface IProps{
  name:string,
  age?:number
}

type State={
  text:string
}

function App(props:IProps){
  
 const [text,setText] = React.useState('');

 const handleChange = (e:React.FocusEvent<HTMLInputElement>):void =>{
  setText(e.currentTarget.value);
 }
  return(<>
   <div>{props.name}</div>
   <div>{text}</div>
   <input type={'text'} value={text} onChange={handleChange}></input>
  </>)
}

export default App;
```

这里就给onChange方法的事件对象定义为了FormEvent类型，并且作用的对象时一个HTMLInputElement类型的标签（input标签） 

 

 

 