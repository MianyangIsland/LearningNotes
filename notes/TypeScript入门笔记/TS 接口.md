# TypeScript 接口:

接口是一些列抽象方法的声明,是一些方法特征的集合,这些方法都应该是抽象的,需要由具体的类去实现,然后第三方就可以通过这组抽象方法调用,让具体的类执行具体的方法.

**TS 接口的定义如下:**

```js
interface interface_name { 
}
```

**实例**

以下实例中,我们定义了一个接口IPerson,接着定义了一个变量customer,它的类型是IPerson.

customer实现了接口IPerson的属性和方法.

```js
interface IPerson { 
    firstName:string, 
    lastName:string, 
    sayHi: ()=>string 
} 
 
var customer:IPerson = { 
    firstName:"Tom",
    lastName:"Hanks", 
    sayHi: ():string =>{return "Hi there"} 
} 
 
console.log("Customer 对象 ") 
console.log(customer.firstName) 
console.log(customer.lastName) 
console.log(customer.sayHi())  
 
var employee:IPerson = { 
    firstName:"Jim",
    lastName:"Blakes", 
    sayHi: ():string =>{return "Hello!!!"} 
} 
 
console.log("Employee  对象 ") 
console.log(employee.firstName) 
console.log(employee.lastName)
```

需要注意接口不能转换为JavaScript.它只是TypeScript的一部分.

编译以上代码,得到以下JavaScript代码:

```js
var customer = {
    firstName: "Tom",
    lastName: "Hanks",
    sayHi: function () { return "Hi there"; }
};
console.log("Customer 对象 ");
console.log(customer.firstName);
console.log(customer.lastName);
console.log(customer.sayHi());
var employee = {
    firstName: "Jim",
    lastName: "Blakes",
    sayHi: function () { return "Hello!!!"; }
};
console.log("Employee  对象 ");
console.log(employee.firstName);
console.log(employee.lastName);
```

## 联合类型和接口

以下实例演示了如何在接口中使用联合类型:

```js
interface RunOptions { 
    program:string; 
    commandline:string[]|string|(()=>string); 
} 
 
// commandline 是字符串
var options:RunOptions = {program:"test1",commandline:"Hello"}; 
console.log(options.commandline)  
 
// commandline 是字符串数组
options = {program:"test1",commandline:["Hello","World"]}; 
console.log(options.commandline[0]); 
console.log(options.commandline[1]);  
 
// commandline 是一个函数表达式
options = {program:"test1",commandline:()=>{return "**Hello World**";}}; 
 
var fn:any = options.commandline; 
console.log(fn());
```

编译以上代码，得到以下 JavaScript 代码： 

```ts
// commandline 是字符串
var options = { program: "test1", commandline: "Hello" };
console.log(options.commandline);
// commandline 是字符串数组
options = { program: "test1", commandline: ["Hello", "World"] };
console.log(options.commandline[0]);
console.log(options.commandline[1]);
// commandline 是一个函数表达式
options = { program: "test1", commandline: function () { return "**Hello World**"; } };
var fn = options.commandline;
console.log(fn());
```

## 接口和数组

接口中,我们可以将数组的索引值和元素设置为不同类型,索引值可以是数字或字符串.

设置元素为字符串类型:

```js
interface namelist { 
   [index:number]:string 
} 
 
// 类型一致，正确
var list2:namelist = ["Google","Runoob","Taobao"]
// 错误元素 1 不是 string 类型
// var list2:namelist = ["Runoob",1,"Taobao"]
```

## 接口和继承

接口继承就是说接口可以通过其他接口来扩展自己.TS允许接口继承多个接口.继承使用关键字**extends**

单接口继承语法格式:

```js
Child_interface_name extends super_interface_name
```

多接口继承的语法格式:

```js
Child_interface_name extends super_interface1_name, super_interface2_name,…,super_interfaceN_name
```

继承的各个接口使用逗号 , 分隔。 

**单接口实例**

```js
//使用类型断言的写法
interface Person { 
   age:number 
} 
 
interface Musician extends Person { 
   instrument:string 
} 
 
var drummer = <Musician>{}; 
drummer.age = 27 
drummer.instrument = "Drums" 
console.log("年龄:  "+drummer.age)
console.log("喜欢的乐器:  "+drummer.instrument)



interface Person{
    age:number
}

interface Musician extends Person{
    instrument:string
}

let drummer:Musician={
    age:27,
    instrument:'Drums'
}
console.log("年龄:  "+drummer.age)
console.log("喜欢的乐器:  "+drummer.instrument)

```

**多继承实例**

```js
interface IParent1 { 
    v1:number 
} 
 
interface IParent2 { 
    v2:number 
} 
 
interface Child extends IParent1, IParent2 { } 
var Iobj:Child = { v1:12, v2:23} 
console.log("value 1: "+Iobj.v1+" value 2: "+Iobj.v2)



//使用类型断言的写法
interface IParent1{
    v1:number
}
interface IParent2{
    v2:number
}

interface Child extends IParent1,IParent2{

}

let Iobj = <Child> {
   v1:12,
   v2:23
}
console.log(Iobj.v1);
console.log(Iobj.v2);
```

