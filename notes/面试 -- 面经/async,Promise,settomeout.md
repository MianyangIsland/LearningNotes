asunc函数返回一个Promise对象,当函数执行的时候,一旦遇到await就会先返回,等到触发的异步操作完成,再接着执行函数体内后面的语句.可以理解为,是让出了线程,跳出了async函数体.

async function声明将定义一个返回AsyncFunction对象的异步函数. 当调用一个async函数时,会返回一个Promise对象.当这个async函数返回一个值时,Promise的resolve方法会负责传递这个值 , 当async函数抛出异常时,Promise的reject方法也会传递这个异常值.

await

```txt
语法: [return_value] = await expression ; 表达式(express) : 一个Promise对象或者任何要等待的值. 返回值(return_value) : 返回Promise对象的处理结果.如果等待的不是Promise对象,则返回该值本身
```

所以,当await操纵符后面的表达式是一个Promise的时候,它的返回值,实际上就是Promise的回调函数resolve的参数.

我们都知道Promise是一个立即执行函数,但是它的成功(或失败:reject)的回调函数resolve却是一个异步执行的回调.当执行到resolve()时,这个任务会被放入到回调队列中,等待调用栈有空闲时事件循环再来取走它.

**JS代码执行机制:**

1. 遇到同步代码直接执行
2. 遇到异步代码先放到一边,并且将他回调函数存起来,存的地方叫事件队列.
3. 等所有同步代码都执行完,再从事件队列中把存起来的所有异步回调函数拿出来按顺序执行.

