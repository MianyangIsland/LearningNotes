# useRef的详细介绍:

## 一.什么是useRef

```js
const refContainer = useRef(initialValue);
```

1.   返回一个可变的ref对象,该对象只有个current属性,初始值为传入的参数(initialValue).
2. 返回的ref对象在组件的整个生命周期内保持不变.
3. 当更新current值时并不会re-render,这是与useState不同的地方.
4. 更新useRef是side Effect(副作用),所以一般写在useEffect或event handler里面
5. useRef 类似于组件的this.

## 二.为什么使用useRef?

需求:跨渲染取到状态值

### 只有useState:

```ts
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
export default LikeButton;
```

现象: 在like为6的时候,点击alert,再继续增加like到10,弹出的值为6,而非10.

为什么不是界面上like的实时的状态?

当我们更改状态的时候,React会重新渲染组件,每次的渲染都会拿到独立的like值,并重新定义个handleAlertClick函数,每个handleAlertClick函数体里的值也是它自己的,所以当like为6时,点击alert,触发了handleAlertClick,此时的like是6,哪怕后面继续更改like到10,但alert时的like已经定下来了.

因此: 不同渲染之间无法共享state状态值.

### 采用全局变量

在组件前定义一个类似 global 的变量 

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

### 采用useRef

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

现象: 在like为6的时,点击alert,再继续增加like到10,弹出的值为10.

小结: 采用useRef,作为组件实例的变量,保证获取到的数据肯定是最新的.

该示例同时也说明,ref更改不会re-render.

## useRef与全局变量的区别

```js
import React , {useRef} from "react";

let like = 0;
const LikeButton = ()=>{
  let likeRef = useRef(0);
  function handleAlertClick(){
    setTimeout(()=>{
      alert(`You clicked on ${like}`);
      alert(`You clicked on ${likeRef.current}`);
    },3000);
  }

  return(
    <p>
      <button onClick={()=>{
        like = ++like;
        likeRef.current = likeRef.current + 1;
      }}>
      点赞
      </button>
      <button onClick={handleAlertClick}>Alert</button>
    </p>
  )


}

export default LikeButton;
```

useRef是定义在实例基础上的,如果代码中有多个相同的组件,每个组件的ref只跟组件本身有关,跟其他组件的ref没有关系.

组件前定义的global变量,是属于全局的.如果代码中有多个相同的组件,,那这个global变量在全局是同一个,它们会相互影响.

### 三.useRef与createRef的区别

在一个组件的正常生命周期中可以大致分为3个阶段:

1.  从创建组件到挂在到DOM阶段.初始化props以及state,根据state与props来构建DOM
2. 组件依赖的props以及state状态发生变更,触发更新.
3. 销毁组件

第一个阶段 , useRef与createRef没有差别

**第二个阶段,createRef每次都会返回一个新的引用,而useRef不会随着组件的更新而重新创建.**

第三个阶段,两者都会销毁

## 总结

1. useRef可以用来定义变量,这些变量更改之后不会引起页面重新渲染,比如分页获取数据时,存储页面.
2. useRef也可以用来区分初始渲染还是更新(通过current有没有值.)
3. 在DOM节点上定义Ref属性,通过.current就可以获取到该DOM元素.
4. 通过forwardRef就可以给函数子组件传入ref属性.

