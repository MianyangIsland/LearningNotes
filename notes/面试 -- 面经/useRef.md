## useRef的总结

### 一.什么是useRef

```js
const refContainer = useRef(initialValue);
```

返回一个可变的ref对象,该对象只有个current属性,初始值为传入的参数(initialValue).

返回的ref对象在组件的整个生命周期内保持不变.

当更新current值时并不会re-render,这是与useState不同的地方.

更新useRef是side effect(副作用),所以一般写在useEffect或event handler里.

useRef类似于组件的this.

### 简单实例

需求:点击button的时候选中文本框.

```js
import React, { MutableRefObject, useRef } from 'react'
const TextInputWithFocusButton: React.FC = () => {
   const inputEl: MutableRefObject<any> = useRef(null)
   const handleFocus = () => {
       // `current` 指向已挂载到 DOM 上的文本输入元素
       inputEl.current.focus()
   }
   return (
       <p>
           <input ref={inputEl} type="text" />
           <button onClick={handleFocus}>Focus the input</button>
       </p>
   )
}
export default TextInputWithFocusButton
```

小结: 通过useRef定义一个inputEL变量,在input元素上定义ref={inputEl},这样通过inoutEl.current就可以获取到input Dom元素.选中则调用下focus函数即可.

## 二.为什么使用useRef

需求:跨渲染取到状态值.

只用useState:

实现:

```js
import React, { useState } from "react";
const LikeButton: React.FC = () => {
    const [like, setLike] = useState(0)
    function handleAlertClick() {
        setTimeout(() => {
            alert(`you clicked on ${like}`) 
            //形成闭包，所以弹出来的是当时触发函数时的like值
        }, 3000)
    }
    return (
        <>
            <button onClick={() => setLike(like + 1)}>{like}赞</button>
            <button onClick={handleAlertClick}>Alert</button>
        </>
    )
}
export default LikeButton
```

现象:

在like为6的时候,点击alert,在继续增加like到10,弹出的值为6,而非10.

### 为什么不是界面上like的实时状态?

当我们更改状态的时候,React会重新渲染组件,每次的渲染都会拿到的like值,并重新定义个handkAlertClick函数,每个handleAlertClick函数体里的like值也是它自己的,所以当like为6时,点击alert,触发了handleAlertClick,此时的like是6,哪怕后面继续更改like到10,但alert时的like已经定下来了.

### 小结:不同渲染之间无法共享state状态.

### 采用全局变量

在组件前定义一个类似global的变量.

实现:

```js
import React from "react";
let like = 0;
const LikeButton: React.FC = () => {
  function handleAlertClick() {
    setTimeout(() => {
      alert(`you clicked on ${like}`);
    }, 3000);
  }
  return (
    <>
      <button
        onClick={() => {
          like = ++like;
        }}
      >
        {like}赞
      </button>
      <button onClick={handleAlertClick}>Alert</button>
    </>
  );
};
export default LikeButton;
```

现象: 在like为6的时候,点击alert,在继续增加like到10,弹出的值为10.

**小结: 由于like变量时定义在组件外,所以不同渲染件是可以共用该变量,所以3秒后获取的like值就是最新的like值.**

**该实例同时也说明,非state变量不会引起重新render.**

## 采用useRef

```js
import React, { useRef } from "react";
const LikeButton: React.FC = () => {
  // 定义一个实例变量
  let like = useRef(0);
  function handleAlertClick() {
    setTimeout(() => {
      alert(`you clicked on ${like.current}`);
    }, 3000);
  }
  return (
    <>
      <button
        onClick={() => {
          like.current = like.current + 1;
        }}
      >
        {like.current}赞
      </button>
      <button onClick={handleAlertClick}>Alert</button>
    </>
  );
};
export default LikeButton;
```

**现象:在like为6的时候,点击alert,再继续增加like到10,弹出的值为10.跟上面使用全局变量现象一致.**

**小结:**

采用useRef,作为组件实例的变量,保证获取到的数据肯定是最新的.

## useRef与全局变量的区别

useRef是定义在组件实例基础上的,如果代码中有多个相同的组件,每个组件的ref只跟组件本身有关,跟其他组件的ref没有关系.

组件前定义的global变量,是属于全局的.如果代码中有多个相同的组件,那这个global变量在全局是同一个,它们会互相影响.

## 三.useRef与createRef的区别

在一个组件的正常生命周期中可以大致分为3个阶段:

1.从创建组件到挂载到DOM阶段.初始化props以及state,根据state与props来构建DOM.

2.组件依赖的props以及state状态发生变更,触发更新.

3.销毁阶段.

第一个阶段,useRef与createRef没有差别.

第二个阶段,createRef每次都会返回一个新的引用;而useRef不会随着组件的更新而重新创建.

第三个阶段, 两者都会销毁.

## 四.获取子组件的属性或方法

### 需求: 

调用子组件里的某个函数.

### 自定义属性传入ref

实现:

父组件创建一个ref作为一个属性传入子组件.子组件根据内部方法的变化动态更改ref(useEffect).

```js
import React, {
    MutableRefObject,
    useState,
    useEffect,
    useRef,
    useCallback
} from 'react'
interface IProps {
    //prettier-ignore
    label: string,
    cRef: MutableRefObject<any>
}
const ChildInput: React.FC<IProps> = (props) => {
    const { label, cRef } = props
    const [value, setValue] = useState('')
    const handleChange = (e: any) => {
        const value = e.target.value
        setValue(value)
    }
    const getValue = useCallback(() => {
        return value
    }, [value])
    useEffect(() => {
        if (cRef && cRef.current) {
            cRef.current.getValue = getValue
        }
    }, [getValue])
    return (
        <div>
            <span>{label}:</span>
            <input type="text" value={value} onChange={handleChange} />
        </div>
    )
}

const ParentCom: React.FC = (props: any) => {
    const childRef: MutableRefObject<any> = useRef({})
    const handleFocus = () => {
        const node = childRef.current
        alert(node.getValue())
    }
    return (
        <div>
            <ChildInput label={'名称'} cRef={childRef} />
            <button onClick={handleFocus}>focus</button>
        </div>
    )
}

export default ParentCom;
```

现象:父组件按钮点击时,通过调用getValue,获取到子组件input里的value值.

小结:不够优雅,尤其是自定义一个属性传入ref.

## 通过useImperativeHandle , 配合forwardRef

### forwardRef: 将父类的ref作为参数传入函数式组件中.

```js
React.forwardRef((props, ref) => {})  
//创建一个React组件，
//这个组件将会接受到父级传递的ref属性，
//可以将父组件创建的ref挂到子组件的某个dom元素上,
//在父组件通过该ref就能获取到该dom元素
```

```js
const FancyButton = React.forwardRef((props, ref) => (  
  <button ref={ref} className="FancyButton">    
    {props.children}
  </button>
));
// 可以直接获取到button的DOM节点
const ref = React.useRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

**useImperativeHandle**在函数式组件中,用于定义暴露给父组件的ref方法,用来限制子组件对外暴露的信息,只有useImperativeHandle的第二个参数定义的属性跟方法才可以在父组件获取到.

**为什么使用**: 因为使用forward + useRef获取子函数式组件DOM时,获取到的dom属性暴露的太多了.

**解决**:使用useImperativeHandle解决,在子函数式组件中定义父组件需要进行的DOM操作,没有定义的就不会暴露给父组件>

