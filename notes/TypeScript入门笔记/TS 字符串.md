# TypeScript String(字符串)

String对象用于处理文本(字符串).

**语法**

```js
var txt = new String("string");
或者更简单方式：
var txt = "string";
```

## String对象属性

constructor :对创建对象的函数的引用.

```js
var str = new String( "This is string" ); 
console.log("str.constructor is:" + str.constructor)
输出结果：

str.constructor is:function String() { [native code] }
```

length:返回字符串的长度.

prototype:允许你像对象添加属性和方法.

**String方法**:

charAt():返回在指定位置的字符串.

charCodeAt();返回在指定的位置的字符的Unicode编码.

concat(): 连接两个或更多字符串,并返回新的字符串.

indexOf():返回某个指定的字符串值在字符串中首次出现的位置.

lastIndexOf():从后像前搜索字符串,并从起始位置(0)开始计算返回字符串最后出现的位置.

localeCompare():用本地特定的顺序来比较两个特定的字符串.

```js
var str1 = new String( "This is beautiful string" );
  
var index = str1.localeCompare( "This is beautiful string");  

console.log("localeCompare first :" + index );  // 0
```



match():查找找到一个或多个正则表达式的匹配.

```js
var str="The rain in SPAIN stays mainly in the plain"; 
var n=str.match(/ain/g);  // ain,ain,ain
```

replcae():替换与正则表达式匹配的字串.

```js
var re = /(\w+)\s(\w+)/; 
var str = "zara ali"; 
var newstr = str.replace(re, "$2, $1"); 
console.log(newstr); // ali, zara
```

search(): 检索与正则式相匹配的值.

```js
	
var re = /apples/gi; 
var str = "Apples are round, and apples are juicy.";
if (str.search(re) == -1 ) { 
   console.log("Does not contain Apples" ); 
} else { 
   console.log("Contains Apples" ); 
} 
```

slice(开始位置,长度):提取字符串片断,并在新的字符串中返回被提取的部分.

```js
console.log(str1.slice(0,4));
```



split():把字符串分割为子字符串数组.

substr():从起始索引号提取字符串中指定数目的字符.

substring():提取字符串中两个指定的索引号之间的字符.

Warning:第一个索引字符包含,第二个字符不包含.

 toLocaleLowerCase():根据主机的语言环境把字符串转换为小写，只有几种语言（如土耳其语）具有地方特有的大小写映射。 

 toLocaleUpperCase():据主机的语言环境把字符串转换为大写，只有几种语言（如土耳其语）具有地方特有的大小写映射 .

toLowerCase():把字符串转换为小写。

toString():返回字符串。

toUpperCase():把字符串转换为大写。

valueOf():返回指定字符串对象的原始值。