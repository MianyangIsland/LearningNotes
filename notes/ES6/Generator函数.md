# 简介

## 基本概念

Generator函数是ES6提供的一种异步编程的解决方案.语法行为与传统函数完全不同 .

Generator 函数有多种理解角度。语法上，首先可以把它理解成，Generator 函数是一个状态机，封装了多个内部状态。 

执行Generator函数会返回一个遍历器对象,Generator函数除了状态机,还是一个遍历器对象生成器.返回的遍历器对象,可以依次遍历Generator函数内部的每一个状态.

形式上,Generator函数是一个普通函数,但是有两个特征.一是,function关键字与函数名之间有一个星号;二是,函数体内部使用yield表达式,定义不同的内部状态(yield在英语里的意思就是"产出").

```js
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}
var hw = helloWorldGenerator();
```

然后，Generator 函数的调用方法与普通函数一样，也是在函数名后面加上一对圆括号。不同的是，调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是上一章介绍的遍历器对象（Iterator Object）。 

下一步,必须调用遍历器对象的next方法,使得指针移向下一个状态.也就是说,每次调用next方法,内部指针就从函数头部或上一次停下来的地方开始执行,直到遇到下一个yield表达式(或return语句)为止.换言之,Generator函数是分段执行的,yield表达式是暂停执行的标记,而next方法可以恢复执行.

```js
hw.next()
// { value: 'hello', done: false }
hw.next()
// { value: 'world', done: false }
hw.next()
// { value: 'ending', done: true }
hw.next()
// { value: undefined, done: true }
```

总结一下,调用Generator函数,返回一个遍历器对象,代表Generator函数的内部指针.以后,每次调用遍历器对象的next方法,就会返回一个有着value喝done两个属性的对象.value属性表示当前的内部状态的值,是yield表达式后面那个表达式后面那个表达式的值;done属性是一个布尔值,表示是否遍历结束.

## yield表达式

由于Generator函数返回的遍历器兑现,只有调用next方法才会遍历下一个内部状态,所以起始提供了一种可以暂停执行的函数.yield表达式就是暂停标志.

遍历器对象的next方法的运行逻辑如下.

（1）遇到`yield`表达式，就暂停执行后面的操作，并将紧跟在`yield`后面的那个表达式的值，作为返回的对象的`value`属性值。 

（2）下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式。 

（3）如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。 

（4）如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。 

需要注意的是,yield表达式后面的表达式,只有当调用next方法,内部指针指向该语句时才会执行,因此等于为JS提供了手动的"惰性求值"的语法功能.

```js
function* gen() {
  yield  123 + 456;
}
```

yield表达式与return 语句既有相似之处,也有区别.相似之处在于,都能返回紧跟在语句后面的那个表达式的值.区别在于每次遇到yield,函数暂停执行,下一次再从该位置继续向后执行,而return语句不具备位置记忆的功能.一个函数里面,只能执行一次(或者说一个)return 语句,但是可以执行多次(或者说多个)yield表达式.正常函数只能返回一个值,因此只能执行一次return;Generator函数可以返回一系列的值,因为可以有任意多个yield.从另一个角度看,也可以说Generator生成了一系列的值,这也就是它的名称的来历.

Generator 函数可以不用`yield`表达式，这时就变成了一个单纯的暂缓执行函数。 

```js
function* f() {
  console.log('执行了！')
}
var generator = f();
setTimeout(function () {
  generator.next()
}, 2000);
```

另外需要注意，`yield`表达式只能用在 Generator 函数里面，用在其他地方都会报错。 

### 与 Iterator 接口的关系

上一章说过，任意一个对象的`Symbol.iterator`方法，等于该对象的遍历器生成函数，调用该函数会返回该对象的一个遍历器对象。 

由于 Generator 函数就是遍历器生成函数，因此可以把 Generator 赋值给对象的`Symbol.iterator`属性，从而使得该对象具有 Iterator 接口。 

```js
let myIterator = {};
myIterator[Symbol.iterator] = function*(){
    yield 1;
    yield 2;
    yield 3;
};

for(let value of myIterator){
    console.log(value);
}
```

Generator函数执行后,返回一个遍历器对象.该对象本身也具有symbol.iterator属性,执行后返回自身.

```js
function* gen(){
  // some code
}
var g = gen();
g[Symbol.iterator]() === g
// true
```

## 含义

### Generator与状态机

Generator是实现状态机的最佳结构.比如,下面的clock函数就是一个状态机.

```js
var ticking = true;
var clock = function() {
  if (ticking)
    console.log('Tick!');
  else
    console.log('Tock!');
  ticking = !ticking;
}
```

上面代码的`clock`函数一共有两种状态（`Tick`和`Tock`），每运行一次，就改变一次状态。这个函数如果用 Generator 实现，就是下面这样。 

```js
var clock = function* () {
  while (true) {
    console.log('Tick!');
    yield;
    console.log('Tock!');
    yield;
  }
};
```

### Generator与协程

协程（coroutine）是一种程序运行的方式，可以理解成“协作的线程”或“协作的函数”。协程既可以用单线程实现，也可以用多线程实现。前者是一种特殊的子例程，后者是一种特殊的线程。 

(1)**协程与子例程的差异** 

传统的“子例程”（subroutine）采用堆栈式“后进先出”的执行方式，只有当调用的子函数完全执行完毕，才会结束执行父函数。 协程与其不同，多个线程（单线程情况下，即多个函数）可以并行执行，但是只有一个线程（或函数）处于正在运行的状态，其他线程（或函数）都处于暂停态（suspended），线程（或函数）之间可以交换执行权。 也就是说，一个线程（或函数）执行到一半，可以暂停执行，将执行权交给另一个线程（或函数），等到稍后收回执行权的时候，再恢复执行。这种可以并行执行、交换执行权的线程（或函数），就称为协程。 

从现实上看,在内存中,子例程只使用一个栈(stack),而协程是同时存在多个栈,但只有一个栈是在运行状态,也就是说,协程是以多占用内存为代价,实现多任务的并行.

**（2）协程与普通线程的差异** 

不难看出，协程适合用于多任务运行的环境。在这个意义上，它与普通的线程很相似，都有自己的执行上下文、可以分享全局变量。它们的不同之处在于，同一时间可以有多个线程处于运行状态，但是运行的协程只能有一个，其他协程都处于暂停状态。此外，普通的线程是抢先式的，到底哪个线程优先得到资源，必须由运行环境决定，但是协程是合作式的，执行权由协程自己分配。 

由于 JavaScript 是单线程语言，只能保持一个调用栈。引入协程以后，每个任务可以保持自己的调用栈。这样做的最大好处，就是抛出错误的时候，可以找到原始的调用栈。不至于像异步操作的回调函数那样，一旦出错，原始的调用栈早就结束。 

Generator函数是ES6对协程的实现,但属于不完全实现.Generator函数被称为"半协程",意思是只有Generator函数的调用者,才能将执行权还给Generator函数.如果是完全执行的协程,任何函数都可以让暂停的协程继续执行.

如果将 Generator 函数当作协程，完全可以将多个需要互相协作的任务写成 Generator 函数，它们之间使用`yield`表达式交换控制权。 

### Generator 与上下文

JavaScript 代码运行时，会产生一个全局的上下文环境（context，又称运行环境），包含了当前所有的变量和对象。然后，执行函数（或块级代码）的时候，又会在当前上下文环境的上层，产生一个函数运行的上下文，变成当前（active）的上下文，由此形成一个上下文环境的堆栈（context stack）。

这个堆栈是“后进先出”的数据结构，最后产生的上下文环境首先执行完成，退出堆栈，然后再执行完成它下层的上下文，直至所有代码执行完成，堆栈清空。

Generator 函数不是这样，它执行产生的上下文环境，一旦遇到`yield`命令，就会暂时退出堆栈，但是并不消失，里面的所有变量和对象会冻结在当前状态。等到对它执行`next`命令时，这个上下文环境又会重新加入调用栈，冻结的变量和对象恢复执行。

```js
function* gen() {
  yield 1;
  return 2;
}
let g = gen();
console.log(
  g.next().value,
  g.next().value,
);
```

上面代码中，第一次执行`g.next()`时，Generator 函数`gen`的上下文会加入堆栈，即开始运行`gen`内部的代码。等遇到`yield 1`时，`gen`上下文退出堆栈，内部状态冻结。第二次执行`g.next()`时，`gen`上下文重新加入堆栈，变成当前的上下文，重新恢复执行。 

## 应用

Generator 可以暂停函数执行，返回任意表达式的值。这种特点使得 Generator 有多种应用场景。 

### （1）异步操作的同步化表达

Generator函数的暂停执行的效果,意味着可以把异步操作写在yield表达式里面,等到调用next方法时再往下执行.这实际上等同于不需要写回调函数了,因为异步操作的后续操作可以放在yield表达式下面,反正要等到调用next方法时再执行.所以,Generator函数的一个重要实际意义就是用来处理异步操作,改写回调函数.

```js
function* loadUI() {
  showLoadingScreen();
  yield loadUIDataAsynchronously();
  hideLoadingScreen();
}
var loader = loadUI();
// 加载UI
loader.next()
// 卸载UI
loader.next()
```

上面代码中，第一次调用`loadUI`函数时，该函数不会执行，仅返回一个遍历器。下一次对该遍历器调用`next`方法，则会显示`Loading`界面（`showLoadingScreen`），并且异步加载数据（`loadUIDataAsynchronously`）。等到数据加载完成，再一次使用`next`方法，则会隐藏`Loading`界面。可以看到，这种写法的好处是所有`Loading`界面的逻辑，都被封装在一个函数，按部就班非常清晰。 

Ajax是典型的异步操作,通过Generator函数部署Ajax操作,可以用同步的方式表达.

```js
function* main() {
  var result = yield request("http://some.url");
  var resp = JSON.parse(result);
    console.log(resp.value);
}
function request(url) {
  makeAjaxCall(url, function(response){
    it.next(response);
  });
}
var it = main();
it.next();
```

上面代码的`main`函数，就是通过 Ajax 操作获取数据。可以看到，除了多了一个`yield`，它几乎与同步操作的写法完全一样。注意，`makeAjaxCall`函数中的`next`方法，必须加上`response`参数，因为`yield`表达式，本身是没有值的，总是等于`undefined`。 

### （2）控制流管理

如果有一个操作非常耗时,采用回调函数,可能写成下面这样:

```js
step1(function (value1) {
  step2(value1, function(value2) {
    step3(value2, function(value3) {
      step4(value3, function(value4) {
        // Do something with value4
      });
    });
  });
});
```

采用Promise改写成上面的代码:

```js
Promise.resolve(step1)
  .then(step2)
  .then(step3)
  .then(step4)
  .then(function (value4) {
    // Do something with value4
  }, function (error) {
    // Handle any error from step1 through step4
  })
  .done();
```

上面代码已经把回调函数，改成了直线执行的形式，但是加入了大量 Promise 的语法。Generator 函数可以进一步改善代码运行流程。 

```js
function* longRunningTask(value1) {
  try {
    var value2 = yield step1(value1);
    var value3 = yield step2(value2);
    var value4 = yield step3(value3);
    var value5 = yield step4(value4);
    // Do something with value4
  } catch (e) {
    // Handle any error from step1 through step4
  }
}
```

然后使用一个函数,按次序自动执行所有步骤;

```js
scheduler(longRunningTask(initialValue));
function scheduler(task) {
  var taskObj = task.next(task.value);
  // 如果Generator函数未结束，就继续调用
  if (!taskObj.done) {
    task.value = taskObj.value
    scheduler(task);
  }
}
```

下面,利用for ... of 循环回自动一次执行yield命令的特性,提供一种更一般的控制流管理的方法.

```js
let steps = [step1Func, step2Func, step3Func];
function* iterateSteps(steps){
  for (var i=0; i< steps.length; i++){
    var step = steps[i];
    yield step();
  }
}
```

上面代码中，数组`jobs`封装了一个项目的多个任务，Generator 函数`iterateJobs`则是依次为这些任务加上`yield*`命令。 

最后，就可以用`for...of`循环一次性依次执行所有任务的所有步骤。 

```js
for (var step of iterateJobs(jobs)){
  console.log(step.id);
}
```

```
  res = it.next();}
```

### （3）部署 Iterator 接口

利用 Generator 函数，可以在任意对象上部署 Iterator 接口。

```js
function* iterEntries(obj) {
  let keys = Object.keys(obj);
  for (let i=0; i < keys.length; i++) {
    let key = keys[i];
    yield [key, obj[key]];
  }
}
let myObj = { foo: 3, bar: 7 };
for (let [key, value] of iterEntries(myObj)) {
  console.log(key, value);
}
// foo 3
// bar 7
```

上述代码中，`myObj`是一个普通对象，通过`iterEntries`函数，就有了 Iterator 接口。也就是说，可以在任意对象上部署`next`方法。 

下面是一个对数组不是Iterator接口的例子,尽管数组原生具有这个接口:

```js
function* makeSimpleGenerator(array){
  var nextIndex = 0;
  while(nextIndex < array.length){
    yield array[nextIndex++];
  }
}
var gen = makeSimpleGenerator(['yo', 'ya']);
gen.next().value // 'yo'
gen.next().value // 'ya'
gen.next().done  // true
```

### （4）作为数据结构

Generator 可以看作是数据结构，更确切地说，可以看作是一个数组结构，因为 Generator 函数可以返回一系列的值，这意味着它可以对任意表达式，提供类似数组的接口。

```js
function* doStuff() {
  yield fs.readFile.bind(null, 'hello.txt');
  yield fs.readFile.bind(null, 'world.txt');
  yield fs.readFile.bind(null, 'and-such.txt');
}
```

上面代码就是依次返回三个函数，但是由于使用了 Generator 函数，导致可以像处理数组那样，处理这三个返回的函数。 

```js
for (task of doStuff()) {
  // task是一个函数，可以像回调函数那样使用它
}
```

## next方法的参数

yield表达式本身没有返回值,或者说总是返回undefined.next方法可以带一个参数,该参数就会被当作上一个yield表达式的返回值.

```js
function* f() {
  for(var i = 0; true; i++) {
    var reset = yield i;
    if(reset) { i = -1; }
  }
}
var g = f();
g.next() // { value: 0, done: false }
g.next() // { value: 1, done: false }
g.next(true) // { value: 0, done: false }
```

上面代码先定义了一个可以无限运行的 Generator 函数`f`，如果`next`方法没有参数，每次运行到`yield`表达式，变量`reset`的值总是`undefined`。当`next`方法带一个参数`true`时，变量`reset`就被重置为这个参数（即`true`），因此`i`会等于`-1`，下一轮循环就会从`-1`开始递增。 

这个功能有很重要的语法意义。Generator 函数从暂停状态到恢复运行，它的上下文状态（context）是不变的。通过`next`方法的参数，就有办法在 Generator 函数开始运行之后，继续向函数体内部注入值。也就是说，可以在 Generator 函数运行的不同阶段，从外部向内部注入不同的值，从而调整函数行为。 

```js
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
  return (x + y + z);
}
var a = foo(5);
a.next() // Object{value:6, done:false}
a.next() // Object{value:NaN, done:false}
a.next() // Object{value:NaN, done:true}
var b = foo(5);
b.next() // { value:6, done:false }
b.next(12) // { value:8, done:false }
b.next(13) // { value:42, done:true }
```

注意，由于`next`方法的参数表示上一个`yield`表达式的返回值，所以在第一次使用`next`方法时，传递参数是无效的。V8 引擎直接忽略第一次使用`next`方法时的参数，只有从第二次使用`next`方法开始，参数才是有效的。从语义上讲，第一个`next`方法用来启动遍历器对象，所以不用带有参数。 

再看一个通过`next`方法的参数，向 Generator 函数内部输入值的例子。 

```js
function* dataConsumer() {
  console.log('Started');
  console.log(`1. ${yield}`);
  console.log(`2. ${yield}`);
  return 'result';
}
let genObj = dataConsumer();
genObj.next();
// Started
genObj.next('a')
// 1. a
genObj.next('b')
// 2. b
```

## for…of 循环

`for...of`循环可以自动遍历 Generator 函数运行时生成的`Iterator`对象，且此时不再需要调用`next`方法。

```js
function* foo() {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  yield 5;
  return 6;
}
for (let v of foo()) {
  console.log(v);
}
// 1 2 3 4 5
```

上面代码使用`for...of`循环，依次显示 5 个`yield`表达式的值。这里需要注意，一旦`next`方法的返回对象的`done`属性为`true`，`for...of`循环就会中止，且不包含该返回对象，所以上面代码的`return`语句返回的`6`，不包括在`for...of`循环之中。 

下面是一个利用 Generator 函数和`for...of`循环，实现斐波那契数列的例子。 

```js
function* fibonacci() {
  let [prev, curr] = [0, 1];
  for (;;) {
    yield curr;
    [prev, curr] = [curr, prev + curr];
  }
}
for (let n of fibonacci()) {
  if (n > 1000) break;
  console.log(n);
}
```

利用`for...of`循环，可以写出遍历任意对象（object）的方法。原生的 JavaScript 对象没有遍历接口，无法使用`for...of`循环，通过 Generator 函数为它加上这个接口，就可以用了。 

```js
function* objectEntries(obj) {
  let propKeys = Reflect.ownKeys(obj);
  for (let propKey of propKeys) {
    yield [propKey, obj[propKey]];
  }
}
let jane = { first: 'Jane', last: 'Doe' };
for (let [key, value] of objectEntries(jane)) {
  console.log(`${key}: ${value}`);
}
// first: Jane
// last: Doe
```

除了`for...of`循环以外，扩展运算符（`...`）、解构赋值和`Array.from`方法内部调用的，都是遍历器接口。这意味着，它们都可以将 Generator 函数返回的 Iterator 对象，作为参数。 

```js
function* numbers () {
  yield 1
  yield 2
  return 3
  yield 4
}
// 扩展运算符
[...numbers()] // [1, 2]
// Array.from 方法
Array.from(numbers()) // [1, 2]
// 解构赋值
let [x, y] = numbers();
x // 1
y // 2
// for...of 循环
for (let n of numbers()) {
  console.log(n)
}
// 1
// 2
```

