## JS数组常用方法

1.join()(数组转字符串)

数组转字符串,方法只接收一个参数: 即默认为逗号分割符号().

```js
<script>
	var arr=[1,2,3,4];
	console.log(arr.join()); //1,2,3,4
	console.log(arr.join(":")); //1:2:3:4
	console.log(arr); //[1,2,3,4],原数组不变
</script>
```

join()实现重复字符串

```js
<script>
    function repeatStr(str, n) {
        return new Array(n + 1).join(str);
    }
	console.log(repeatStr("嗨",3)); //嗨嗨嗨
	console.log(repeatStr("Hi",3)); //HiHiHi
	console.log(repeatStr(1,3));    //111
</script>
```

2.push()和pop()(数组尾操作)

push():方法可向数组的末尾添加一个或多个元素,并返回新的长度.

pop():方法用于删除并返回数组的最后一个元素.

```js
<script>
	var arr=[1,2,3,4];
	//push
	var push_arr=arr.push("Tom","Sun");
	console.log(arr); //[1,2,3,4,"Tom","Sun"];
	console.log(push_arr); // 6
	//pop
	var pop_arr=arr.pop();
	console.log(arr); //[1,2,3,4,"Tom"];
	console.log(pop_arr); // Sun	
</script>
```

3.shift()和unshift()(数组首操作)

shift():方法用于把数组的第一个元素从其中删除,并返回第一个元素的值.

unshift():方法可向数组的开头添加一个或更多元素,并返回新的长度.

4.sort()(排序)

方法用于对数组的元素进行排序.

```js
<script>
	var arr=[1,100,5,20];
	console.log(arr.sort()); // [1, 100, 20, 5]
	console.log(arr); // [1, 100, 20, 5] (原数组改变)	
</script>
```

注意,上面的代码没有按照数组的大小对数字进行排序,是按照字符编码的顺序进行排序,要实现这一点,就必须使用一个排序函数:

```js
//升序
<script>
	var arr=[1,100,5,20];
	function sortNumber(a,b){return a - b};
	console.log(arr.sort(sortNumber)); //[1, 5, 20, 100]
	console.log(arr); //[1, 5, 20, 100] (原数组改变)
</script>

//降序
<script>
	var arr=[1,100,5,20];
	function sortNumber(a,b){return b - a};
	console.log(arr.sort(sortNumber)); // [100, 20, 5, 1]
	console.log(arr); // [100, 20, 5, 1] (原数组改变)
</script>
```

5.reverse() (反转数组)

方法用于颠倒数组中元素的顺序.

```js
<script>
	var arr=[12,25,5,20];
	console.log(arr.reverse()); // [20, 5, 25, 12]
	console.log(arr); // [20, 5, 25, 12] (原数组改变)
</script>
```

6.concat() (连接两个或多个数组).

concat()方法用于两个或多个数组.该方法不会改变现有的数组,而仅仅会返回被连接数组的一个副本.再没有给concat()方法传递该参数的情况下,它只是赋值当前数组并返回副本.

```js
<script>
	var arr=[1,2,3,4];
	var arr2=[11,12,13] 
	var arrCopy = arr.concat(arr2);
	console.log(arr.concat()); // [1, 2, 3, 4] (复制数组)
	console.log(arrCopy); // [1, 2, 3, 4, 11, 12, 13]
	console.log(arr); // [1, 2, 3, 4] (原数组未改变)
</script>
```

如果传入的参数是一个二维数组呢?

```js
<script>
	var arr=[1,2,3,4];
	var arr2=[11,[12,13]] 
	var arrCopy = arr.concat(arr2);	
	console.log(arrCopy); // [1, 2, 3, 4, 11, Array(2)]
	console.log(arr); // [1, 2, 3, 4] (原数组未改变)
</script>
```

从上面代码中可以看出concat方法只能将传入数组中的每一项添加到数组中,如果传入数组中有些项是数组,那么也会把这一数组项当作一项添加到arrCopy中.

7.silce() (数组截取)

arr.silce(start,end)

start:可选.规定从何处开始选取.如果是负数,那么它规定从数组尾部开始算起的位置.也就是说,-1指最后一个元素,-2指倒数第二个元素,以此类推.

end:可选.规定从何处结束选取.该参数是数组片段结束处的数组下标.如果没有指定该参数,那么切分的数组包含从start到数组结束的所有元素.如果这个参数是负数,那么它规定的是从数组尾部开始算起的元素.

返回值: 返回一个新的数组,包含从start到end(不包括该元素)的arr中的元素.

```js
<script>
	var arr = [1,4,6,8,12];
	var arrCopy1 = arr.slice(1);	
	var arrCopy2 = arr.slice(0,4);	
	var arrCopy3 = arr.slice(1,-2);
	var arrCopy4 = arr.slice(-5,4);
	var arrCopy5 = arr.slice(-4,-1)
	console.log(arrCopy1);  // [4, 6, 8, 12]
	console.log(arrCopy2);  // [1, 4, 6, 8] 
	console.log(arrCopy3);  // [4, 6] 
	console.log(arrCopy4);  // [1, 4, 6, 8]
	console.log(arrCopy5);  // [4, 6, 8]
	console.log(arr);  // [1, 4, 6, 8, 12] (原数组未改变) 
</script>
```

8.splice()(数组更新)

splice()方法向/从数组中添加/删除项目,然后返回被删除的项目.(**该方法会改变原始数组**).

arr.splice(index,howmany,item1,....,itemX);

**index**：必需。整数，规定添加/删除项目的位置，使用负数可从数组结尾处规定位置。

**howmany**：必需。要删除的项目数量。如果设置为 0，则不会删除项目。

**item1, ..., itemX**：可选。向数组添加的新项目。

返回值: 含有被删除的元素的数组,若没有删除则返回一个空数组.

```js
<script>
	var arr = ["张三","李四","王五","小明","小红"];
	/**************删除"王五"****************/
	var arrReplace1 = arr.splice(2,1);	
	console.log(arrReplace1);  // ["王五"] 
	console.log(arr);  // ["张三", "李四", "小明", "小红"] (原数组改变)
	//删除多个
	var arrReplace2 = arr.splice(1,2);	
	console.log(arrReplace2);  //  ["李四", "小明"] 
	console.log(arr);  // ["张三", "小红"]
	/**************添加"小刚"****************/
	var arrReplace3 = arr.splice(1,0,"小刚");
	console.log(arrReplace3);  // [] (没有删除元素，所以返回的是空数组)
	console.log(arr);  // ["张三", "小刚", "小红"]
	//添加多个
	var arrReplace4 = arr.splice(3,0,"刘一","陈二","赵六");
	console.log(arrReplace4);  // []
	console.log(arr);  // ["张三", "小刚", "小红", "刘一", "陈二", "赵六"]
	/**************"王五"替换"小刚"****************/
	var arrReplace5 = arr.splice(1,1,"王五");
	console.log(arrReplace5);  // ["小刚"]
	console.log(arr);  // ["张三", "王五", "小红", "刘一", "陈二", "赵六"]
	//替换多个
	var arrReplace6 = arr.splice(1,4,"李四");
	console.log(arrReplace6);  // ["王五", "小红", "刘一", "陈二"]
	console.log(arr);  // ["张三", "李四", "赵六"]
</script>
```

## ES5数组新增方法:

**2个索引方法:indexOf()和lastIndexOf()**

两个方法都返回要查找的项在数组中首次出现的位置,在没有找到的情况下返回-1.

indexOf() -----  array.indexOf(item,start) (从数组的开头(位置0)开始向后查找).

item:必选.查找的元素.

start：可选的整数参数。规定在数组中开始检索的位置。如省略该参数，则将从array[0]开始检索。

lastIndexOf()--------array.lastIndexOf(item,start) （从数组的末尾开始向前查找）

item： 必须。查找的元素。

start：可选的整数参数。规定在数组中开始检索的位置。如省略该参数，则将从 array[array.length-1]开始检索。

```js
<script>
	var arr = [1,4,7,10,7,18,7,26];
	console.log(arr.indexOf(7));        // 2
	console.log(arr.lastIndexOf(7));    // 6
	console.log(arr.indexOf(7,4));      // 4
	console.log(arr.lastIndexOf(7,2));  // 2
	console.log(arr.indexOf(5));        // -1		
</script>
```

**5个迭代方法 forEach(),map(),some(),every(),filter()**

**这几个方法语法都一样,都不会改变原数组.**

forEach():对数组进行遍历循环,这个方法没有返回值.

语法：array.forEach(function(currentValue , index , arr){//do something}, thisValue)

currentValue : 必需。当前元素

index： 可选。当前元素的索引值。

arr :  可选。当前元素所属的数组对象。

thisValue： 可选。传递给函数的值一般用 "this" 值。如果这个参数为空， "undefined" 会传递给 "this" 值

```js
<script>
	var Arr = [1,4,7,10];
	Arr.forEach(function(currentValue, index, arr){
		console.log(index+"--"+currentValue+"--"+(arr === Arr));		
	})
	// 输出：
	// 0--1--true
	// 1--4--true
	// 2--7--true
	// 3--10--true	
</script>
```

map():指"映射",方法返回一个新数组,数组中的元素为原始数组元素调用函数处理后的值.

**语法：array.map(function(currentValue , index , arr){//do something}, thisValue)**   

map方法实现数组中每个数求平方.

```js
<script>
    var arr = [1,4,8,10];
    var arr2 = arr.map(function(currentValue){
        return currentValue*currentValue;
    });
    console.log(arr2);  // [1, 16, 64, 100]
</script>
```

**filter()： “过滤”功能，方法创建一个新数组, 其包含通过所提供函数实现的测试的所有元素。** 

**语法： array.filter(function(currentValue , index , arr){//do something}, thisValue)**  

filter方法实现筛选排除掉所有小于5的元素： 

```js
<script>
    var arr = [1,4,6,8,10];
    var result1 = arr.filter(function(currentValue){
        return currentValue>5;
    });
    console.log(result1);  // [6, 8, 10]
    var result2 = arr.filter(function(currentValue){
        return currentValue>"5";
    });
    console.log(result2);  // [6, 8, 10]
</script>
```

当我们分别设置item > 5和item > "5"时, 返回的结果是一样的，由此我们可以看出**函数****支持弱等于（==），不是必须全（===）。** 

**every()：判断数组中每一项都是否满足条件，只有所有项都满足条件，才会返回true。**

**语法： array.every(function(currentValue , index , arr){//do something}, thisValue)** 

```js
<script>
	var arr = [1,4,6,8,10];
	var result1 = arr.every(function(currentValue){
	    return currentValue< 12;
	});
	console.log(result1);  // true
	var result2 = arr.every(function(currentValue){
	    return currentValue> 1;
	});
	console.log(result2);  // false
</script>
```

**some()：判断数组中是否存在满足条件的项，只要有一项满足条件，就会返回true。**

**语法： array.some(function(currentValue , index , arr){//do something}, thisValue)**

```js
<script>
	var arr = [1,4,6,8,10];
	var result1 = arr.some(function(currentValue){
	    return currentValue> 10;
	});
	console.log(result1);  // false
	var result2 = arr.some(function(currentValue){
	    return currentValue> 5;
	});
	console.log(result2);  // true
</script>
```

**2个归并方法: reduce(),reduceRight()**

这两个方法都会迭代数组中的所有项，然后生成一个最终返回值。他们都接收两个参数，第一个参数是每一项调用的函数，函数接受四个参数分别是初始值，当前值，索引值，和当前数组，函数需要返回一个值，这个值会在下一次迭代中作为初始值。第二个参数是迭代初始值，参数可选，如果缺省，初始值为数组第一项，从数组第一个项开始叠加，缺省参数要比正常传值少一次运算。

reduce()方法从数组的第一项开始，逐个遍历到最后。而 reduceRight()则从数组的最后一项开始，向前遍历到第一项。

reduce()语法：arr.reduce(function(total , cur , index , arr){//do something}, initialValue)

reduceRight()语法：arr.reduceRight(function(total , cur , index , arr){//do something}, initialValue)

total ：必需。初始值, 或者计算结束后的返回值。

cur ：必需。当前元素。

index ：可选。当前元素的索引。

arr：可选。当前元素所属的数组对象。

initialValue：可选。传递给函数的初始值。
下面代码实现数组求和:

```js
<script>
	var arr = [1,2,3,4,5];
	var result1 = arr.reduce(function(total,cur,index,arr){	
		console.log("total:"+total+",cur:"+cur+",index:"+index);
	    return total+cur;
	});
	console.log("结果："+result1);
	// 输出
	// total:1,cur:2,index:1
	// total:3,cur:3,index:2
	// total:6,cur:4,index:3
	// total:10,cur:5,index:4
	// 结果：15
	var result2 = arr.reduce(function(total,cur,index,arr){	
		console.log("total:"+total+",cur:"+cur+",index:"+index);
	    return total+cur;
	},10);
	console.log("结果："+result2);
	// 输出
	// total:10,cur:1,index:0
	// total:11,cur:2,index:1
	// total:13,cur:3,index:2
	// total:16,cur:4,index:3
	// total:20,cur:5,index:4
	// 结果：25
</script>
```

从上面代码我们可以看出，当我们不给函数传递迭代初始值时初始值 **total** 为数组第一项，函数从数组第二项开始迭代；若我们给函数传递迭代初始值，则函数从数组第一项开始迭代 .

## ES6数组新增方法

1.Array.from():方法是用于类似数组的对象(即有length属性的对象)和可遍历对象转为真正的数组.

```js
<script>
	let json ={
	    '0':'卢',
	    '1':'本',
	    '2':'伟',
	    length:3
	}
	let arr = Array.from(json);
	console.log(arr); // ["卢", "本", "伟"]	
</script>
```

2.Array.of()

方法是将一组值转变为数组,参数不分类型,只分数量,数量为0返回空数组.

```js
<script>
	let arr1 = Array.of(1,2,3);	
	let arr2 = Array.of([1,2,3]);
	let arr3 = Array.of(undefined);
	let arr4 = Array.of();
	console.log(arr1); // [1, 2, 3]
	console.log(arr2); // [[1, 2, 3]]
	console.log(arr3); // [undefined]
	console.log(arr4); // []
</script>
```

3.find()

方法返回通过测试（函数内判断）的数组的第一个元素的值。方法为数组中的每个元素都调用一次函数执行。当数组中的元素在测试条件时返回 true 时, find() 返回符合条件的元素，之后的值不会再调用执行函数。如果没有符合条件的元素返回 undefined。

回调函数可以接收3个参数，依次为当前的值（currentValue）、当前的位置（index）、原数组（arr）

注意：find() 对于空数组，函数是不会执行的。find() 并没有改变数组的原始值。

```js
<script>
	let Arr = [1,2,5,7,5,9];
	let result1 = Arr.find(function(currentValue,index,arr){			
		return currentValue>5;
	});
	let result2 = Arr.find(function(currentValue,index,arr){			
		return currentValue>9;
	});
	console.log(result1); // 7
	console.log(result2); // undefined
</script>
```

find()实现根据id去粗数组中的对象.

```js
<script>
	let Arr = [
		{
			id:1,
			name:"张三"
		},
		{
			id:2,
			name:"李四"
		}		
	];
	let obj = Arr.find(function(currentValue,index,arr){			
		return currentValue.id===1;
	});
	console.log(obj.name); // 张三
</script>
```

4.findIndex()

findIndex和find差不多，不过默认返回的是索引，如果没有符合条件的元素返回 -1 

```js
<script>
	let Arr = [1,2,5,7,5,9];
	let result1 = Arr.findIndex(function(currentValue,index,arr){			
		return currentValue>5;
	});
	let result2 = Arr.findIndex(function(currentValue,index,arr){			
		return currentValue>9;
	});
	console.log(result1); // 3
	console.log(result2); // -1
</script>
```

5.fill()

fill()方法用一个固定值填充一个数组中从起始索引到终止索引内的全部元素.**不包括终止索引**.

语法：array.fill(value,  start,  end)

value：必需。填充的值。

start：可选。开始填充位置。如果这个参数是负数，那么它规定的是从数组尾部开始算起。

end：可选。停止填充位置 (默认为 array.length)。如果这个参数是负数，那么它规定的是从数组尾部开始算起。

```js
<script>
    let arr = [1,2,3,4,5,6];
    arr.fill(0);  // [0, 0, 0, 0, 0, 0]
    arr.fill(0,1);  // [1, 0, 0, 0, 0, 0] 
    arr.fill(0,1,2);  // [1, 0, 3, 4, 5, 6]
    arr.fill(0,-1);  // [1, 2, 3, 4, 5, 0]
    arr.fill(0,1,-1);  // [1, 0, 0, 0, 0, 6]
</script>
```

6.遍历数组方法keys(),values(),entries()

这三个方法都是返回一个遍历器对象,可用for...of 循环遍历 , 唯一区别: keys()是对键名的遍历,values()对键值的遍历,entries()是对键值对的遍历.

7.includes()

方法用来判断一个数组是否包含一个指定的值，如果是返回 true，否则false。 

语法：arr.includes(searchElement ,  fromIndex)

searchElement ： 必须。需要查找的元素值。

fromIndex：可选。从该索引处开始查找 searchElement。如果为负值，则按升序从 array.length + fromIndex 的索引开始搜索。默认为 0。

```js
<script>
	let arr = ["a","b","c","d"];
	let result1 = arr.includes("b");
	let result2 = arr.includes("b",2);
	let result3 = arr.includes("b",-1);
	let result4 = arr.includes("b",-3);
	console.log(result1);  // true
	console.log(result2);  // false
	console.log(result3);  // flase
	console.log(result4);  // true
</script>
```

8.copyWithin()

方法用于从数组的指定位置拷贝元素到数组的另一个指定位置中,**会覆盖原有成员**.

语法: array.copyWithin(target,start,end).

target:必需,从该位置开始替换数据.

start:可选.从该位置开始读取数据,默认为0.如果为负值,表示倒数.

end:可选,到该位置前停止读取数据,默认等于数组长度.如果为负值,表示倒数.

```js
<script>
	let arr = [1,2,3,4,5,6];
	let result1 = [1,2,3,4,5,6].copyWithin(0);
	let result2 = [1,2,3,4,5,6].copyWithin(0,1);
	let result3 = [1,2,3,4,5,6].copyWithin(1,3,5);
	let result4 = [1,2,3,4,5,6].copyWithin(1,2,-1);
	let result5 = [1,2,3,4,5,6].copyWithin(1,-4,6);
	console.log(result1);  // [1, 2, 3, 4, 5, 6]
	console.log(result2);  // [2, 3, 4, 5, 6, 6]
	console.log(result3);  // [1, 4, 5, 4, 5, 6]
	console.log(result4);  // [1, 3, 4, 5, 5, 6]
	console.log(result5);  // [1, 3, 4, 5, 6, 6]
</script>
```

