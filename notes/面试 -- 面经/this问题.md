# this

## 1.call apply bind 的作用与区别

作用: 都可以改变函数内部的this指向.

区别点:

1. call和apply会调用函数,并且改变函数内部this指向.
2. call 和 apply 传递的参数不一样,call传递参数arg1,arg2... 形式 , apply必须数组形式[arg].
3. bind 不会调用函数,可以改变函数内部this指向.

**解析**:

call方法 , 改变函数内部this指向, call方法调用一个对象.简单理解为调用函数的方式,但是它可以改变函数的this指向.

写法:fun.call(thisArg,arg1,agr2,....)//thisArg为想要指向的对象,arg1,arg2为参数.

call的主要作用也可以实现继承.

```js
function Person(uname, age) {
    this.uname = uname;
    this.age = age;
  }
  function Son(uname, age) {
    Person.call(this, uname, age);
  }
  var son = new Son("zhang", 12);
  console.log(son);
```

apply方法

apply()方法调用一个函数.简单理解为调用函数的方式,但是它可以改变函数的this指向.

apply()方法调用一个函数. 简单理解为调用函数的方式,但是它可以改变函数的this指向.

写法: fun.apply(thisArg,[argArray]);

 thisArg : 在fun函数运行时指定的this值

 argsArray : 传递的值,必须包含在数组里面

 返回值就是函数的返回值,因为他就是调用函数.

apply的主要应用,比如可以利用apply可以求得数组中最大值.

```js
const arr = [1, 22, 3, 44, 5, 66, 7, 88, 9];
const max = Math.max.apply(Math, arr);
console.log(max);
```

bind方法

bind()方法不会调用函数,但能改变函数内部this指向.

写法:fun.bind(thisArg, arg1 , arg2, ......);

 thisArg : 在fun函数运行时指定的this值.

 arg1 , arg2 : 传递的其他参数

 返回有指定的this值和初始化参数改造的原函数拷贝.

```js
var o = {
    name:'lisa'
};
function fn(){
    console.log(this);
}
var f = fn.bind(o);
f();
```

bind应用

如果有的函数我们不需要立即调用,但是有需要改变这个函数的this指向,此时用bind再合适不过了.

```js
const btns = document.querySelectorAll("button");
for (let i = 0; i < btns.length; i++) {
    btns[i].onclick = function() {
      this.disabled = true;
      setTimeout(
        function() {
          this.disabled = false;
        }.bind(this),
        2000
      );
    };
}
```

扩展:

主要应用场景:

1. call经常做继承
2. apply经常跟数组有关系,比如借助于数学对象实现数组最大值最小值.
3. bind不调用函数,但是还想改变this指向,比如改变定时器内部的this指向.

**this指向(普通函数,箭头函数)**

普通函数中的this

1. 谁调用了函数或者方法,那么这个函数或者对象中的this就指向谁.

   ```js
      let getThis = function () {
               console.log(this);
           }
   
           let obj={
               name:"Jack",
               getThis:function(){
                   console.log(this);
               }
           }
           //getThis()方法是由window在全局作用域中调用的，所以this指向调用该方法的对象，即window
           getThis();//window
           //此处的getThis()方法是obj这个对象调用的，所以this指向obj
           obj.getThis();//obj
   ```

2. 匿名函数中的this:匿名函数的执行具有全局性,则匿名函数中的this指向是window,而不是调用该匿名函数的对象.

   ```js
           let obj = {
               getThis: function () {
                   return function () {
                       console.log(this);
                   }
               }
           }
           obj.getThis()(); //window
   ```

   上面代码中，getThi()方法是由obj调用，但是obj.getThis()返回的是一个匿名函数，而匿名函数中的this指向window，所以打印出window。 如果想在上述代码中使this指向调用该方法的对象，可以提前把this传值给另外一个变量(_this或者that)： 

   ```js
   let obj = {
               getThis: function () {
               //提前保存this指向
                   let _this=this
                   return function () {
                       console.log(_this);
                   }
               }
           }
           obj.getThis()(); //obj
   ```

3. 箭头函数中的this:

   箭头函数中的this是在函数定义的时候就确定下来的,而不是在函数调用的时候确定;

   箭头函数中的this指向父级作用域的执行上下文(**技巧: 因为javaScript除了全局作用域,其他作用域都是有函数创建出来的,所以如果想确定this的指向,则找到离箭头函数最近的function,与该function平级的执行上下文中的this即是箭头函数中的this**)

   箭头函数无法使用apply,call和bind改变this指向,因为其this值在函数定义的时候就被确定下来.

   (1)eg: 首先,距离箭头函数最近的是getThis(),与该函数平级的执行上下文是obj中的执行上下文,箭头函数中的this就是下注释代码处的this,即obj.

   ```js
     let obj = {
               //此处的this即是箭头函数中的this
               getThis: function () {
                   return  ()=> {
                       console.log(this);
                   }
               }
           }
           obj.getThis()(); //obj
   ```

   (2)eg: 该段代码中存在两个箭头函数,this找不到对应的function(){},所以一直往上找直到指向window.

   ```js
    //代码中有两个箭头函数，由于找不到对应的function，所以this会指向window对象。
          let obj = {
               getThis: ()=> {
                   return  ()=> {
                       console.log(this);
                   }
               }
           }
           obj.getThis()(); //window
   ```

4. 手写bind

   1.Function.prototype.bind, 这样就可以让所有函数的隐式原型上都会有一个bind.

   ```js
   Function.prototype.bind = function() {
       // TODO
   }
   ```

   2.bind的第一个形参就是要绑定给函数的上下文,所以再完善一下上面的代码.

   ```js
   Function.prototype.bind = function(context) {
       var fn = this;
       return function() {
           return fn.apply(context);
       }
   }
   ```

   3.真正的bind函数是可以传递多个参数的,第一个参数是要绑定给调用它的函数上下文,其他的参数将会作为预设参数传递给这个函数,如下所示

   ```js
   let foo = function(){
       console.log(arguments);
   }
   foo.bind(null,"a","b")("c","d","e"); // {"1":"a","2":"b","3":"c","4":"d","5":"e"}
   ```

   为了实现上面的效果,我们发现只要在返回值上将函数合并上去就行了.

   ```js
   Function.prototype.bind = function(context, ...args) {
       var fn = this;
       return function(...rest) {
           return fn.apply(context,[...args, ...rest]);
       }
   }
   ```

5. **箭头函数能否当构造函数**

   箭头函数表达式的语法比函数表达式更简洁,并没有自己的this,arguments,super或new.target.箭头函数表达式更适用于那些本来需要匿名函数的地方,并且它不能用作构造函数.

6. **继承的优缺点**

   继承的好处:

    a: 提高了代码的复用性

    b: 提高了代码的维护性

    c: 让类与类之间产生了关系,是多态的前提

   继承的弊端:

   类的耦合性增强了,但是开发的原则:高内聚,低耦合.

7. js继承的方法和优缺点:

   (1)原型链继承

   实现方式: 将子类的原型链指向父类的对象实例.

   ```js
   function Parent(){
     this.name = 'parent';
     this.list = ['a'];
   }
   Parent.prototype.sayHi = function(){
     console.log('hi');
   }
   function Child(){
   
   }
   Child.prototype = new Parent();
   var child  = new Child();
   console.log(child.name);
   child.sayHi();
   ```

   原理: 子类实例child的 __proto__ 指向Child的原型链prototype,而Child.prototype指向Parent类的对象实例,该父类对象实例的 __proto__ 指向Parent.prototype,所以Child可继承Parent的构造函数属性,方法和原型属性,方法.

   优点: 可继承构造函数传参,父类构造函数的属性,父类原型的属性.

   缺点: 无法向父类构造函数传参,且所有实例共享父类实例的属性;若父类共有属性为引用类型,一个子类实例更改父类构造函数共有属性时会导致继承的共有属性发生变化.

   ```js
   var a = new Child();
   var b = new Child();
   a.list.push('b');
   console.log(b.list); // ['a','b']
   ```

   2.构造函数继承

   实现方式:在子类构造函数中使用call或者apply劫持父类构造函数方法,并传入参数:

   ```js
   function Parent(name, id){
     this.id = id;
     this.name = name;
     this.printName = function(){
       console.log(this.name);
     }
   }
   Parent.prototype.sayName = function(){
     console.log(this.name);
   };
   function Child(name, id){
     Parent.call(this, name, id);
     // Parent.apply(this, arguments);
   }
   var child = new Child("jin", "1");
   child.printName(); // jin
   child.sayName() // Error
   ```

   原理: 利用call或者apply更改子类函数的作用域,使this执行父类构造函数,子类因此可以继承父类共有属性.

   优点:可以解决原型链继承的缺点.

   缺点: 不可继承父类的原型链方法,构造函数不可复用.

   3.组合继承

   原理: 组合继承使用构造函数继承和原型链继承

   ```js
   function Parent(name, id){
     this.id = id;
     this.name = name;
     this.list = ['a'];
     this.printName = function(){
       console.log(this.name);
     }
   }
   Parent.prototype.sayName = function(){
     console.log(this.name);
   };
   function Child(name, id){
     Parent.call(this, name, id);
     // Parent.apply(this, arguments);
   }
   Child.prototype = new Parent();
   var child = new Child("jin", "1");
   child.printName(); // jin
   child.sayName() // jin
   
   var a = new Child();
   var b = new Child();
   a.list.push('b');
   console.log(b.list); // ['a']
   ```

   优点: 可以继承父类原型上的属性,且可传参,每个新实例引入的构造函数是私有的.

   缺点: 会执行两次父类的构造函数,消耗较大内存,子类的构造函数会代替原型上的那个父类构造函数.

   4.原型式继承

   原理: 类似Object.create,用一个函数包装一个对象,然后返回这个函数的调用,这个函数就变成了个可以随意增添属性的实例或对象,结果是将子对象 __ptoto__ 指向父对象.

   ```js
   var parent = {
     names:['a']
   }
   function copy(object){
     function F(){}
     F.prototype = object;
     return new F();
   }
   var child = copy(parent);
   ```

   缺点: 共享引用类型

   5.寄生式继承

   原理:二次封装原型式继承,并拓展

   ```js
   function createObject(obj) {
     var o = copy(obj);
     o.getNames = function() {
       console.log(this.names);
       return this.names;
     }
     return o;
   }
   ```

   优点: 可添加新的属性和方法.

8. **new会发生什么**

   1. 创建空对象

      ```js
      var obj = {}
      ```

   2. 设置新对象的constructor属性为构造函数的名称,设置新对象的proto属性指向构造函数的prototype对象.

      ```js
      obj.__proto__ = ClassA.prototype;
      ```

      扩展了新对象的原型链

   3. 使用新对象调用函数,函数中的this被指向新实例对象;

      ```js
      ClassA.call(obj); // {}.构造函数()
      ```

   4. 返回this指针.当存在显示的返回时,返回return 后面的内容.新建的空对象作废.

      ```js
      function test() {
       this.name = "test";
       }
       test.prototype = {
       a:{},
       b:{}
       }
      
      var  c = new test();
      ```

      

