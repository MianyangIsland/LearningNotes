## Call,apply,bind:

**Apply**

```js
Function.prototype.myApply = function(context,agrs){
    context = context || window;
    agrs = agrs ? agrs : [];
    const key = Symbol();
    context[key] = this;
    let result = context[key](...agrs);
    delete context[key];
    return result;
}
```

**Call**

```js
Function.prototype.myApply = function(context,...agrs){
    context = context || window;
    agrs = agrs ? agrs : [];
    const key = Symbol();
    context[key] = this;
    let result = context[key](...agrs);
    delete context[key];
    return result;
}
```

**bind**

```js
//实现bind的步骤，我们可以分解成为三部分：
//修改this指向
//动态传递参数
// 方式一：只在bind中传递函数参数
fn.bind(obj,1,2)()
// 方式二：在bind中传递函数参数，也在返回函数中传递参数
fn.bind(obj,1)(2)
//1 2 3 4 5 
//兼容new关键字
//整体实现代码如下：
Function.prototype.myBind = function (context) {
// 判断调用对象是否为函数
         if (typeof this !== "function") {
            throw new TypeError("Error");
          }// 获取参数
          const args = [...arguments].slice(1),
          fn = this;
          return function Fn() {
          // 根据调用方式，传入不同绑定值
          return fn.apply(this instanceof Fn ? new fn(...arguments) : context, args.concat(...arguments)); 
    }
}
```

```js
Function.prototype.myBind = function(context,...agrs){
    context = context || window;
    agrs = agrs ? args : [];
    const fn = this;
    return function(...agrs1){
        if(typeof this !== 'function'){
            return new fn(context,...agrs,...agrs1);
        }
        else{
            return fn.apply(context,[...args,...agrs1])
        }
    }
}
```

