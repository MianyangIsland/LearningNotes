6.5 WeakMap: ES6新增的“弱映射”是一种新的集合类型,为这们语言带来了增强的键/值对存储机制.WeakMap是Map的“兄弟”类型，其API也是Map的子集.Weak描述的是js垃圾回收程序对待“弱映射”中键的方式.

6.5.1 基本ApI:

```javascript
let wm = new WeakMap();
```

键只能是Object或者继承Object的类型,尝试使用非对象设置键会抛出错误,值的类型没有限制.

//原始值可以先包装对象再用作键.

```javascript
const stringkey = new String('key1');
          const wm = new WeakMap([
              stringkey,'val1'
          ])
```

6.5.3  不可迭代键:

 6.5.4 使用弱映射 ：

  1.私有变量  ：弱映射造就了在JS中实现真正私有变量的一种新方式。前提很明确：私有变量会存储在弱映射中，以对象实例为键，以私有成员的字典为值.

2.DOM阶段元数据:

因为WeakMap实例不会妨碍垃圾回收，所以非常适合保存关联元数据.

```javascript
const m = new Map();
const loginButton = document.querySelector('#login');
m.set(loginButton,{disabled:true})//由于映射中还保存着按钮的引用，所以对应的DOM节点仍然会逗留在内存中，除非明确将其从映射中删除或等到映射本身被销毁
```

如果这里使用的是弱映射。那么当节点从DOM树中被删除后,垃圾回收程序就可以立即释放其内存.

```javascript
 const wm = new WeakMap();
          const loginButton = document.querySelector('#login');
          wm.set(loginButton,{disabled:true});
```

6.6 Set : Set在很多方面都像是加强的Map ,这是因为他们在大多数API和行为都是共有的.

6.6.1 基本API:

使用new关键字和Set构造函数可以创建一个空集合:

```javascript
const m = new Set(); 
const s1 = new set(['val1','val2','val3']);
```

初始化之后，可以使用add()增加值，使用has()查询，通过size取得元素数量，以及使用delete()和clear()删除元素.

其中clear()销毁集合实例中所有值.

与Map类似，Set可以包含任何JS数据类型作为值.使用严格对象相等的标准来检查值的匹配性.

6.6.2  顺序与迭代:

  Set会维护值插入的顺序，因此支持按顺序迭代.集合实例可以提供一个迭代器，能以插入顺序生成集合内容.通过values()方法及其别名方法keys()取得这个迭代器.

```javascript
const s1 = new Set(['val1','val2','val3']);
         for(let val of s1.values())
         {
             console.log(val);
         }
         /*
         val1
         val2
         val3
         */
```

因为values()是默认迭代器，所以可以直接对集合实例使用扩展运算，把集合转化为数组:

```javascript
 const s1 = new Set(['val1','val2','val3']);
        console.log([...s1]);//['val1', 'val2', 'val3']
```

省略内容请看书!



6.8  迭代与扩展操作:

 