## 捕获和冒泡

当你触发一个元素的事件的时候,该事件从该元素的祖先元素传递下去,此过程为捕获,而到达此元素之后,又会向其祖先元素传播上去,此过程为冒泡.

```js
    <div id="a">
      <div id="b">
        <div id="c">
          <div id="d">哈哈哈哈哈</div>
        </div>
      </div>
    </div>
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a2298b84cc0e484a85d4c8e18674ea34~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image) 

## addEventListener

addEventListener是为元素绑定事件的方法,他接收三个参数:

第一个参数:绑定的时间名

第二个参数:执行的函数

第三个参数:

false: 默认,代表冒泡时绑定.

true:代表捕获时绑定.

## target&currentTarget

false

我们给四个div元素绑定事件,且addEventListener第三个参数不设置,则默认设置为false.

```js
const a = document.getElementById('a')
const b = document.getElementById('b')
const c = document.getElementById('c')
const d = document.getElementById('d')
a.addEventListener('click', (e) => {
  const {
    target,
    currentTarget
  } = e
  console.log(`target是${target.id}`)
  console.log(`currentTarget是${currentTarget.id}`)
})
b.addEventListener('click', (e) => {
  const {
    target,
    currentTarget
  } = e
  console.log(`target是${target.id}`)
  console.log(`currentTarget是${currentTarget.id}`)
})
c.addEventListener('click', (e) => {
  const {
    target,
    currentTarget
  } = e
  console.log(`target是${target.id}`)
  console.log(`currentTarget是${currentTarget.id}`)
})
d.addEventListener('click', (e) => {
  const {
    target,
    currentTarget
  } = e
  console.log(`target是${target.id}`)
  console.log(`currentTarget是${currentTarget.id}`)
})
```

现在我们点击,看看输出的东西,可以看出触发的是d,而执行的元素是冒泡的顺序.

```txt
target是d currentTarget是d
target是d currentTarget是c
target是d currentTarget是b
target是d currentTarget是a
```

true

我们把四个事件第三个参数都设置为`true`，我们看看输出结果，可以看出触发的是d，而执行的元素是捕获的顺序 

```txt
target是d currentTarget是a
target是d currentTarget是b
target是d currentTarget是c
target是d currentTarget是d
```

区别:

e.target : **触发**事件的元素

e.currentTarget:**绑定**事件的元素

阻止事件冒泡有三种方法:

### 1.event.stopPropagation()方法

```js
	$('.btn').click(function (even) {
		even.stopPropagation();
		alert('按钮被点击了');
	})
```

这是阻止事件的冒泡方法,不让事件向document上蔓延,但是默认事件依然会执行,当你调用这个方法的时候,如果点击一个链接,这个链接仍然会被打开.

```js
<a href="https://www.csdn.net/" class="box">
	<button class="btn">按钮</button>
</a>
```

**2.event.preventDefault()方法**

```js
	$('.btn').click(function (even) {
		even.preventDefault();
		alert('按钮被点击了');
	})
```

这是阻止默认事件的方法,调用此方法时,链接不会被打开,但是会发生冒泡,冒泡会传递到上一层的父元素.

**3.return false**

```js
	$('.btn').click(function (even) {
		alert('按钮被点击了');
		return false;
	})
```

这个方法比较暴力,它会同时阻止事件冒泡也会阻止默认事件,写上此代码,链接不会被打开,事件也不会传递到上一层的父元素,可以理解为return false就等于同时调用event.stopPropagation()和event.preventDefault().

