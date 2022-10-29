## Promise的理解

一.Promise是最早由社区提出和实现的一种解决异步编程的方案,比其他传统的解决方案(回调函数和事件)更合理和更强大.

ES6将其写进了语言标准,统一了用法,原生提供了Promise对象.

ES6规定,Promise对象是一个构造寒素,用来生成Promise实例.

二.Promise是为解决什么问题而产生的?

Promise是为解决处理回调金字塔问题而产生的.

三.Promise的两个特点:

1. Promise对象的状态不受外界影响

   1) pending 初始状态

   2) fulfilled 成功状态

   3) rejected 失败状态

   Promise由以上三种状态,只有异步操作的结果可以决定当前是哪一种状态,其他任何操作都无法改变这个状态.

2. Promise的状态一旦改变,就不会再变,任何时候都可以得到这个结果,状态不可逆,只能由pending变成fulfilled或者由pending变成rejected.

四.Promise的三个缺点:

1) 无法取消Promise , 一旦新建它就会立即执行,无法中途取消.

2) 如果不设置回调函数, Promise内部抛出的戳五,不会反映到外部.

3) 当出于pending状态时, 无法得知目前进展到哪一阶段,是刚刚开始还是即将完成.

五.Promise在哪存放成功回调序列和失败回调序列.

1) onResolvedCallbacks 成功后要执行的回调序列是一个数组.

2) onRejectedCallbacks 失败后要执行的回调序列 是一个数组.

以上两个数组存放在Promise创建实例时给Promise这个类传的函数中,默认都是空数组,每次实例then的时候传入onFulfilled成功回调 onRejected失败回调,如果此时的状态是pending则将onFulfilled和onRejected push到对应的成功回调序列数组和失败回调序列数组中,如果此时的状态是fulfilled 则 onFulfilled立即执行,如果此时的状态是rejected 则onRejected立即执行.

## 六.Promise的用法

1.Promise构造函数接受一个函数作为参数,该函数的两个参数分别是resolve和rejected.它们是两个函数,由JavaScript引擎提供,不用自己部署.

```js
    const promise = new Promise(function(resolve, reject) {
      // ... some code

      if (/* 异步操作成功 */){
        resolve(value);
      } else {
        reject(error);
      }
    });
```

2.resolved函数的作用是,将Promise对象的状态从"未完成"变为"成功,在异步操作成功时调用,并将异步操作的结果，作为参数传递出去；reject函数的作用是，将Promise对象的状态从“未完成”变为“失败”（即从 pending 变为 rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

 3.Promise实例生成后,可以用then方法分别指定resolved状态和rejected状态的回调函数.

```js
    promise.then(function(value) {
      // success
    }, function(error) {
      // failure
    });
```

then方法可以接受两个回调函数作为参数.第一个回调函数是Promise对象的状态变为resolved时调用，第二个回调函数是Promise对象的状态变为rejected时调用。其中，第二个函数是可选的，不一定要提供。这两个函数都接受Promise对象传出的值作为参数。

 七.按照PromiseA+规范写Promise的简单实现原理:

```js
// 第一步：Promise构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。它们是两个函数，由 JavaScript 引擎提供，不用自己部署。
    function Promise(task) {

        let that = this; // 缓存this
        that.status = 'pending'; // 进行中的状态
        that.value = undefined; //初始值

        that.onResolvedCallbacks = []; // 存放成功后要执行的回调函数的序列
        that.RejectedCallbacks = []; // 存放失败后要执行的回调函数的序列
        // 该方法是将Promise由pending变成fulfilled
        function resolve (value) {
            if (that.status == 'pending') {
                that.status = 'fulfilled';
                that.value = value;
                that.onResolvedCallbacks.forEach(cb => cd(that.value))
            }

        }
        // 该方法是将Promise由pending变成rejected
        function reject (reason) {
          if (that.status == 'pending') {
                that.status = 'rejected';
                that.value = reason;
                that.onRjectedCallbacks.forEach(cb => cd(that.value))
            }
        }

        try {
        // 每一个Promise在new一个实例的时候 接受的函数都是立即执行的
            task(resolve, reject)
        } catch (e) {
            reject(e)
        }
    }

// 第二部 写then方法，接收两个函数onFulfilled onRejected，状态是成功态的时候调用onFulfilled 传入成功后的值，失败态的时候执行onRejected，传入失败的原因，pending 状态时将成功和失败后的这两个方法缓存到对应的数组中，当成功或失败后 依次再执行调用
    Promise.prototype.then = function(onFulfilled, onRejected) {
        let that = this;
        if (that.status == 'fulfilled') {
            onFulfilled(that.value);
        }
        if (that.status == 'rejected') {
            onRejected(that.value);
        }
        if (that.status == 'pending') {
            that.onResolvedCallbacks.push(onFulfilled);
            that.onRjectedCallbacks.push(onRejected);
        }
    }
```

## Promise 方法

Promise.prototype.catch方法是.then(null,rejction)的别名,用于指定发生错误时的回调函数.

Promise.all : 参数 : 接受一个数组,数组内都是Promise实例

返回值 : 返回一个Promise实例,这个Promise实例的状态转移取决于参数的Promise实例的状态变化.当参数中所有的实例都处于resolve状态时,返回的Promise实例会变为resolve状态.如果参数中任意一个实例出于rejected状态,返回的Promise实例变为reject状态.

Promise.race:

参数: 接受一个数组,数组内都是Promise实例.

返回值: 返回一个Promise实例,这个Promise实例的状态转移取决于参数的Promise实例的状态变化.当参数中任何一个实例处于resolv状态时,返回的Promise实例会变为resolve状态.如果参数中任意一个实例处于rejetced状态,返回的Promise实例变为reject状态.

Promise.any() : 参数中的Promise都失败才会失败.

参数中只要有一个成功的值,那么最终就是成功.返回的时第一个成功的值.后面就不管了.

Then方法中return 的作用?

then方法执行后返回一个新的Promise对象.输出then方法,返回的promise对象的状态改变,是pending状态.在then的回调函数中,return 后面的东西,会用promise包装一下.如果不加return 默认返回的永远是resolve的成功状态;加上return 后,下一个then就会根据上一个return 返回的Promise状态来进行.

 Promise.all()和promise.race()、promise.any()的区别？

（1）Promise.all()用来关注多个Promise对象的变化的,传入多个promise实例，包装成一个新的promise实例返回。Promise.all()参数可以是数组。Promise.all()的状态变化与所有传入Promise实例对象状态有关，所有的状态都变成resolved，最终的状态才会变成resolved，只要有一个变成rejected，最终的状态就变成rejected。

（2）Promise.race()的状态取决于第一个完成的Promise对象，如果第一个完成的成功了，那么最终的就成功，否则最终失败。

Promise.allSettled()的状态与传入的Promise状态无关，它永远都是成功状态的，它只会记录下各个Promise的表现。

（3）Promise.any()

参数中的Promise都失败最终才会失败

参数中只要有一个为成功状态，那么最终就是成功

返回是第一个成功的值，后面就不管了

实际应用：一次性加载多张图片时，哪一张先加载出来就显示哪一张，此时就可以使用Promise.any()方法。


 

 

 

 