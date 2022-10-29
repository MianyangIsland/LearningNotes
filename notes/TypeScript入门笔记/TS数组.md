# TypeScript数组

数组变量是使用单独的变量名来存储一系列的值.

TS声明数组的语法格式:

```js
var array_name[:datatype];        //声明 
array_name = [val1,val2,valn..]   //初始化
```

在声明时初始化:

```js
var array_name[:data type] = [val1,val2…valn]
```

## Array对象

我们也可以用Array对象创建数组.Array对象的构造函数接受以下两种值:

- 表示数组大小的数值。
- 初始化的数组列表，元素使用逗号分隔值。

```js
var arr_names:number[] = new Array(4)  
 
for(var i = 0; i<arr_names.length; i++) { 
        arr_names[i] = i * 2 
        console.log(arr_names[i]) 
}
```

直接初始化数组:

```js
var sites:string[] = new Array("Google","Runoob","Taobao","Facebook") 
 
for(var i = 0;i<sites.length;i++) { 
        console.log(sites[i]) 
}
```

**数组解构**

也可以把数组元素赋值给变量:

```js
var arr:number[] = [12,13] 
var[x,y] = arr // 将数组的两个元素赋值给变量 x 和 y
console.log(x) 
console.log(y)
```

**数组迭代**

我们可以使用for语句来循环输出数组的各个元素:

```js
var j:any; 
var nums:number[] = [1001,1002,1003,1004] 
 
for(j in nums) { 
    console.log(nums[j]) 
}
```

**多维数组**

一个数组的元素可以是另外一个数组,这样就构成了多维数组.最简单的多维数组是二维数组,定义方式如下:

```js
var arr_name:datatype[][]=[ [val1,val2,val3],[v1,v2,v3] ]
```

eg:

```js
let arr:number[][] = [[1,2,3],[4,5,6],[7,8,9]];
for(let value of arr)
{
   for(let val of value)
   {
       console.log(val)
   }
}
```

**数组在函数中的作用**  :作为参数传递给函数

```js
var sites:string[] = new Array("Google","Runoob","Taobao","Facebook") 
 
function disp(arr_sites:string[]) {
        for(var i = 0;i<arr_sites.length;i++) { 
                console.log(arr_sites[i]) 
        }  
}  
disp(sites);
```

# 数组方法

concat():连接两个更多的数组,并返回结果.

```js
let str:string[] = ['张珂','刘堃淼','黄欲烈'];
let str1:string[] = ['王睿霆','张泽阳'];
console.log(str);
console.log(str1);
console.log(str.concat(str1));
```

every():检测数值元素的每个元素都是否符合条件:

```js
function isBigEnough(element, index, array) { 
    console.log(element,index,array);
    return(element>=10)
} 
    
var passed = [12, 15, 18, 130, 44].every(isBigEnough); 
console.log("Test Value : " + passed ); 
```

filter():检测数值元素,并返回符合条件所有元素的数组.

```js
function isBigEnough(element,index,array){
    return (element >= 10);
}
let passed = [12,5,8,130,44].filter(isBigEnough);
console.log(passed);
```

forEach():数组每个元素都执行一次回调函数.

```js
let arr:string[] = ['张珂','黄欲烈','王睿霆','刘堃淼','张泽阳'];
arr.forEach((value)=>{
    console.log(value+'是傻逼!');
})
```

indexOf():搜索数组中的元素,并返回它所在的位置.如果搜不到,返回值-1,代表没有此项.

```js
var index = [12, 5, 8, 130, 44].indexOf(8); 
console.log("index is : " + index );  // 2
```

join():把数组的所有元素放入一个字符串.

```js
let arr = new Array('张珂','刘堃淼','王睿霆','张泽阳','黄欲烈');
let str = arr.join('爱');
console.log(str);
```

lastIndexOf():返回一个指定的字符串值最后出现的位置,在一个字符串中的指定位置从后向前搜索.

```js
var index = [12, 5, 8, 130, 44].lastIndexOf(8); 
console.log("index is : " + index );  // 2
```

map():通过指定函数处理数组的每个元素,并返回处理后的数组.

```js
let arr:string[] = ['张珂','黄玉烈','王睿霆','刘坤秒','张泽阳'];
let arr1 = arr.map((value)=>{
   return value+'傻逼';
})
console.log(arr);
console.log(arr1);
```

pop():删除数组的最后一个元素并返回删除的元素.

```js
var numbers = [1, 4, 9]; 
var element = numbers.pop(); 
console.log("element is : " + element );  // 9
console.log(numbers);
var element = numbers.pop(); 
console.log("element is : " + element );  // 4
console.log(numbers);
```

push():向数组的末尾添加一个或更多元素,并返回新的长度.

```js
var numbers = new Array(1, 4, 9); 
var length = numbers.push(10); 
console.log("new numbers is : " + numbers );  // 1,4,9,10 
length = numbers.push(20); 
console.log("new numbers is : " + numbers );  // 1,4,9,10,20
```

reduce():将数组元素计算为一个值(从左到右).

```js
var total = ['张珂','黄欲烈','王睿霆','张泽阳','刘堃淼'].reduce(function(a,b){ return a+b+'傻逼'; }); 
console.log("total is : " + total );  // 6
```

reduceRight():将数组计算为一个值(从右到左).

reverse():反转数组的元素顺序.

shift():删除并返回数组的第一个元素.

slice():选取数组的一部分,并返回一个新数组.

some():检测数组元素中是否有元素符合指定条件.

sort():对数组的元素进行排序.

```js
let arr:number[] = [1,4,2,13,66,12];
console.log(arr);
let arr1 = arr.sort((a,b)=>{
    return a - b;
});
console.log(arr1);

```

splice():从数组中添加或删除元素.

```ts
let arr:string[] = ['张珂','刘堃淼','黄欲烈','王睿霆'];
let removed = arr.splice(1,0,'张泽阳');
console.log(arr);
console.log(removed);
removed = arr.splice(1,1);
console.log(removed);
console.log(arr);
```

toString():把数组转换为字符串,并返回结果.

```js

```

unshift():向数组的开头添加一个或更多元素,并返回新的长度.

```js
var arr = new Array("orange", "mango", "banana", "sugar"); 
var length = arr.unshift("water"); 
console.log("Returned array is : " + arr );  // water,orange,mango,banana,sugar 
console.log("Length of the array is : " + length ); // 5
```

