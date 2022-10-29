# TypeScript类

TypeScript是面向对象的JavaScript.类描述了所创建的对象共同的属性和方法.TypeScript支持面向对象的所有特性,比如,类,接口等.

TypeScript类定义方式如下:

```js
class class_name { 
    // 类作用域
}
```

定义类的关键字为 class，后面紧跟类名，类可以包含以下几个模块（类的数据成员）： 

- **字段** − 字段是类里面声明的变量。字段表示对象的有关数据。
- **构造函数** − 类实例化时调用，可以为类的对象分配内存。
- **方法** − 方法为对象要执行的操作。

**实例**

创建一个Person类:

```js
class Person {
}
```

**创建类的数据成员**

以下实例我们声明了类Car,包含字段为engine,构造函数在类实例化后初始化字段engine.this关键字表示当前类实例化的对象.注意构造函数的参数名与字段名相同,this.engine表示类的字段.

此外我们也在类中dinginess一个方法disp().

```js
class Car { 
    // 字段 
    engine:string; 
 
    // 构造函数 
    constructor(engine:string) { 
        this.engine = engine 
    }  
 
    // 方法 
    disp():void { 
        console.log("发动机为 :   "+this.engine) 
    } 
}
```

编译以上代码，得到以下 JavaScript 代码： 

```js
var Car = /** @class */ (function () {
    // 构造函数 
    function Car(engine) {
        this.engine = engine;
    }
    // 方法 
    Car.prototype.disp = function () {
        console.log("发动机为 :   " + this.engine);
    };
    return Car;
}());
```

**创建实例化对象**

我们使用new关键字来实例化类的对象,语法格式如下:

```js
var object_name = new class_name([ arguments ])
```

类实例化时会调用构造函数，例如： 

```js
var obj = new Car("Engine 1")
```

类中的字段属性和方法可以使用 . 号来访问： 

```js
// 访问属性
obj.field_name 

// 访问方法
obj.function_name()
```

**完整实例**

```js
class Car{
    //字段
    engine:string

   //构造函数
   constructor(engine:string)
   {
       this.engine = engine;
   }
   //方法
   disp():void{
    console.log('函数中现实发动机型号:  '+this.engine);
   }
}


//创建一个对象
let obj = new Car('XXSY1');

console.log(obj.engine);

obj.disp();
```

## 类的继承

TypeScript支持继承类,即使我们可以在创建类的时候继承一个已存在的类,这个已存在的类称为父类,继承它的类称为子类.类继承使用**extends**,**子类除了不能继承父类的私有成员(方法和属性)和构造函数**,其他的都可以继承.

TypeScript一次只能继承一个类,不支持继承多个类,但TypeScript支持多重继承(A 继承 B, B 继承 C).

语法格式如下:

```js
class child_class_name extends parent_class_name
```

**实例**

类的继承:实例中创建了Shape类,Circle类继承了Shape类,Circle类可以直接使用Area属性:

```js
class Shape{
    Area:number

    constructor(a:number)
    {
        this.Area = a;
    }
}

class Circle extends Shape{
    disp():void{
        console.log(this.Area)
    }
}

let obj = new Circle(233)
obj.disp();
```



需要注意的是子类只能继承一个父类,TypeScript不支持继承多个类,但支持多重继承,如下实例:

```js
class Root { 
   str:string; 
} 
 
class Child extends Root {} 
class Leaf extends Child {} // 多重继承，继承了 Child 和 Root 类
 
var obj = new Leaf(); 
obj.str ="hello" 
console.log(obj.str)
```

**继承类的方法重写**

类继承后,子类可以对父类的方法重新定义,这个过程称之为方法的重写.其中super关键字是对父类的直接引用,该关键字可以引用父类的属性和方法,

```js
class PrinterClass{
    doPrint():void{
        console.log('父类的doPrint()方法')
    }
}

class StringPrinter extends PrinterClass{
    doPrint(): void {
        super.doPrint();//调用父类的函数
        console.log('子类的doPrint()方法');
    }
}
let obj1 = new PrinterClass();
let obj2 = new StringPrinter();
obj1.doPrint();
obj2.doPrint();
```

**static关键字**

static关键字用于定义类的数据成员(属性和方法)为静态的,静态成员可以直接通过类名调用.

```js
class StaticMem {  
   static num:number; 
   
   static disp():void { 
      console.log("num 值为 "+ StaticMem.num) 
   } 
} 
 
StaticMem.num = 12     // 初始化静态变量
StaticMem.disp()       // 调用静态方法
```

**instanceof运算符**

instanceof运算符用于判断对象是否指定的类型,如果是返回true,否则返回false.

```js
class Person{ } 
var obj = new Person() 
var isPerson = obj instanceof Person; 
console.log("obj 对象是 Person 类实例化来的吗？ " + isPerson);
```

**访问控制修饰符**

TypeScript中,可以使用访问修饰符来保护类,变量,方法和构造方法的访问.TypeScript支持3中不同的访问权限.

- **public（默认）** : 公有，可以在任何地方被访问。
- **protected** : 受保护，可以被其自身以及其子类访问。
- **private** : 私有，只能被其定义所在的类访问。

**类和接口**

类可以实现接口,使用关键字implements,并将interest字段作为类的属性使用.

```js
interface ILoan{
    interest:number
}

class AgriLoan implements ILoan{
    interest: number;
    rebate:number;

    constructor(interest:number,rebate:number){
        this.interest = interest;
        this.rebate = rebate;
    }
}

let obj = new AgriLoan(10,1);
console.log("利润为 : "+obj.interest+"，抽成为 : "+obj.rebate )
```

