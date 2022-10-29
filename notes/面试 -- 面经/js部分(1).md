# js部分

1. let const var 相关

var -es5 声明变量的方式

   在变量未赋值时,变量undefined(为使用声明变量时也为undefined)

   作用域 ----- var 的作用域为方法作用域;只要在方法内定义了,整个方法内的定义变量后的代码都可以使用

let --- es6声明方式

   在变量为声明前声明前直接使用会报错

   作用域  ---  let为块作用域 -- 通常let比var范围要小

​    let禁止重复声明变量,否则会报错,var可以重复声明变量

const --- ES6变量声明方式

  const为常量声明方式;声明变量时必须初始化,在后面出现的代码中不能再修改该常量的值.

  const 实际上保证的,并不是变量的值不得改动,而是变量指向的内存地址不得改动.

### 2.js数据类型,区别

基本数据类型:

 Number , String , Boolean, null, undefiend,symbol,bigint(后两个为ES6新增).

引用数据类型:

 object , function 

object: 普通对象,数组对象,正则对象,日期对象,Math数学函数对象.

两种数据存储方式:

 基本数据类型时直接存储再栈中的简单数据段,占据空间小,大小固定,属于被频繁使用的数据.栈是存储基本类型值和执行代码的空间.

引用数据类型是存储在堆内存中,占据空间大,大小不固定.引用数据类型在栈中存储了指针,该指针指向堆中该实体的起始地址,当解释器寻找引用值时,会检索其在栈中的地址,取得地址后从堆中获得实体.

两种数据类型的区别:

 堆比栈空间大,栈比堆运行速度块.

  堆内存是无序存储,可以根据引用值直接获取.

 基础数据类型比较稳定,而且相对来说占用的内存小.

   引用数据类型大小是动态的,而且是无限的.

3.Object.assign 的理解

作用: Object.assign可以实现对象的合并.

 语法: Object.assign(target,...sources)

解析: Object.assign 会将source里面可枚举属性赋值到target,如果和target的已有属性重名,则会覆盖.

后续的source会覆盖前面的source的同名属性

Object.assign 复制的是属性值,如果属性值是一个引用类型,那么复制的其实是引用地址,就会存在引用共享的问题.

### 4.constructor的理解

创建的每个函数都有一个prototype(原型)对象,这个属性是一个指针,指向一个对象.在默认情况下,所有原型对象都会自动获得一个constructor(构造函数)属性,这个属性是一个指向prototype属性所在函数的指针.当调用构造函数创建一个新的实例后,该实例的内部将包含一个指针(继承自构造函数的prototype),指向构造函数的原型对象.注意将构造函数的protottype设置为等于一个以对象字面量形式创建的新对象时,constructor属性不再指向该构造函数.

5.map 与 forEach的区别

相同点:

 都是循环遍历数组的每一项,每次执行匿名函数都支持三个参数,参数为分别为item(当前每一项),index(索引值),arr(原数组), 匿名函数中的this都是指向window, 只能遍历数组.

不同点:

1. map()会分配内存空间存储新数组并返回.forEach不会返回数据.
2. forEach()允许callback更改原始数组的元素.map()返回新的数组.

### 6.for of可以遍历那些对象

for ... of 它是es6新增的一个遍历方法,但只限于迭代器(iterator),所以普通的对象用for ... of 遍历是会报错的.

可迭代的对象: Array,Map,Set,String,TypedArray,arguments对象等.

### 7.js静态类型检查:

js是动态类型语言.

静态类型语言: 类型检查发生在编译阶段,因此除非修复错误,否则会一致编译失败.

动态类型语言: 只有在程序运行了依次的时候错误才会被发现,也就是在运行时,因此即使代码中包含了会在运行时阻止脚本正常运行的错误类型,这段代码也可以通过编译.

js静态类型检查的方法:

Flow 时 Facebook开发和发布的一个开源静态类型检查库,它允许你逐渐地像JS代码中添加类型.

TypeScript是一个会编译为JS地超集(尽管它看起来几乎像一种新地静态类型语言).

使用静态类型地优势:

 可以尽早发现错误和bug.  减少了复杂地错误处理  将数据和行为分离  减少单元测试地数量   提供了领域建模工具  帮助我们消除了一整类bug  重构时更有信心.

使用静态类型地劣势

 代码冗长   需要话时间去掌握类型

### 8.indexof

语法: str.indexOf(searchValue,[,fromIndex]).

参数: searchValue : 要被查找地字符串值.

 如果没有提供确切地字符串,[Searchvalue 会被强制设置为 "undefined"],然后在当前字符串中查找这个值.

举个例子：'undefined'.indexOf()将会返回0，因为undefined在位置0处被找到，但是'undefine'.indexOf()将会返回 -1 ，因为字符串'undefined'未被找到 .

fromIndex : 可选

数字表示开始查找地位置.可以是任意整数,默认值为0.

如果fromIndex的值小于0,或者大于str,length,那么查找分别从0和str.length开始.

（译者 注：fromIndex的值小于0，等同于为空情况；fromIndex的值大于或等于str.length，那么结果 会直接返回-1。） 

返回值: 查找字符串的searchValue的第一次出现的索引,如果没有找到,则返回-1.

若查找的字符串searchValue是一个空字符串,则返回fromIndex.如果fromIndex值为空,或者fromIndex值小于被查找的字符串的长度,返回值和以下的fromIndex值一样.

如果fromIndex值大于等于字符串的长度,将会直接返回字符串的长度(str,length)

特点: 

  严格区分大小写

  在使用indexOf检索数组时,用 `===`去匹配,意味着检查数据类型.

### 9.iframe有什么优点,缺点

优点:

 iframe能够原封不动的把嵌入的网页展现出来. 如果有多个网页引用iframe,那么你只需要修改iframe的内容,就可以实现调用的每一个页面内容的更改,方便快捷.

网页如果伪类统一风格,头部和版本都是一样的,就可以写成一个页面,用iframe来嵌套,可以增加代码的可重用. 如果遇到加载缓慢的第三方内容如图标和广告,这些问题可以有iframe来解决.

缺点:

 iframe 会阻塞主页面的onload事件;iframe和主页面共享连接池,而浏览器对相同域的连接有限制,所以会影响页面的并行加载.会产生很多页面,不容易管理.

iframe架构结构有时会让人感到迷惑,如果框架个数多的话,可能会出现上下,左右滚动条,会分散访问者的注意力,用户体验度差.

代码复杂,无法被一些搜索引擎索引到,这一点很关键,现在的搜索引擎爬虫还不能很好的处理iframe中的内容,所以使用iframe会不利于搜索引擎优化(SEO).

很多移动设备无法完全显示框架,设备兼容性差. iframe架构页面会增加服务器http请求,对于大型网站是不可取的.

### 10.变量提升

JS是单线程语言,所以执行肯定是顺序执行.但是并不是逐行的分析和执行,而是一段一段的分析执行,会先进行编译阶段然后才是执行阶段.在编译阶段,代码真正执行前的几毫秒,会检测到所有的变量和函数声明,所有这些函数和变量声明都被添加到名为Lexical Environment的JavaScript数据结构的内存中.所以这些变量和函数能在他们真正被声明之前使用.

### 11.作用域

 作用域就是一个独立地盘,让变量不会外泄,暴露出去.也就是说作用域最大的用处就是隔离变量,不同作用域下同名变量不会有冲突.

ES6之前JavaScript没有块级作用域,只有全局作用域和函数作用域.ES6的到来,为我们提供了"块级作用域",可通过新增命令let和const来体现.

### 13.JavaScript中arguments相关的问题

arguments: 在js中,我们在调用有参的函数时,当往这个调用的有参函数传参时,js会把所传的参数全部存到一个arguments的对象里面.它是一个类数组数据.

由来: JavaScript每个函数都会有一个Argumrnts对象实例arguments,引用着函数的参数.它是寄生在js函数当中,不能显示创建,arguments对象只有函数开始时才可用.

作用: 有了arguments这个对象之后,我们可以不用给函数预先设定形参,可以动态地通过arguments为函数加入参数.

### 14.instanceOf原理 , 手动实现 function isInstanceOf(child,Parent)

instanceof主要作用就是判断一个实例是否属于某种类型:

```js
let Person = function(){

}
let no  =  new Person();
console.log(no instanceof Person);//true
```

instanceOf原理

```js
function new_instance_of(leftValue,rightValue){
    let rightProto = rightValue.prototype;//取右表达式地prototype值
    leftValue = leftValue._proto_;//取左表达式_proto_值
    while(true){
        if(leftValue === null){
            return false;
        }
        if(leftValue === rightProto){
            return true;
        }
        leftValue = leftValue._proto_;
    }
}
```

其实instanceof主要的实现原理就是只要右边变量的prototype在左边变量的原型链上即可.因此,instanceof在查找过程中会遍历左边变量的原型链,直到找到右边变量的prototype,如果查找失败,返回false,告诉我们左边变量并非是右边变量的实例.

```js
function instance_of(L, R) {//L 表示左表达式，R 表示右表达式
    var O = R.prototype;
    L = L.__proto__;
    while (true) { 
        if (L === null) 
        return false; 
        if (O === L) // 这里重点：当 O 严格等于 L 时，返回true 
        return true; 
        L = L.__proto__; 
    } 
}
// 开始测试
var a = []
var b = {}

function Foo(){}
var c = new Foo()
function child(){}
function father(){}
child.prototype = new father() 
var d = new child()

console.log(instance_of(a, Array)) // true
console.log(instance_of(b, Object)) // true
console.log(instance_of(b, Array)) // false
console.log(instance_of(a, Object)) // true
console.log(instance_of(c, Foo)) // true
console.log(instance_of(d, child)) // true
console.log(instance_of(d, father)) // true
```



