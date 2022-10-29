# TypeScript函数

函数是一组一起执行一个任务的语句。

您可以把代码划分到不同的函数中。如何划分代码到不同的函数中是由您来决定的，但在逻辑上，划分通常是根据每个函数执行一个特定的任务来进行的。

函数声明告诉编译器函数的名称、返回类型和参数。函数定义提供了函数的实际主体。

**函数定义**

函数就是包裹在花括号中的代码块,前面使用了关键字function.

```ts
function function_name()
{
    // 执行代码
}
```

**调用函数**

函数只有通过调用才能执行函数内的代码.

```js
function_name()
```

```js
function test() {   // 函数定义
    console.log("调用函数") 
} 
test()              // 调用函数
```

**函数返回值**

有时，我们会希望函数将执行的结果返回到调用它的地方。

通过使用 return 语句就可以实现。

在使用 return 语句时，函数会停止执行，并返回指定的值。

语法格式如下所示：

```js
function function_name():return_type { 
    // 语句
    return value; 
}
```

- return_type 是返回值的类型。
- return 关键词后跟着要返回的结果。
- 一般情况下，一个函数只有一个 return 语句。
- 返回值的类型需要与函数定义的返回类型(return_type)一致。

```js
// 函数定义
function greet():string { // 返回一个字符串
    return "Hello World" 
} 
 
function caller() { 
    var msg = greet() // 调用 greet() 函数 
    console.log(msg) 
} 
 
// 调用函数
caller()
```

- 实例中定义了函数 *greet()*，返回值的类型为 string。
- *greet()* 函数通过 return 语句返回给调用它的地方，即变量 msg，之后输出该返回值。。

**带参数函数**

在调用函数时，您可以向其传递值，这些值被称为参数。

这些参数可以在函数中使用。

您可以向函数发送多个参数，每个参数使用逗号 , 分隔：

语法格式如下所示：

```js
function func_name( param1 [:datatype], param2 [:datatype]) {   
}
```

- param1、param2 为参数名。

- datatype 为参数类型。

- ```js
  function add(x:number ,y:number):number{
    return x + y;
  }
  console.log(add(1,2));
  ```

**可选参数和默认参数**

可选参数:在TS函数里,如果定义了参数,则我们必须传入这些参数,除非这些参数设置为可选,可选参数使用问好表示?.

```js
function buildName(firstName: string, lastName: string) {
    return firstName + " " + lastName;
}
 
let result1 = buildName("Bob");                  // 错误，缺少参数
let result2 = buildName("Bob", "Adams", "Sr.");  // 错误，参数太多了
let result3 = buildName("Bob", "Adams");         // 正确
```

以下实例,将lastName设置为可选参数:

```ts
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}
 
let result1 = buildName("Bob");  // 正确
let result2 = buildName("Bob", "Adams", "Sr.");  // 错误，参数太多了
let result3 = buildName("Bob", "Adams");  // 正确
```

可选参数必须跟在必需参数后面。 如果上例我们想让 firstName 是可选的，lastName 必选，那么就要调整它们的位置，把 firstName 放在后面。

如果都是可选参数就没关系。

**默认参数**

我们也可以设置参数的默认值,这样在调用函数的时候,如果不传入该参数的值,则使用默认参数,语法:

```js
function function_name(param1[:type],param2[:type] = default_value) { 
}
```

eg:

```js
function calculate_discount(price:number,rate:number = 0.50) { 
    var discount = price * rate; 
    console.log("计算结果: ",discount); 
} 
calculate_discount(1000) 
calculate_discount(1000,0.30)
```

**剩余参数**

有一种情况,我们不知道向函数传入多少个参数,这时候我们就可以使用剩余参数来定义.

剩余参数语法允许我们将一个不确定数量的参数作为一个数组传入.

```js
function buildName(firstName: string, ...restOfName: string[]) {
    return firstName + " " + restOfName.join(" ");
}
  
let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
```

函数的最后一个参数restOfName以......为前缀,它将成为一个由剩余参数组成的数组,索引值从0(包括)到restOfName.lenght(不包括).

```js
function addNumbers(...nums:number[]) {  
    var i;   
    var sum:number = 0; 
    
    for(i = 0;i<nums.length;i++) { 
       sum = sum + nums[i]; 
    } 
    console.log("和为：",sum) 
 } 
 addNumbers(1,2,3) 
 addNumbers(10,10,10,10,10)
```

**匿名函数**  :匿名函数是一个没有函数名的函数.匿名函数在程序运行时动态声明,除了没有函数名外,其他的与标准函数一样.

我们可以将匿名函数赋值给一个变量,这种表达式就成为函数表达式.

语法格式:

```js
var res = function( [arguments] ) { ... }
```

不带参数的匿名函数:

```js
var msg = function() { 
    return "hello world";  
} 
console.log(msg())
```

带参数的匿名函数

```js
var res = function(a:number,b:number) { 
    return a*b;  
}; 
console.log(res(12,2))
```

**匿名函数自调用**

匿名函数自调用在函数后使用()即可:

```js
(function () { 
    var x = "Hello!!";   
    console.log(x)     
 })()
```

**构造函数**

TypeScript也支持使用JavaScript内置的构造函数Function来定义函数:

```js
var res = new Function ([arg1[, arg2[, ...argN]],] functionBody)
```

参数说明：

- **arg1, arg2, ... argN**：参数列表。
- **functionBody**：一个含有包括函数定义的 JavaScript 语句的字符串。

```
var myFunction = new Function("a", "b", "return a * b"); 
var x = myFunction(4, 3); 
console.log(x);
```

**递归函数**  :递归函数即在函数内调用函数本身.

```js
function factorial(number) {
    if (number <= 0) {         // 停止执行
        return 1; 
    } else {     
        return (number * factorial(number - 1));     // 调用自身
    } 
}; 
console.log(factorial(6));      // 输出 720
```

**Lambda函数**

Lambda函数也称之为箭头函数.箭头函数表达式的语法比函数表达式更短.

```js
( [param1, parma2,…param n] )=>statement;
```

eg:

```js
let foo = (x?:number ):number => {
    return x + 10;
}

console.log(foo(1));
```

我们可以不指定函数的参数类型,通过函数内来推断参数类型.

```js
var func = (x)=> { 
    if(typeof x=="number") { 
        console.log(x+" 是一个数字") 
    } else if(typeof x=="string") { 
        console.log(x+" 是一个字符串") 
    }  
} 
func(12) 
func("Tom")
```

**函数重载**

重载是方法名字相同,而参数不同,返回类型可以相同也可以不同.每个重载的方法(或构造函数)都必须有独一无二的参数类型列表.

参数类型不同:

```js
function disp(string):void; 
function disp(number):void;
```

参数数量不同:

```js
function disp(n1:number):void; 
function disp(x:number,y:number):void;
```

参数类型顺序不同:

```js
function disp(n1:number,s1:string):void; 
function disp(s:string,n:number):void;
```

如果参数类型不同,则参数类型应设置为any.

参数数量不同你可以将不同的参数设置为可选.

