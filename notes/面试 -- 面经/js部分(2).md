###  1.编码和字符集的区别

 字符集是书写系统与符合的集合,而字符编码映射为一特定的字节或字节序列,是一种规则.通常特定的字符集采用特定的编码方式(即一种字符集对应一种字符编码（例如：ASCII、IOS-8859-1、GB2312、GBK，都是即表示了字符集又表示了对应的字符编码，但Unicode不是，它采用现代的模型）） .

扩展:

字符：在计算机和电信技术中，一个字符是一个单位的字形、类字形单位或符号的基本信息。即一个字符可以是一个中文汉字、一个英文字母、一个阿拉伯数字、一个标点符号等。

字符集：多个字符的集合。例如GB2312是中国国家标准的简体中文字符集，GB2312收录简化汉字（6763个）及一般符号、序号、数字、拉丁字母、日文假名、希腊字母、俄文字母、汉语拼音符号、汉语注音字母，共 7445 个图形字符。

字符编码：把字符集中的字符编码为（映射）指定集合中的某一对象（例如：比特模式、自然数序列、电脉冲），以便文本在计算机中存储和通过通信网络的传递。

**2.null 和 undefined的区别,如何让一个属性变为null.**

undefined表示一个变量自然的,最原始的状态值,而null则表示一个变量被人为的设置为空对象,而不是原始状态.所以,在实例使用过程中,为了保证变量所代表的语义,不要对一个变量显示的赋值undefined,当需要释放一个对象时,直接赋值为null即可.

**undefiend**的字面量意思就是:未定义的值.这个值的语义是,希望表示一个变量最原始的状态,而非人为操作的结果.这种原始状态会在以下4中场景中出现:

1. 声明了一个变量但没有赋值
2. 访问对象上不存在的属性
3. 函数定义了形参,但没有传递实参
4. 使用void对表达式求值

因此,undefined一般都来自于某个表达式最原始的状态值,不是人为操作的结果.当然,你也可以手动给一个变量赋值undefined,但这样做没有意义,因为一个变量不赋值就是undefined.

null的字面意思是:空值.这个值的语义是,希望表示一个对象被人为的重置为空对象,而非一个变量最原始的状态.在内存里的表示就是,栈中的变量没有指向堆中的内存对象.

null有属于自己的类型Null,而不属于Object类型,typeof之所以会判定为Object类型,是因为JS数据类型在底层都是以二进制的形式表示的,**二进制的前三位0会被typeof判断为对象类型**,而null的二进制位恰好都是0,因此,null被误判为Object类型.

**3.数组和伪数组的区别**

1. 定义:

   数组是一个特殊对象,与常规对象的区别:

    当有新元素添加到列表中时,自动更新为length属性

     设置length属性,可以截断数组

     从Array.prototype中继承了方法

    属性为"Array".

    类数组是一个拥有length属性,并且他属性为非负整数的普通对象,类数组不能直接调用数组方法.

   2.区别:

     本质: 类数组是简单对象,它的原型关系与数组不同.

   ```js
   // 原型关系和原始值转换
   let arrayLike = {
       length: 10,
   };
   console.log(arrayLike instanceof Array); // false
   console.log(arrayLike.__proto__.constructor === Array); // false
   console.log(arrayLike.toString()); // [object Object]
   console.log(arrayLike.valueOf()); // {length: 10}
   
   let array = [];
   console.log(array instanceof Array); // true
   console.log(array.__proto__.constructor === Array); // true
   console.log(array.toString()); // ''
   console.log(array.valueOf()); // []
   ```

   3.类数组转换为数组:

       1. 转换方法:

   ​         使用Array.from()

   ​         使用Array.prototype.slice.call()

   ​         使用Array.prototype.forEach()

      转换须知:

   ​          转换后的数组长度由length属性决定.索引不连续时转换结果是连续的,会自动补位.

   eg:

   ```js
   let al1 = {
       length: 4,
       0: 0,
       1: 1,
       3: 3,
       4: 4,
       5: 5,
   };
   console.log(Array.from(al1)) // [0, 1, undefined, 3]
   ```

   仅考虑0或正整数的索引

      ```js
   // 代码示例
   let al2 = {
       length: 4,
       '-1': -1,
       '0': 0,
       a: 'a',
       1: 1
   };
   console.log(Array.from(al2)); // [0, 1, undefined, undefined]
      ```

   使用slice转换产生稀疏数组

   ```js
   // 代码示例
   let al2 = {
       length: 4,
       '-1': -1,
       '0': 0,
       a: 'a',
       1: 1
   };
   console.log(Array.prototype.slice.call(al2)); //[0, 1, empty × 2]
   ```

   2.使用数组方法操作类数组注意的地方

   ```js
    let arrayLike2 = {
       2: 3,
       3: 4,
       length: 2,
       push: Array.prototype.push
     }
   
     // push 操作的是索引值为 length 的位置
     arrayLike2.push(1);
     console.log(arrayLike2); // {2: 1, 3: 4, length: 3, push: ƒ}
     arrayLike2.push(2);
     console.log(arrayLike2); // {2: 1, 3: 2, length: 4, push: ƒ}
   ```

**4.手写一个发布订阅**

**5.手写数组转树**

**6.Set,Map,WeakSet和WeakMap的区别**

Set:

1. 成员不能重复
2. 只有键值,没有键名,有点类似数组
3. 可以遍历,方法由add,delete,has

WeakSet:

1. 成员都是对象(引用)
2. 成员都是弱引用,随时可以消失(不计入垃圾回收机制).可以用来保存DOM节点,不容易造成内存泄露;
3. 不能遍历,方法有add,delete,has.

Map:

1. 本质上是键值对的结合,类似集合;
2. 可以遍历,方法很多,可以跟各种数据格式转换

WeakMap:

1. 只接收对象为键名(null除外),不接受其他类型的值作为键名.
2. 键名指向的对象,不计入垃圾回收机制
3. 不能遍历,方法同get,set,has,delete

**7.JS中内存泄露的情况**

1. 意外的全局变量
2. 闭包
3. 未被清空的定时器
4. 未被销毁的事件监听
5. DOM引用

json和xml数据的区别:

1. 数据体积方面: xml 是重量级的,json是轻量级的,传递的速度更快些.
2. 数据传输方面: xml在传输过程中比较占宽带,json占宽带,易于压缩.
3. 数据交互方面: json在javascript的交互更加方便,更容易解析处理,更好的进行数据交互.
4. 数据描述方面: json对数据的描述性比xml较差.
5. xml 和 json都用在项目交互下,xml多用于做配置文件,json用于数据交互.

**8.javaScript有几种方法判断变量的类型?**

1. 使用typeof检测当需要判断变量是否是number,string,boolean,funtion,undefined等类型时,可以使用typeof进行判断.
2. 使用instanceof检测 instanceof运算符与typeof运算符相似,用于识别正在处理的对象类型.与typeof方法不同的是,instanceof方法要求开发者明确确认对象为某特定类型.
3. 使用constructor检测, constructor本来是原型对象上的属性,指向构造函数.但是根据实例对象寻找属性的顺序,若实例对象上没有实例属性或方法时,就去原型链上寻找,因此,实例对象也是能使用constructor属性的.

**9.代码解释题**

```js
var min = Math.min();
max = Math.max();
console.log(min < max);
// 写出执行结果，并解释原因
false
```

Math.min 的参数是0个或者多个,如果多个参数很容易理解,返回参数中最小的.如果没有参数,则返回Infinity,无穷大

Math.max 没有传递参数时返回的是-Infinity,所以输出false.

**10.代码解析题**

```js
var company = {
    address: 'beijing'
}
var yideng = Object.create(company);
delete yideng.address
console.log(yideng.address);
// 写出执行结果，并解释原因
beijing
```

这里的yideng通过prototype继承了company的address.yideng自己并没有address属性.所以删除操作符的作用是无效的.

扩展:

1. delete使用原则:delete操作符用来删除一个对象的属性.

2. delete在删除一个不可配置的属性时在严格模式下的区别:

    (1) 在严格模式中,如果属性是一个不可配置属性,删除时会抛出异常.

    (2) 非严格模式下false.

3. delete能删除隐式声明的全局变量:这个全局变量其实是blobal对象(window)的属性.

4. delete能删除的:

     (1) 可配置对象的属性 (2) 隐式声明的全局变量 (3)用户定义的属性 (4) 显示声明的全局变量 (5) 内置对象的内置属性 (6) 一个对象从原型链继承而来的属性.

5. delete删除数组元素:

    (1)当你删除一个数组元素时,数组的length属性并不会变小,数组元素变成undefined

   (2) 如果你想让一个数组元素的值变为undefined而不是删除它,可以使用undefined给其赋值而不是使用delete操作符.此时数组元素是在数组中的.

6. delete操作符与直接释放内存(只能通过解引用来间接释放),没有关系.

