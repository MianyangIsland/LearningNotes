# TS运算符

**算数运算符**

加法 +   减法  -   乘法  *   除法  /    取模  %    自增 ++   自减 --  

```js
var num1:number = 10
var num2:number = 2
var res:number = 0
   
res = num1 + num2
console.log("加:        "+res);

res = num1 - num2;
console.log("减: "+res)

res = num1*num2
console.log("乘:    "+res)

res = num1/num2
console.log("除:   "+res)
   
res = num1%num2
console.log("余数:   "+res)

num1++
console.log("num1 自增运算: "+num1)

num2--
console.log("num2 自减运算: "+num2)
```

**关系运算符**

关系运算符用于计算结果是否为true或者false

== 等于   !=  不等于   >  大于   < 小于  >= 大于或者等于   <=  小于或者等于

```js
var num1:number = 5;
var num2:number = 9;
 
console.log("num1 的值为: "+num1); 
console.log("num2 的值为:"+num2);
 
var res = num1>num2 
console.log("num1 大于n num2: "+res)
 
res = num1<num2 
console.log("num1 小于 num2: "+res)  
 
res = num1>=num2 
console.log("num1 大于或等于  num2: "+res)
 
res = num1<=num2
console.log("num1 小于或等于 num2: "+res)  
 
res = num1==num2 
console.log("num1 等于 num2: "+res)  
 
res = num1!=num2  
console.log("num1 不等于 num2: "+res)
```

**逻辑运算符**

&& and     ||  or     |  not

```js
var avg:number = 20; 
var percentage:number = 90; 
 
console.log("avg 值为: "+avg+" ,percentage 值为: "+percentage);
    
var res:boolean = ((avg>50)&&(percentage>80)); 
console.log("(avg>50)&&(percentage>80): ",res);
 
var res:boolean = ((avg>50)||(percentage>80)); 
console.log("(avg>50)||(percentage>80): ",res);
 
var res:boolean=!((avg>50)&&(percentage>80)); 
console.log("!((avg>50)&&(percentage>80)): ",res);
```

**短路运算符(&& 与 ||)**

&&与||运算符可用于组合表达式. &&运算符只有在左右两个表达式都为true时才返回true.||运算符只要期中一个表达式为true,则该组合就会返回true.

**位运算符**

位操作是程序设计中对位模式按位或二进制数的一元和二元操作.

&   AND，按位与处理两个长度相同的二进制数，两个相应的二进位都为 1，该位的结果值才为 1，否则为 0。 

|     OR，按位或处理两个长度相同的二进制数，两个相应的二进位中只要有一个为 1，该位的结果值为 1。 

~   取反，取反是一元运算符，对一个二进制数的每一位执行逻辑反操作。使数字 1 成为 0，0 成为 1。 

^  异或，按位异或运算，对等长二进制模式按位或二进制数的每一位执行逻辑异按位或操作。操作的结果是如果某位不同则该位为 1，否则该位为 0。 

<<    左移，把 << 左边的运算数的各二进位全部左移若干位，由 << 右边的数指定移动的位数，高位丢弃，低位补 0。 

右移，把 >> 左边的运算数的各二进位全部右移若干位，>> 右边的数指定移动的位数。   >>

无符号右移，与有符号右移位类似，除了左边一律使用0 补位。  >>>

```ts
var a:number = 2;   // 二进制 10 
var b:number = 3;   // 二进制 11
    
var result; 
        
result = (a & b);     
console.log("(a & b) => ",result)
            
result = (a | b);          
console.log("(a | b) => ",result)  
        
result = (a ^ b);  
console.log("(a ^ b) => ",result);
    
result = (~b); 
console.log("(~b) => ",result);
 
result = (a << b); 
console.log("(a << b) => ",result); 
 
result = (a >> b); 
console.log("(a >> b) => ",result);
 
result = (a >>> 1); 
console.log("(a >>> 1) => ",result);
```

**赋值运算符** 

赋值运算符用于给变量赋值。 

=(赋值)    +=(先进行加运算后赋值)    -=(先进行减运算后赋值)  *=  (先运行乘运算后赋值)   /=(先运行除运算后赋值)

类似的逻辑运算符也可以与赋值运算符联合使用：<<=, >>=, >>=, &=, |= 与 ^=。 

**三元运算符(?)**

三元运算有3个操作数,并且需要判断布尔表达式的值.该运算符的主要是决定那个值应该赋值给变量.

```js
Test ? expr1 : expr2
```

- Test − 指定的条件语句
- expr1 − 如果条件语句 Test 返回 true 则返回该值
- expr2 − 如果条件语句 Test 返回 false 则返回该值

**类型运算符**

typeof运算符,typeof是一元运算符,返回操作数的数据类型.

```
var num = 12 
console.log(typeof num);   //输出结果: number
```

**其他运算符**

负号运算符(-) 更改操作数的符号.

字符串运算符(+):连接运算符(+)  +  运算符可以拼接两个字符产.

```js
var msg:string = "RUNOOB"+".COM" 
console.log(msg)
```

