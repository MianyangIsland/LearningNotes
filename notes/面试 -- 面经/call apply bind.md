## this的指向

在ES5中,其实this的指向,始终坚持一个原理: **this永远指向最后调用它的那个对象.**

```js
    var name = "windowsName";
    function a() {
        var name = "Cherry";

        console.log(this.name);          // windowsName

        console.log("inner:" + this);    // inner: Window
    }
    a();
    console.log("outer:" + this)         // outer: Window
```

因为this永远指向最后调用用它的那个对象.我们看最后调用a的地方a(),前面没有调用的对象那么就是全局对象window,这就相当于window.a(),注意,这里我们没有使用严格模式,如果使用严格模式的话,全局对象就是undefined.

再看看这个例子:

```js
var name ="windowsName";
var a = {
    name :'Chery',
    fn:function(){
        console.log(this.name);
    }
}
a.fn();
```

在这个例子中,函数fn是对象a调用的,所以打印的值就是a中的name的值.

做一点小小的改动:

```js
var name = 'windowsName';

var a = {
    name:'Chery',
    fn:function(){
        console.log(this.name); // Chery
    }
}

window.a.fn();
```

这里打印的Cherry的原因也是因为刚刚那句 **this永远指向最后掉用它的那个对象**,最后调用它的对象任然是对象a.

再看这个例子:

```js
var name = "windowsName";
var a = {
    fn:function(){
        console.log(this.name); //undefined
    }
}
window.a.fn();
```

这里为什么会打印undefiend呢?这是因为正如刚刚所描述的那样,调用fn的是a对象,也就是说fn内部的this是对象a,而对象a中并没有对name进行定义,所以log的this.name的值是undefined.

这个例子还是说明了: this永远指向最后调用它的那个对象,因为最后调用fn的对象是a,所以就算a中没有name这个属性,也不会继续向上一个对象寻找this.name,而是直接输出undefined.

再看以恶比较坑的例子:

```js
var name = 'windowsName';
var a  = {
    name:null,
    fn:function(){
        console.log(this.name); //windowsName
    }
}
var f = a.fn;
f();
```

这是因为虽然将a对象的fn方法赋值给了变量f了,但是没有调用,由于刚刚的f并没有调用,所以fn()最后仍然是被window调用的.所以this指向的也就是window.

再来看一个例子:

```js
    var name = "windowsName";

    function fn() {
        var name = 'Cherry';
        innerFunction();
        function innerFunction() {
            console.log(this.name);      // windowsName
        }
    }

    fn()
```

## 怎么改变this指向

改变this的指向有以下几种方法;

 使用ES6的箭头函数

  在函数内部使用_this = this

  使用apply , call ,bind

  new实例化一个对象

```js
    var name = "windowsName";

    var a = {
        name : "Cherry",

        func1: function () {
            console.log(this.name)     
        },

        func2: function () {
            setTimeout(  function () {
                this.func1()
            },100);
        }

    };

    a.func2()     // this.func1 is not a function
```

在不使用箭头函数的情况下,是会报错的,因为最后调用setTimeout的对象是window,但是在window中并没有func1函数.

### 箭头函数

**箭头函数的this始终指向函数定义时的this,而非执行时**.箭头函数需要记着这句话 : 箭头函数中没有this绑定,必须通过查找作用域链来决定其值,如果箭头函数被非箭头函数所包含,则this绑定的是最近一层非箭头函数的this,否则,this为undefiend.

```js
    var name = "windowsName";

    var a = {
        name : "Cherry",

        func1: function () {
            console.log(this.name)     
        },

        func2: function () {
            setTimeout( () => {
                this.func1()
            },100);
        }

    };

    a.func2()     // Cherry
```

如果不使用ES6,那么这种方式应该是最简单的不会出错的方式了,我们是先将这个函数的对象保存在变量_this中,然后在函数中都使用这个 _this,这样 _this就不会改变了.

```js
    var name = "windowsName";

    var a = {

        name : "Cherry",

        func1: function () {
            console.log(this.name)     
        },

        func2: function () {
            var _this = this;
            setTimeout( function() {
                _this.func1()
            },100);
        }

    };

    a.func2()       // Cherry
```

这个例子中，在 func2 中，首先设置 `var _this = this;`，这里的 `this` 是调用 `func2` 的对象 a，为了防止在 `func2` 中的 setTimeout 被 window 调用而导致的在 setTimeout 中的 this 为 window。我们将 `this(指向变量 a)` 赋值给一个变量 `_this`，这样，在 `func2` 中我们使用 `_this` 就是指向对象 a 了。

## 使用apply,call,bind

使用apply , call ,bind 函数也是可以改变this的指向的,先看一下是怎么实现的:

### 使用apply

```js
    var a = {
        name : "Cherry",

        func1: function () {
            console.log(this.name)
        },

        func2: function () {
            setTimeout(  function () {
                this.func1()
            }.apply(a),100);
        }

    };

    a.func2()            // Cherry
```

### 使用call

```js
    var a = {
        name : "Cherry",

        func1: function () {
            console.log(this.name)
        },

        func2: function () {
            setTimeout(  function () {
                this.func1()
            }.call(a),100);
        }

    };

    a.func2()            // Cherry
```

### 使用 bind

```js
    var a = {
        name : "Cherry",

        func1: function () {
            console.log(this.name)
        },

        func2: function () {
            setTimeout(  function () {
                this.func1()
            }.bind(a)(),100);
        }

    };

    a.func2()            // Cherry
```

## apply , call ,bind 区别

apply , call , bind 都是可以该那边this的指向的,但是这三个函数稍有不同.

在MDN中定义apply如下:

```js
apply()方法调用一个函数,其具有一个指定的this,以及作为一个数组(或类数组的对象)提供的参数
```

语法:

```js
fun.apply(thisArg, [argsArray])
```

在fun函数运行时指定的this值,需要注意的是,指定的this值并不一定是该函数执行时真正的this值,

 thisArg : 在fun函数运行时指定的this值.需要注意的是,指定的this值并不一定是该函数执行时真正的this值,如果这个函数出于非严格模式下,则指定为null或undefiend时会自动指向全局对象(浏览器中就是window对象),同时值为原始值(数字,字符串,布尔值)的this会指向该原始值的自动包装对象.

argArray: 一个数组或者类数组对象,其中的数组元素将作为单独的参数传给fun函数.如果该参数的值为null或undefiend,则表示不需要传入任何参数.从ES5开始可以使用类数组对象.

### apply和call的区别

其实apply和call基本类似,它们的区别只是传入的参数不同:

call的语法为:

```js
fun.call(thisArg[, arg1[, arg2[, ...]]])
```

所以apply和call的区别是call方法接收的是若干个参数列表,而apply接收的是一个包含多个参数的数组.

```js
var a = 2;
var b =3;
var a = {
    name:'Cherry',
    fn:function(a,b){
        console.log(a + b);
    }
}

var b = a.fn;
b.apply(a,[1,2]);
```

```js
    var a ={
        name : "Cherry",
        fn : function (a,b) {
            console.log( a + b)
        }
    }

    var b = a.fn;
    b.call(a,1,2)       // 3
```

## bind 和 apply , call区别

```txt
bind()方法创建一个新的函数,当被调用时,将其this关键字设置为提供的值,在调用新函数时,在任何提供之前提供了给定的参数序列
```

所以我们可以看出,bind时创建一个新的函数,我们必须要手动去调用:

```js
    var a ={
        name : "Cherry",
        fn : function (a,b) {
            console.log( a + b)
        }
    }

    var b = a.fn;
    b.bind(a,1,2)()           // 3
```

## JS 中的函数调用

函数调用的方法一共有 4 种

1. 作为一个函数调用
2. 函数作为方法调用
3. 使用构造函数调用函数
4. 作为函数方法调用函数（call、apply）

### 作为一个函数调用

```js
    var name = "windowsName";
    function a() {
        var name = "Cherry";

        console.log(this.name);          // windowsName

        console.log("inner:" + this);    // inner: Window
    }
    a();
    console.log("outer:" + this)         // outer: Window
```

这样一个最简单的函数，不属于任何一个对象，就是一个函数，这样的情况在 JavaScript 的在浏览器中的非严格模式默认是属于全局对象 window 的，在严格模式，就是 undefined。 

### 函数作为方法调用

```js
    var name = "windowsName";
    var a = {
        name: "Cherry",
        fn : function () {
            console.log(this.name);      // Cherry
        }
    }
    a.fn();
```

这里定义一个对象 `a`，对象 `a` 有一个属性（`name`）和一个方法（`fn`）。

然后对象 `a` 通过 `.` 方法调用了其中的 fn 方法。

### 使用构造函数调用函数

```txt
如果函数调用前使用了 new 关键字, 则是调用了构造函数。
这看起来就像创建了新的函数，但实际上 JavaScript 函数是重新创建的对象：
```

```js
// 构造函数:
function myFunction(arg1, arg2) {
    this.firstName = arg1;
    this.lastName  = arg2;
}

// This    creates a new object
var a = new myFunction("Li","Cherry");
a.lastName;                             // 返回 "Cherry"
```

这就有要说另一个面试经典问题：new 的过程了， 

```js
var a = new myFunction("Li","Cherry");

new myFunction{
    var obj = {};
    obj.__proto__ = myFunction.prototype;
    var result = myFunction.call(obj,"Li","Cherry");
    return typeof result === 'obj'? result : obj;
}
```

1. 创建一个空对象 obj;
2. 将新创建的空对象的隐式原型指向其构造函数的显示原型。
3. 使用 call 改变 this 的指向
4. 如果无返回值或者返回一个非对象值，则将 obj 返回作为新对象；如果返回值是一个新对象的话那么直接直接返回该对象。

所以我们可以看到，在 new 的过程中，我们是使用 call 改变了 this 的指向。 

### 作为函数方法调用函数

```js
在 JavaScript 中, 函数是对象。
JavaScript 函数有它的属性和方法。call() 和 apply() 是预定义的函数方法。 两个方法可用于调用函数，两个方法的第一个参数必须是对象本身
在 JavaScript 严格模式(strict mode)下, 在调用函数时第一个参数会成为 this 的值， 即使该参数不是一个对象。在 JavaScript 非严格模式(non-strict mode)下, 如果第一个参数的值是 null 或 undefined, 它将使用全局对象替代。
```

```js
    var name = "windowsName";

    function fn() {
        var name = 'Cherry';
        innerFunction();
        function innerFunction() {
            console.log(this.name);      // windowsName
        }
    }

    fn()
```

这里的 innerFunction() 的调用是不是属于第一种调用方式：作为一个函数调用（它就是作为一个函数调用的，没有挂载在任何对象上，所以对于没有挂载在任何对象上的函数，在非严格模式下 this 就是指向 window 的） .

```js
    var name = "windowsName";

    var a = {
        name : "Cherry",

        func1: function () {
            console.log(this.name)     
        },

        func2: function () {
            setTimeout(  function () {
                this.func1()
            },100 );
        }

    };

    a.func2()     // this.func1 is not a function
```

这个简单一点的理解可以理解为“**匿名函数的 this 永远指向 window**”，你可以这样想，还是那句话**this 永远指向最后调用它的那个对象**，那么我们就来找最后调用匿名函数的对象，这就很尴尬了，因为匿名函数名字啊，笑哭，所以我们是没有办法被其他对象调用匿名函数的。所以说 匿名函数的 this 永远指向 window。

如果这个时候你要问，那匿名函数都是怎么定义的，首先，我们通常写的匿名函数都是自执行的，就是在匿名函数后面加 `()` 让其自执行。其次就是虽然匿名函数不能被其他对象调用，但是可以被其他函数调用啊，比如例 7 中的 setTimeout。