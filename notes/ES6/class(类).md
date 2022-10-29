# Class (类) 

## 简介

类的由来

JavaScript 语言中，生成实例对象的传统方法是通过构造函数。下面是一个例子。 

```js
function Point(x, y) {
  this.x = x;
  this.y = y;
}
Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};
var p = new Point(1, 2);
```

ES6提供了更接近传统语言的写法,引入了Class(类)这个概念,作为对象的模板.通过class关键字,可以定义类.

基本上,ES6的class可以看作只是一个语法糖,它的绝大部分功能,ES5都可以做到,新的class写法只是让对象原型的写法更加清晰,更像面向对象编程的语法而已.上面的代码用ES6的class改写,就是下面这样:

```js
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
```

上面代码定义了一个“类”，可以看到里面有一个`constructor`方法，这就是构造方法，而`this`关键字则代表实例对象。也就是说，ES5 的构造函数`Point`，对应 ES6 的`Point`类的构造方法。 

`Point`类除了构造方法，还定义了一个`toString`方法。注意，定义“类”的方法的时候，前面不需要加上`function`这个关键字，直接把函数定义放进去了就可以了。另外，方法之间不需要逗号分隔，加了会报错。 

ES6 的类，完全可以看作构造函数的另一种写法。 

```js
class Point {
  // ...
}
typeof Point // "function"
Point === Point.prototype.constructor // true
```

使用的时候，也是直接对类使用`new`命令，跟构造函数的用法完全一致。 

```js
class Bar {
  doStuff() {
    console.log('stuff');
  }
}
var b = new Bar();
b.doStuff() // "stuff"
```

构造函数的prototype属性,在ES6的"类"上面继续存在.事实上,类的所有方法都定义在类的prototype属性上面.

```js
class Point {
  constructor() {
    // ...
  }
  toString() {
    // ...
  }
  toValue() {
    // ...
  }
}
// 等同于
Point.prototype = {
  constructor() {},
  toString() {},
  toValue() {},
};
```

在类的实例上面调用方法,其实就是调用原型上的方法.

```js
class B {}
let b = new B();
b.constructor === B.prototype.constructor // true
```

由于类的方法都定义在`prototype`对象上面，所以类的新方法可以添加在`prototype`对象上面。`Object.assign`方法可以很方便地一次向类添加多个方法。 

```js
class Point {
  constructor(){
    // ...
  }
}
Object.assign(Point.prototype, {
  toString(){},
  toValue(){}
});
```

`prototype`对象的`constructor`属性，直接指向“类”的本身 

```js
Point.prototype.constructor === Point // true
```

另外，类的内部所有定义的方法，都是不可枚举的（non-enumerable）。 这一点与ES5的行为不一致.

## constructor方法

constructor方法是类的默认方法,通过new命令生成对象实例时,自动调用该方法.一个类必须有constructor方法,如果没有显示定义,一个空的constructor方法会被默认添加.

```js
class Point {
}
// 等同于
class Point {
  constructor() {}
}
```

constructor方法默认返回实例对象(即this),完全可以指定返回另外一个对象.

```js
class Foo {
  constructor() {
    return Object.create(null);
  }
}
new Foo() instanceof Foo
// false
```

类必须使用new调用,否则会报错.这是它跟普通构造函数的一个主要区别,后者不用new也可以执行.

## 类的实例

生成类的实例的写法,与ES5完全一样,也是使用new命令.与ES5一样,实例的属性除非显示定义在其本身(即定义在this对象上),否则都是定义在原型上(即定义在class上).

与ES5一样,类的所有实例共享一个原型对象.

```js
var p1 = new Point(2,3);
var p2 = new Point(3,2);
p1.__proto__ === p2.__proto__
//true
```

上面代码中，`p1`和`p2`都是`Point`的实例，它们的原型都是`Point.prototype`，所以`__proto__`属性是相等的。 

这也意味着,可以通过实例的`_proto_`属性为"类"添加方法.

## 取值函数(getter)和存值函数(setter)

与Es5一样,在"类"的内部可以使用get和set关键字,对某个属性设置存值函数和取值函数,拦截该属性的存取行为.

```js
class MyClass {
  constructor() {
    // ...
  }
  get prop() {
    return 'getter';
  }
  set prop(value) {
    console.log('setter: '+value);
  }
}
let inst = new MyClass();
inst.prop = 123;
// setter: 123
inst.prop
// 'getter'
```

存值函数和取值函数是设置在属性的Descriptor对象上的.

## 属性表达式

类的属性名,可以采用表达式.

```js
let methodName = 'getArea';
class Square {
  constructor(length) {
    // ...
  }
  [methodName]() {
    // ...
  }
}
```



## Class表达式

```js
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
```

上面代码使用表达式定义了一个类。需要注意的是，这个类的名字是`Me`，但是`Me`只在 Class 的内部可用，指代当前类。在 Class 外部，这个类只能用`MyClass`引用。 

```js
let inst = new MyClass();
inst.getClassName() // Me
Me.name // ReferenceError: Me is not defined
```

上面代码表示，`Me`只在 Class 内部有定义。

如果类的内部没用到的话，可以省略`Me`，也就是可以写成下面的形式。

```js
const MyClass = class { /* ... */ };
```

采用 Class 表达式，可以写出立即执行的 Class。 

```js
let person = new class {
  constructor(name) {
    this.name = name;
  }
  sayName() {
    console.log(this.name);
  }
}('张三');
person.sayName(); // "张三"
```

## 注意点

(1)不存在提升

类不存在变量提升,这一点与ES5完全不同.

```js
new Foo(); // ReferenceError
class Foo {}
```

上面代码中,Foo类使用在前,定义在后,这样会报错,因为ES6不会把类的声明提升到代码头部.

(2)name属性

由于本质上,ES6的类只是ES5的构造函数的一层包装,所以函数的许多特性都被Class继承,包括name属性.

```js
class Point {}
Point.name // "Point"
```

`name`属性总是返回紧跟在`class`关键字后面的类名。 

(4)Generator方法

如果在某个方法之前加上星号(*),就表示该方法是一个Generator函数.

```js
class Foo {
  constructor(...args) {
    this.args = args;
  }
  * [Symbol.iterator]() {
    for (let arg of this.args) {
      yield arg;
    }
  }
}
for (let x of new Foo('hello', 'world')) {
  console.log(x);
}
// hello
// world
```

上面代码中，`Foo`类的`Symbol.iterator`方法前有一个星号，表示该方法是一个 Generator 函数。`Symbol.iterator`方法返回一个`Foo`类的默认遍历器，`for...of`循环会自动调用这个遍历器。 

(4)this的指向

类的方法内部如果包含this,它默认指向类的实例.但是,必须非常小心,一旦单独使用该方法,很可能报错.

```js
class Logger {
  printName(name = 'there') {
    this.print(`Hello ${name}`);
  }
  print(text) {
    console.log(text);
  }
}
const logger = new Logger();
const { printName } = logger;
printName(); // TypeError: Cannot read property 'print' of undefined
```

上面代码中，`printName`方法中的`this`，默认指向`Logger`类的实例。但是，如果将这个方法提取出来单独使用，`this`会指向该方法运行时所在的环境（由于 class 内部是严格模式，所以 this 实际指向的是`undefined`），从而导致找不到`print`方法而报错。 

一个比较简单的解决方法是,在构造方法中绑定this,这样就不会找不到print方法了.

```js
class Logger {
  constructor() {
    this.printName = this.printName.bind(this);
  }
  // ...
}
```

另一种方法是使用箭头函数.

```js
class Obj {
  constructor() {
    this.getThis = () => this;
  }
}
const myObj = new Obj();
myObj.getThis() === myObj // true
```

箭头函数内部的`this`总是指向定义时所在的对象。上面代码中，箭头函数位于构造函数内部，它的定义生效的时候，是在构造函数执行的时候。这时，箭头函数所在的运行环境，肯定是实例对象，所以`this`会总是指向实例对象。 

## 静态方法

类相当于实例的原型,所有在类中定义的方法,都会被实例继承.如果在一个方法前,加上static关键字,就表示该方法不会被实例继承,而是直接通过类来调用,这就被为"静态方法".

```js
class Foo {
  static classMethod() {
    return 'hello';
  }
}
Foo.classMethod() // 'hello'
var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function
```

注意,如果静态方法包含this关键字,这个this指的是类,而不是实例.

```js
class Foo {
  static bar() {
    this.baz();
  }
  static baz() {
    console.log('hello');
  }
  baz() {
    console.log('world');
  }
}
Foo.bar() // hello
```

父类的静态方法,可以被子类继承.

```js
class Foo {
  static classMethod() {
    return 'hello';
  }
}
class Bar extends Foo {
}
Bar.classMethod() // 'hello'
```

静态方法也是从super对象上调用的.

```js
class Foo {
  static classMethod() {
    return 'hello';
  }
}
class Bar extends Foo {
}
Bar.classMethod() // 'hello'
```

## 实例属性的新写法

实例属性除了定义在constructor()方法里面的this上面,也可以定义在类的最顶层.

```js
class IncreasingCounter {
  _count = 0;
  get value() {
    console.log('Getting the current value!');
    return this._count;
  }
  increment() {
    this._count++;
  }
}
```

## 静态属性

静态属性指的是Class本身的属性,即Class.propName,而不是定义在实例对象(this). 在实例属性的前面,加上static关键字.

```js
class MyClass {
  static myStaticProp = 42;
  constructor() {
    console.log(MyClass.myStaticProp); // 42
  }
}
```

## new.target属性

new是从构造函数生成实例对象的命令.ES6为new命令引入了一个new.target属性,该属性一般用在构造函数之中,返回new命令作用于的那个构造函数.如果构造函数不是通过new命令成Reflect.constructor()调用的,new.target会返回undefined,因此这个属性可以用来确定构造函数是怎么调用的.

```js
function Person(name) {
  if (new.target !== undefined) {
    this.name = name;
  } else {
    throw new Error('必须使用 new 命令生成实例');
  }
}
// 另一种写法
function Person(name) {
  if (new.target === Person) {
    this.name = name;
  } else {
    throw new Error('必须使用 new 命令生成实例');
  }
}
var person = new Person('张三'); // 正确
var notAPerson = Person.call(person, '张三');  // 报错
```

Class 内部调用`new.target`，返回当前 Class。 

```js
class Rectangle {
  constructor(length, width) {
    console.log(new.target === Rectangle);
    this.length = length;
    this.width = width;
  }
}
var obj = new Rectangle(3, 4); // 输出 true
```

需要注意的是，子类继承父类时，`new.target`会返回子类。 

```js
class Rectangle {
  constructor(length, width) {
    console.log(new.target === Rectangle);
    // ...
  }
}
class Square extends Rectangle {
  constructor(length, width) {
    super(length, width);
  }
}
var obj = new Square(3); // 输出 false
```

利用这个特点,可以写出不能独立使用,必须继承后才能使用的类.

```js
class Shape {
  constructor() {
    if (new.target === Shape) {
      throw new Error('本类不能实例化');
    }
  }
}
class Rectangle extends Shape {
  constructor(length, width) {
    super();
    // ...
  }
}
var x = new Shape();  // 报错
var y = new Rectangle(3, 4);  // 正确
```

注意，在函数外部，使用`new.target`会报错。 