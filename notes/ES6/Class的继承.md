# Class的继承

Class可以通过extends关键字实现继承,这笔ES5的通过修改原型链实现继承要清晰和方便很多.

```js
class Point {
}
class ColorPoint extends Point {
}
```

上面代码定义了一个`ColorPoint`类，该类通过`extends`关键字，继承了`Point`类的所有属性和方法。但是由于没有部署任何代码，所以这两个类完全一样，等于复制了一个`Point`类。下面，我们在`ColorPoint`内部加上代码。 

```js
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }
  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```

上面代码中，`constructor`方法和`toString`方法之中，都出现了`super`关键字，它在这里表示父类的构造函数，用来新建父类的`this`对象。 

子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错。这是因为子类自己的`this`对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用`super`方法，子类就得不到`this`对象。 

```js
class Point { /* ... */ }
class ColorPoint extends Point {
  constructor() {
  }
}
let cp = new ColorPoint(); // ReferenceError
```

上面代码中,ColorPoint继承了父类的Ponit,但是它的构造函数没有调用super方法,导致新建实例时报错.

ES5的继承,实质是先创造子类的实例对象this,然后再将父类的方法添加到this上面.ES6的继承机制完全不同,实质是先将父类实例对象的属性和方法加到this上面(所以必须先调用suoer方法),然后再用子类的构造函数修改this.

如果子类没有定义constructor方法,这个方法会被默认添加,代码如下.也就是说,不管有没有显示定义,任何一个子类都i有constructor方法.

```js
class ColorPoint extends Point {
}
// 等同于
class ColorPoint extends Point {
  constructor(...args) {
    super(...args);
  }
}
```

另一个需要注意的地方是,在子类的构造函数中,只有调用super之后,才可以使用this关键字,否则会报错.这时因为子类实例的构建,基于父类实例,只有super方法才能调用父类实例.

```js
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
}
class ColorPoint extends Point {
  constructor(x, y, color) {
    this.color = color; // ReferenceError
    super(x, y);
    this.color = color; // 正确
  }
}
```

## Object.getPrototypeOf()

Object.getPrototypeOf方法可以用来从子类上获取父类.

```js
Object.getPrototypeOf(ColorPoint) === Point
// true
```

因此,可以使用这个方法判断,一个类是否继承了另一个类.

## super关键字

super这个关键字,既可以当作函数使用,也可以当作对象使用.

第一种情况,super作为函数调用时,代表父类的构造函数.ES6要求,子类的构造函数必须执行一次super函数.

注意,super虽然代表了父类A的构造函数,但是返回的是子类B的实例,即super内部的this指的是B的实例,因此,super()在这里相当于`A.prototype.constructor.call(this)`。 

```js
class A {
  constructor() {
    console.log(new.target.name);
  }
}
class B extends A {
  constructor() {
    super();
  }
}
new A() // A
new B() // B
```

作为函数,super()只能用在子类的构造函数之中,用在其他地方就会报错.

第二种情况,super作为对象时,在普通方法中,指向父类的原型对象;在静态方法中,指向父类.

```js
class A {
  p() {
    return 2;
  }
}
class B extends A {
  constructor() {
    super();
    console.log(super.p()); // 2
  }
}
let b = new B();
```

这里需要注意,由于super指向父类的原型对象,所以定义在父类实例上的方法或属性,是无法通过super调用的.

```js
class A {
  constructor() {
    this.p = 2;
  }
}
class B extends A {
  get m() {
    return super.p;
  }
}
let b = new B();
b.m // undefined
```

如果属性定义在父类的原型对象上，`super`就可以取到。 

ES6 规定，在子类普通方法中通过`super`调用父类的方法时，方法内部的`this`指向当前的子类实例。 

```js
class A {
  constructor() {
    this.x = 1;
  }
  print() {
    console.log(this.x);
  }
}
class B extends A {
  constructor() {
    super();
    this.x = 2;
  }
  m() {
    super.print();
  }
}
let b = new B();
b.m() // 2
```

上面代码中，`super.print()`虽然调用的是`A.prototype.print()`，但是`A.prototype.print()`内部的`this`指向子类`B`的实例，导致输出的是`2`，而不是`1`。也就是说，实际上执行的是`super.print.call(this)`。 

由于this指向子类实例,所以通过super对某个属性赋值,这时super就是this,赋值的属性会变成子类实例的属性.

```js
class A {
  constructor() {
    this.x = 1;
  }
}
class B extends A {
  constructor() {
    super();
    this.x = 2;
    super.x = 3;
    console.log(super.x); // undefined
    console.log(this.x); // 3
  }
}
let b = new B();
```

如果`super`作为对象，用在静态方法之中，这时`super`将指向父类，而不是父类的原型对象。 

```js
class Parent {
  static myMethod(msg) {
    console.log('static', msg);
  }
  myMethod(msg) {
    console.log('instance', msg);
  }
}
class Child extends Parent {
  static myMethod(msg) {
    super.myMethod(msg);
  }
  myMethod(msg) {
    super.myMethod(msg);
  }
}
Child.myMethod(1); // static 1
var child = new Child();
child.myMethod(2); // instance 2
```

另外，在子类的静态方法中通过`super`调用父类的方法时，方法内部的`this`指向当前的子类，而不是子类的实例。 

注意，使用`super`的时候，必须显式指定是作为函数、还是作为对象使用，否则会报错。 

## 类的prototype属性和`_proto_`属性

大多数浏览器的 ES5 实现之中，每一个对象都有`__proto__`属性，指向对应的构造函数的`prototype`属性。Class 作为构造函数的语法糖，同时有`prototype`属性和`__proto__`属性，因此同时存在两条继承链。 

（1）子类的`__proto__`属性，表示构造函数的继承，总是指向父类。 

（2）子类`prototype`属性的`__proto__`属性，表示方法的继承，总是指向父类的`prototype`属性。 

```js
class A {
}
class B extends A {
}
B.__proto__ === A // true
B.prototype.__proto__ === A.prototype // true
```

这两条继承链，可以这样理解：作为一个对象，子类（`B`）的原型（`__proto__`属性）是父类（`A`）；作为一个构造函数，子类（`B`）的原型对象（`prototype`属性）是父类的原型对象（`prototype`属性）的实例。 

