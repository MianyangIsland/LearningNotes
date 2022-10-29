# TypeScript 命名空间

**命名空间一个最明确的目的就是解决重名问题.**

假设这样一种情况，当一个班上有两个名叫小明的学生时，为了明确区分它们，我们在使用名字之外，不得不使用一些额外的信息，比如他们的姓（王小明，李小明），或者他们父母的名字等等。 

命名空间定义了标识符的可见范围,一个标识符可在多个命名空间中定义,它在不同名字空间中的含义是互不相干的.这样,在一个新的名字空间中可定义任何标识符,它们不会与任何已有的标识符发生冲突,因为已有的定义都处于其他名字空间中.

TS中命名空间使用namespace来定义,语法格式如下:

```js
namespace SomeNameSpaceName { 
   export interface ISomeInterfaceName {      }  
   export class SomeClassName {      }  
}
```

以上定义了一个命名空间SomeNameSpaceName,如果我们需要在外部可以调用SomeNameSpaceName中的类和接口,则需要在类和接口添加**export**关键字.

要在另一个命名空间调用语法格式为:

```js
SomeNameSpaceName.SomeClassName;
```

如果一个命名空间在一个单独的TypeScrpt文件中,则应该使用三斜杠///引用它,语法格式如下:

```js
/// <reference path = "SomeFileName.ts" />
```

以下实例演示了命名空间的使用，定义在不同文件中： 

## IShape.ts 文件代码：

```js
namespace Drawing { 
    export interface IShape { 
        draw(); 
    }
}
```

## Circle.ts 文件代码：

```js
/// <reference path = "IShape.ts" /> 
namespace Drawing { 
    export class Circle implements IShape { 
        public draw() { 
            console.log("Circle is drawn"); 
        }  
    }
}
```

## Triangle.ts 文件代码：

```js
/// <reference path = "IShape.ts" /> 
namespace Drawing { 
    export class Triangle implements IShape { 
        public draw() { 
            console.log("Triangle is drawn"); 
        } 
    } 
}
```

使用 tsc 命令编译以上代码： 

```js
tsc --out app.js TestShape.ts  
```

