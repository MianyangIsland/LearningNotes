# TypeScript联合类型

联合类型可以通过管道(1)将变量设置多种类型,赋值时可以根据设置的类型来赋值.

**Warning**:只能赋值指定的类型,如果赋值其他类型就会报错.

创建联合类型的语法格式如下:

```js
Type1|Type2|Type3 
```

**实例**

声明一个联合类型:

```js
var val:string|number 
val = 12 
console.log("数字为 "+ val) 
val = "Runoob" 
console.log("字符串为 " + val)
```

如果赋值其他类型就会报错.

也可以将联合类型作为函数参数使用:

```js
function disp(name:string|string[])
{
    if(typeof name == 'string')
    {
        console.log(name);
    }
    else
    {
        for(let val of name)
        {
            console.log(val);
        }
    }
}

disp('张珂');
disp(['张珂','黄欲烈','张泽阳','王睿霆','刘堃淼']);
```

**联合类型数组**

我们也可以将数组声明为联合类型:

```js
let arr:string[]|number[];
arr = ['张珂','王睿霆','刘堃淼','张泽阳','黄欲烈'];
for(let val of arr)
{
    console.log(val);
}

arr = [1,2,3,4,5];
for(let val of arr)
{
    console.log(val);
}
```

