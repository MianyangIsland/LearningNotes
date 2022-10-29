### 构造函数和对象

  在JS中函数是一等公民,我们可以通过构造函数来创建对象,所谓构造函数,就是提供了一个生成对象的模板并描述对象的基本结构的函数.一个构造函数,可以生成多个对象,每个对象都有相同的结构.总的来说,构造函数就是对象的模板,对象就是构造函数的实例.

```js
function Person(name){
    this.name = name;
    this.sayName = function(){
        return this.name;
    }
};
let person1 = new Person('xyf1');
let person2 = new Person('xyf2');
console.log(person1.constructor === Person);//true
```

我们通过new来构造实例化对象,类函数中的this总是指向实例化对象,每一个实例化对象都有一个不可枚举的属性constructor属性来指向构造函数,即Person.

![class1](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/171796141d0c6730~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp) 

## 构造函数缺点

 所有的实例对象都会单独创建自己的属性和方法,不同实例对象之间无法共享通用的属性.

```js
//xyf1
person1.sayName()
//xyf2
person2.sayName()
//false
person1.sayName === person2.sayName
```

但是有的属性或方法是共有的,我们希望每个实例对象创建的时候就能有.

## 原型对象prototype

为了解决实例对象之间共享属性的问题,JS提供了prototype属性.

```js
function Person(name){
    this.name = name;
    this.sayName = function(){
        return this.name;
    }
}

Person.prototype.cry = function(){
    console.log('cry');
}

let person1 = new Person('xyf1');
let person2 = new Person('xyf2');
console.log(person1.cry === person2.cry);
```

prototype是从一个函数指向一个对象,即函数才有prototype属性.它的作用是让该构造函数创建的所有实例对象们都能找到公有属性和方法.任何函数在创建实例对象的时候,其实会关联该函数的prototype对象.

![class2.jpg](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/1717961438b9bc37~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp) 

需要注意的是,我们可以修改原型对象的引用,但是仍需要把constructor属性指向回构造函数;上面的cry函数绑定我们可以这样改写:

```js
Person.prototype = {
    constructor: Person,
    cry: function(){
        console.log('cry')
    }
}
```

### 原型链

有了原型对象,我们知道了,实例对象的属性和方法,有可能是定义在自身,也有可能是定义在它的原型对象上.通过上面的cry函数我们可以看出,实例对象能够直接获取原型对象上的属性和方法,那么它是怎么获取的呢? 通过**_proto_**

因此*_proto_*指向了实例对象的原型对象,当你访问一个对象上没有的属性时,对象就会去`_proto_`上面找,如果还是找不到,就会继续找原型对象的`_proto_`,直到原型对象为null,因此`_proto_`构成了一条原型链.

```js
function Person(name){
    this.name = name;
    this.sayName = function(){
        return this.name;
    }
}

Person.prototype.cry = function(){
    console.log('cry');
}

let person1 = new Person('xyf1');
console.log(person1.__proto__ === Person.prototype);//true
```

同时我们也解答了上面实例对象上没有constructor属性的问题,constructor属性真正存在于原型对象上,所以实例对象才能获取到,我们继续完善原型图(虚线表示该属性或方法并不是真正存在).

![class3.jpg](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/1717961501898fd6~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp) 

同时,原型对象也是一个对象,既然是对象,那么肯定也有它自己的原型对象,那么它的原型对象是谁呢? 我们知道,JS中所有的对象都是Object的实例,并继承Object.prototype的属性和方法;字面量 var a ={} 实际上也是new Object()的语法糖.因此:

```js
var obj1 = {}
var obj2 = new Object()
//true
obj1.__proto__ === Object.prototype
//true
obj2.__proto__ === Object.prototype
//true
Person.prototype.__proto__ === Object.prototype
```

我们继续完善原型图:

![class4.jpg](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/17179614de7ae893~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp) 

### constructor

我们说过constructor用来指向构造函数;同时,constructor真正存在于原型对象上,因此,我们可以得到下面的等式关系:

```js
//true
Person.prototype.constructor === Person
```

在学数据类型判断的时候学过,constryctor可以用来数据类型的判断:

```js
console.log((2).constructor === Number); // true 
console.log((true).constructor === Boolean);// true 
console.log(('str').constructor === String);// true 
console.log(([]).constructor === Array);// true 
console.log((function() {}).constructor === Function);// true 
console.log(({}).constructor === Object);// true 
```

这种方式看起来能判断所有类型,但是一旦我们更改了原型对象,这种方式就不可靠了.

```js
function Fn(){};
Fn.prototype = new Array();

var f = new Fn();
console.log(f.constructor === Fn);//false
console.log(f.constructor === Array);//true
```

在JS中,函数本身也可以看成对象,对这种又是函数,又是对象,有一个特殊的称呼:函数对象; 我们调用函数的fn.call 和 fn.apply其实调用的是继承自其原型对象上的Function.prototype.call和Function.prototype.apply,因此函数都是Function函数的实例对象;既然是实例对象,所以Person函数也拥有____proto____和constructor属性,我们来看一个函数的属性:

```js
//ƒ Function() { [native code] }
console.log(Person.constructor)
//true
Person.constructor === Function
//false
Person.hasOwnProperty('constructor')
//ƒ () { [native code] }
console.log(Person.__proto__)
//true
Person.__proto__.hasOwnProperty('constructor')
//true
Function.prototype.hasOwnProperty('constructor')
```

可以看出来,Person构造函数和JS普通的对象没有任何区别,有自己的constructor属性,指向Function函数,说明Person函数是Function的实例对象;而且constructor属性不再Person本身,其在其原型对象Function.prototype上,因此我们再次完善一下原型图:

![class5.jpg](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/17179615cca2681f~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp) 

到这里,我们发现最终原型图指向四个基本的东西:Object,Object.prototype,Function,Function.prototype,他们之间的关系是整个原型关系里面最难理解的,为了避免干扰,我们给他们四个单独开个图:

![Object-Function1.jpg](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/17179615a97cf13d~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp) 

我们知道Object函数和Person函数一样,都是函数对象,因此都是Function函数的实例对象.

```js
//true
Object.constructor === Function
//true
Object instanceof Function
//true
Object instanceof Object
//false
Object.hasOwnProperty('constructor')
//true
Object.__proto__ === Function.prototype
```

因此我们完善Object和Function的关系:

![Object-Function2.jpg](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/1717961fcb8df7c8~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp) 

既然Object是构造函数,我们又想起Function也能通过new Function()来构造匿名函数,同时自己又是自己的constructor.

```js
//true
Function.constructor === Function
//true
Function instanceof Object
//true
Function instanceof Function
//false
Function.hasOwnProperty('constructor')
//true
Function.__proto__ === Function.prototype
```

 同时我们猜测`Function.prototype`和`Person.prototype`一样是个对象，因此它的原型对象肯定就是`Object.prototype`。 

```js
//true
Function.prototype.__proto__ === Object.prototype
```

我们继续完善原型图:

![Object-Function3.jpg](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/1717961d9a0f83c9~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp) 

这样,整个原型链最有意思的一幕出现了;Object是构造函数,继承了Function.prototype; Function也是对象,继承了Object.prototype,那么到底是现有Object,还是现有Function? 这似乎是一个无解的问题.

根本原因在于`Function.__proto__`指向了`Function.prototype`，让`Function`继承了`Object.prototype`上的方法，因此我们需要对`Function.prototype`来进一步的了解： 

```js
//function
typeof Function.prototype
//undefined
Function.prototype()
//true
Function.prototype instanceof Object
//false
Function.prototype instanceof Function
//undefined
Function.prototype.prototype
```

我们发现Function.prototype是个特殊的函数对象,但是没有prototype属性;针对上面的代码,我们梳理以下几点:

 

- `Function.prototype`像普通函数一样可以调用，但总是返回`undefined`
- `Function.prototype`继承于`Object.prototype`，并且没有`prototype`这个属性

因此,Function.prototype是个标准的内置对象,他继承于Object.prototype,而我们知道object.prototype === null,说明原型链到Object.prototype就终止了.

总结: 先有Object.prototype(原型链顶端),Function.prototype继承Object.prototype而产生,最后,Function和Object和其他构造函数继承Function.prototype而产生.

### 静态属性和方法

所谓的静态方法,是指不需要声明类的实例就可以使用的方法.

在ES5中,我们直接将它作为类函数的属性即可:

```js
function Person(){}
Person.staticSay = function(){
    console.log('static say')
    //ƒ Person(){}
    console.log(this)
}
Person.num = 10
Person.staticSay()
//10
console.log(Person.num)
```

静态方法和实例方法最主要的区别就是实例方法可以访问到实例对象,可以对实例进行操作,而静态方法一般用于跟实例无关的操作.

### 手写instanceof

 除了constructor,我们还有instanceof来进行数据类型的判断; instanceof 主要用来判断一个实例是否属于某种类型, 简单用法:

```js
//true
[] instanceof Array
//true
person1 instanceof Person
//true
person1 instanceof Object
```

 instanceof第一个变量是一个对象A, 第二个变量是一个函数B,沿着A的原型链__proto__一直向上找,如果能找到一个__proto__等于B的prototype,则返回true,如果找到终点还没找到则返回false.

```js
function _instanceOf(A,B){
    if(!A || !B){
        return false;
    }
    let o = B.prototype;
    A = A.__proto__;
    while(true){
        if(A === null){
            return false;
        }
        else if(o === A){
            return true;
        }
        else{
            A = A.__proto__;
        }
    }
}
```

```js
function _instanceOf(A,B){
    if(!A || !B){
        return false;
    }
    let o = B.prototype;
    A = A.__proto__;
    while(true){
        if(A === null){
            return false;
        }
        else if(o === A){
            return true;
        }
        else{
            A = A.__proto__;
        }
    }
}

let Person = new Function();

console.log(_instanceOf(Person,Function));//true
```

### 手写new

 通过上面的原型链,我们知道了new本质上就是调用构造函数生成一个对象,这个对象能够访问构造函数的原型对象,因此我们来尝试模拟以下new的实现.

```js
function myNew(Fn){
    let obj = new Object();
    Fn.call(obj);
    obj.__proto__ = Fn.prototype;
    return obj;
}

let newObj = myNew(Person);
console.log(newObj);
```

我们首先构建了一个空对象；然后将空对象作为this，调用构造函数绑定参数；最后将该对象的__proto__指向构造函数的原型对象。 



可以看到生成出来的对象该有的属性都有了,原型链也绑定成功了,但是存在的问题就是不能传参,因此我们进行下一改进:

```js
function myNew(Fn, ...param){
    var obj = new Object()
    Fn.call(obj, ...param)
    obj.__proto__ = Fn.prototype
    return obj
}
var newObj = myNew(Person, 'new obj')
console.log(newObj)
```

 可以看到返回的对象已经和原生new生成出来的几乎一模一样了。但是我们对构造函数进行一些修改： 

```js
function Person(name) {
  this.name = name
  this.sayName = function() {
    return this.name
  }
  return 2
//   return 'hello'
//   return {a:3}
//   return []
//   return function(){}
}
```

 我们在构造函数中返回了多种类型，经过测试发现：**如果构造函数返回引用类型，new生成的就是返回的对象；如果返回基本数据类型，new生成新的对象。**因此我们终极版的new函数如下： 

```js
function myNew(Fn, ...param){
    var obj = new Object()
    var result = Fn.call(obj, ...param)
    obj.__proto__ = Fn.prototype
    return typeof result === 'object' 
        || typeof result === 'function' ? result : obj
}
```

### ES5继承

所谓的继承,就是把子类继承父类所有对的属性和方法;同时我们也知道父类上的属性和方法不仅在自身构造函数,原型链上也会有属性和方法,因此我们也需要继承过来.

 既然继承是继承父类的属性和方法,那么我们上面的myNew函数也相当于是一种继承;

原型链继承:

```js
function Parent () {
    this.name = 'xyf';
    this.sayName = function() {
        return this.name
    }
}
Parent.prototype.cry = function () {
    console.log('cry')
}
function Child () {
}
Child.prototype = new Parent();
var child1 = new Child();
console.log(child1.sayName())
child1.cry()
```

我们把父类的实例挂在到子类的原型上,那么所有的子类就能访问到父类的属性和方法,但是由于所有子类共享原型对象,所以会存在以下问题:

- 问题1：父类引用类型的属性被子类共享，一旦改变，所有子类实例引用的都将改变。

```js
function Parent () {
    this.name = ['xyf1', 'xyf2'];
}
function Child () {
}
Child.prototype = new Parent();

var child1 = new Child();
child1.name.push('xyf3')
//["xyf1", "xyf2", "xyf3"]
console.log(child1.name);

var child2 = new Child();
//["xyf1", "xyf2", "xyf3"]
console.log(child2.name);
```

- 问题2：创建子类实例的时候，不能向父类传参数。
- 问题3：不能继承静态方法。

构造函数继承:

```js
function Parent (name) {
    this.name = name;
    this.color = ['red','yellow']
}
Parent.prototype.cry = function(){
    console.log('cry')
}
function Child (name) {
    Parent.call(this, name);
}

var child1 = new Child('xyf');
child1.color.push('blue');
//['red','yellow','blue']
console.log(child1.color);
//undefined
console.log(child1.cry)

var child2 = new Child('corner');
//['red','yellow']
console.log(child2.color);
```

每次创建子类实例的时候调用父类的构造函数,避免了引用类型的属性被所有实例共享,也可以向父类传参;但是没有继承父类原型上的属性和方法.

组合继承

```js
function Parent (name) {
    this.name = name;
    this.color = ['red','yellow']
}
Parent.prototype.cry = function(){
    console.log('cry')
}
function Child (name) {
    Parent.call(this, name);
}
Child.prototype = new Parent()
```

融合了原型链继承和构造函数继承的优点,是JS中常用的继承方式.

ES6继承

  ES6新增了`class`关键词，用来定义一个类，和JAVA中的有种似曾相识的感觉；但是本质上其实是ES5构造函数的语法糖，大多部分功能ES5都能实现： 

```js
class Person{
    constructor(name){
        this.name = name
    }
    sayName(){
        return this.name
    }
    static staticSay() {
        console.log('static say');
    }
}
Person.prototype.cry = function(){
    console.log('cry')
}
//static say
Person.staticSay()

var person1 = new Person('corner')
//cry
person1.cry()
//corner
person1.sayName()
//true
person1.constructor === Person
```

ES6的继承可以通过`extends`关键词实现，比ES5的修改原型链实现继承要更清晰和方便： 

```js
class Person{
    constructor(name){
        this.name = name
    }
    sayName(){
        return this.name
    }
    static staticSay() {
        console.log('static say');
    }
}
Person.prototype.cry = function(){
    console.log('cry')
}

class Child extends Person{
    constructor(name,color){
        super(name)
        this.color = color
    }
    sayColor(){
        return 'my color is:'+ this.color
    }
}
var child1 = new Child('child1', 'black')
//child1
console.log(child1.sayName())
//my color is:black
console.log(child1.sayColor())
//cry
child1.cry()
//static say
Child.staticSay()
```

可以很清晰的看出来子类继承了父类本身呢个以及原型上的属性和方法.同时,在ES5中所有的继承我们都发现不支持静态函数的继承,但是在es6中支持.

