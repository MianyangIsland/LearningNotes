# 概述

ES5的对象属性名都是字符串,这容易造成属性名的冲突.比如,我们使用了一个他人提供的对象,但又想为这个对象添加新的方法,新方法的名字就有可能有现有方法产生冲突.如果有一种机制保证每个属性的名字都是独一无二的就好了,这样就从根本上防止属性的冲突.这就是ES6引入Symbo原因.

ES6 引入了一种新的原始数据类型`Symbol`，表示独一无二的值。它是 JavaScript 语言的第七种数据类型，前六种是：`undefined`、`null`、布尔值（Boolean）、字符串（String）、数值（Number）、对象（Object）。 

Symbol值通过Symbol函数生成.这就是说,对象的属性名现在可以有两种类型,一种是原来就有的字符串，另一种就是新增的 Symbol 类型。凡是属性名属于 Symbol 类型，就都是独一无二的，可以保证不会与其他属性名产生冲突。 

```js
let s = Symbol();
typeof s
// "symbol"
```

上面代码中，变量`s`就是一个独一无二的值。`typeof`运算符的结果，表明变量`s`是 Symbol 数据类型，而不是字符串之类的其他类型。 

注意，`Symbol`函数前不能使用`new`命令，否则会报错。这是因为生成的 Symbol 是一个原始类型的值，不是对象。也就是说，由于 Symbol 值不是对象，所以不能添加属性。基本上，它是一种类似于字符串的数据类型。 

symbol函数可以接受一个字符串作为参数,表示对Symbol实例的描述,主要是为了在控制台显示,或者转为字符串时,比较容易区分.

```js
let s1 = Symbol('foo');
let s2 = Symbol('bar');
s1 // Symbol(foo)
s2 // Symbol(bar)
s1.toString() // "Symbol(foo)"
s2.toString() // "Symbol(bar)"
```

上面代码中，`s1`和`s2`是两个 Symbol 值。如果不加参数，它们在控制台的输出都是`Symbol()`，不利于区分。有了参数以后，就等于为它们加上了描述，输出的时候就能够分清，到底是哪一个值。 

如果 Symbol 的参数是一个对象，就会调用该对象的`toString`方法，将其转为字符串，然后才生成一个 Symbol 值。 

```js
const obj = {
  toString() {
    return 'abc';
  }
};
const sym = Symbol(obj);
sym // Symbol(abc)
```

注意，`Symbol`函数的参数只是表示对当前 Symbol 值的描述，因此相同参数的`Symbol`函数的返回值是不相等的。 

```js
// 没有参数的情况
let s1 = Symbol();
let s2 = Symbol();
s1 === s2 // false
// 有参数的情况
let s1 = Symbol('foo');
let s2 = Symbol('foo');
s1 === s2 // false
```

Symbol 值不能与其他类型的值进行运算，会报错。 但是，Symbol 值可以显式转为字符串。 

```js
let sym = Symbol('My symbol');
String(sym) // 'Symbol(My symbol)'
sym.toString() // 'Symbol(My symbol)'
```

另外，Symbol 值也可以转为布尔值，但是不能转为数值。 

```js
let sym = Symbol();
Boolean(sym) // true
!sym  // false
if (sym) {
  // ...
}
Number(sym) // TypeError
sym + 2 // TypeError
```

## Symbol.prototype.description

创建 Symbol 的时候，可以添加一个描述。 

```js
const sym = Symbol('foo');
```

但是，读取这个描述需要将 Symbol 显式转为字符串，即下面的写法。 

```js
const sym = Symbol('foo');
String(sym) // "Symbol(foo)"
sym.toString() // "Symbol(foo)"
```

上面的方法不是很方便,ES2019提供了一实例属性description,直接返回Symbol的描述.

```js
const sym = Symbol('foo');
sym.description // "foo"
```

```js
const s1 = Symbol('getName');

const s2 = Symbol('setName');

let obj = {
    name:'',
    s1:()=>{
      return this.name;
    },
    s2:(value)=>{
      this.name = value;
    }
}

obj.s2('张珂');
console.log(obj.s1());
```

## 作为属性名的Symbol

由于每一个Symbol值都是不相等的,这意味着Symol值可以作为标识符,用于对象的属性名,就能保证不会出现同名的属性.这对于一个对象由多个模块构成的情况非常有用,能防止某一个键被不小心改写或覆盖.

```js
let mySymbol = Symbol();
// 第一种写法
let a = {};
a[mySymbol] = 'Hello!';
// 第二种写法
let a = {
  [mySymbol]: 'Hello!'
};
// 第三种写法
let a = {};
Object.defineProperty(a, mySymbol, { value: 'Hello!' });
// 以上写法都得到同样结果
a[mySymbol] // "Hello!"
```

注意，Symbol 值作为对象属性名时，不能用点运算符。 

```js
const mySymbol = Symbol();
const a = {};
a.mySymbol = 'Hello!';
a[mySymbol] // undefined
a['mySymbol'] // "Hello!"
```

上面代码中，因为点运算符后面总是字符串，所以不会读取`mySymbol`作为标识名所指代的那个值，导致`a`的属性名实际上是一个字符串，而不是一个 Symbol 值。 

同理,在对象的内部,使用Symbol值定义属性时,Symbol值必须放在方括号之中.

```js
let s = Symbol();
let obj = {
  [s]: function (arg) { ... }
};
obj[s](123);
```

常量使用 Symbol 值最大的好处，就是其他任何值都不可能有相同的值了，因此可以保证上面的`switch`语句会按设计的方式工作。 

还有一点需要注意，Symbol 值作为属性名时，该属性还是公开属性，不是私有属性。 

## 实例:消除魔术字符串

魔术字符串指的是,在代码之中多次出现,与代码形成强耦合的某一个具体字符串或数值.风格良好的代码,应该尽量消除魔术字符串,改由含义清晰的变量代替.

```js
function getArea(shape, options) {
  let area = 0;
  switch (shape) {
    case 'Triangle': // 魔术字符串
      area = .5 * options.width * options.height;
      break;
    /* ... more code ... */
  }
  return area;
}
getArea('Triangle', { width: 100, height: 100 }); // 魔术字符串
```

上面代码中，字符串`Triangle`就是一个魔术字符串。它多次出现，与代码形成“强耦合”，不利于将来的修改和维护。 

常用的消除魔术字符串的方法，就是把它写成一个变量。 

```js
const shapeType = {
  triangle: 'Triangle'
};
function getArea(shape, options) {
  let area = 0;
  switch (shape) {
    case shapeType.triangle:
      area = .5 * options.width * options.height;
      break;
  }
  return area;
}
getArea(shapeType.triangle, { width: 100, height: 100 });
```

如果仔细分析,可以发现shapeType.triangle等于那个值并不重要,只要确保不会跟其他shapeType属性的值冲突即可.因此,这里就适合改用Symbol值.

```js
const shapeType = {
  triangle: Symbol()
};
```

上面代码中，除了将`shapeType.triangle`的值设为一个 Symbol，其他地方都不用修改。 

## 属性名的遍历

Symbol 作为属性名，遍历对象的时候，该属性不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`返回。 

但是,它也不是私有属性,有一个Object.getOwnPropertySymbols()方法,可以获取指定对象的所有Symbol属性名.该方法返回一个数组,成员是当前对象的所有用作属性名的Symbol值.

```js
const obj = {};
let a = Symbol('a');
let b = Symbol('b');
obj[a] = 'Hello';
obj[b] = 'World';
const objectSymbols = Object.getOwnPropertySymbols(obj);
objectSymbols
// [Symbol(a), Symbol(b)]
```

上面代码是`Object.getOwnPropertySymbols()`方法的示例，可以获取所有 Symbol 属性名。 

下面是另一个例子,Object.getOwnPropertySymbol()方法与for .. in 循环,Object.getOwnPropertyNames方法进行对比的例子.

```js
const obj = {};
const foo = Symbol('foo');
obj[foo] = 'bar';
for (let i in obj) {
  console.log(i); // 无输出
}
Object.getOwnPropertyNames(obj) // []
Object.getOwnPropertySymbols(obj) // [Symbol(foo)]
```

另一个新的 API，`Reflect.ownKeys()`方法可以返回所有类型的键名，包括常规键名和 Symbol 键名。 

```js
let obj = {
  [Symbol('my_key')]: 1,
  enum: 2,
  nonEnum: 3
};
Reflect.ownKeys(obj)
//  ["enum", "nonEnum", Symbol(my_key)]
```

由于以 Symbol 值作为键名，不会被常规方法遍历得到。我们可以利用这个特性，为对象定义一些非私有的、但又希望只用于内部的方法。 

## Symbol.for(),Symbol.keyFor()

有时,我们希望重新使用同一个Symbol值,symbol.for()方法可以做到这一点,它接受有一个字符串作为参数,然后搜索有没有以该参数作为名称的Symbol值,如果有,就返回这个Symbol值,否则就新建一个以该字符串为名称的Syymbol值,并将其注册到全局.

```js
let s1 = Symbol.for('foo');
let s2 = Symbol.for('foo');
s1 === s2 // true
```

`Symbol.for()`与`Symbol()`这两种写法，都会生成新的 Symbol。它们的区别是，前者会被登记在全局环境中供搜索，后者不会。`Symbol.for()`不会每次调用就返回一个新的 Symbol 类型的值，而是会先检查给定的`key`是否已经存在，如果不存在才会新建一个值。比如，如果你调用`Symbol.for("cat")`30 次，每次都会返回同一个 Symbol 值，但是调用`Symbol("cat")`30 次，会返回 30 个不同的 Symbol 值。 

```js
Symbol.for("bar") === Symbol.for("bar")
// true
Symbol("bar") === Symbol("bar")
// false
```

`Symbol.keyFor()`方法返回一个已登记的 Symbol 类型值的`key`。 

```js
let s1 = Symbol.for("foo");
Symbol.keyFor(s1) // "foo"
let s2 = Symbol("foo");
Symbol.keyFor(s2) // undefined
```

注意，`Symbol.for()`为 Symbol 值登记的名字，是全局环境的，不管有没有在全局环境运行。 

```js
function foo() {
  return Symbol.for('bar');
}
const x = foo();
const y = Symbol.for('bar');
console.log(x === y); // true
```

## 实例: 模块的Singleton模式

Singleton模式指的是调用一个类,任何时候返回的都是同一个实例.

对于 Node 来说，模块文件可以看成是一个类。怎么保证每次执行这个模块文件，返回的都是同一个实例呢？

很容易想到，可以把实例放到顶层对象`global`。

```js
// mod.js
function A() {
  this.foo = 'hello';
}
if (!global._foo) {
  global._foo = new A();
}
module.exports = global._foo;
```

然后，加载上面的`mod.js`。 

```js
const a = require('./mod.js');
console.log(a.foo);
```

上面代码中，变量`a`任何时候加载的都是`A`的同一个实例。

但是，这里有一个问题，全局变量`global._foo`是可写的，任何文件都可以修改。

```js
global._foo = { foo: 'world' };const a = require('./mod.js');console.log(a.foo);
```

上面的代码，会使得加载`mod.js`的脚本都失真。

为了防止这种情况出现,mod.js的脚本都失真.

```js
// mod.js
const FOO_KEY = Symbol.for('foo');
function A() {
  this.foo = 'hello';
}
if (!global[FOO_KEY]) {
  global[FOO_KEY] = new A();
}
module.exports = global[FOO_KEY];
```

上面代码中，可以保证`global[FOO_KEY]`不会被无意间覆盖，但还是可以被改写。

```js
global[Symbol.for('foo')] = { foo: 'world' };const a = require('./mod.js');
```

如果键名使用`Symbol`方法生成，那么外部将无法引用这个值，当然也就无法改写。

```js
// mod.jsconst FOO_KEY = Symbol('foo');// 后面代码相同 ……
```

上面代码将导致其他脚本都无法引用`FOO_KEY`。但这样也有一个问题，就是如果多次执行这个脚本，每次得到的`FOO_KEY`都是不一样的。虽然 Node 会将脚本的执行结果缓存，一般情况下，不会多次执行同一个脚本，但是用户可以手动清除缓存，所以也不是绝对可靠。