6.4 Map :  在ES6之前，在js中实现“键/值”式存储可以使用Object来方便高效地完成,也就是使用对象属性作为键，再使用属性来引用值.但这种实现并非没有问题，为此TC39委员会专门为“键/值”存储定义了一个规范.

Map是一种新的集合类型，为这门语言带来了真正的键/值存储机制。Map的大多数特性都可以通过Object类型实现，但二者之间还是存在一些细微的差异.

6.4.1 :   基本API :

使用new关键字和Map构造函数可以创建一个空映射.

```javascript
const m = new Map();
```

创建的同时初始化实例,可给Map构造函数传入一个可迭代对象.

```javascript
const m1 = new Map([
    ['key1,'value1;],
     ['key2'value2],
       ['key3'value3]
]);
console.log(m1.size)//3;
```

初始化之后,可以使用set()方法再添加键/值对.可以使用get()和has()进行查询，可以通过size属性获取映射中的键/值对的数量,还可以使用delete()和clear()删除值.

```javascript
 const m = new Map();

    m.set('firstname','Matt')
    .set('lastname','Frisebie');

    console.log(m.has('firstname'));
    console.log(m.get('firstname'));

    m.delete('firstname');//只删除这一个键/值对

    m.clear();//清除这个映射实例中的所有键/值对
```

与Object只能使用数值，字符串或符号作为键不同,Map可以使用任何js数据类型作为键.Map内部使用SameValueZero比较操作，基本上相当于使用严格对象相等的标准来检查键的匹配性.与Object类似，映射的值是没有限制的.

6.4.2 顺序与迭代  ;

  与Object类型的一个主要差异是,Map实例会维护键值对的插入顺序，因此可以根据插入顺序执行迭代操作.

映射实例可以提供一个迭代器(Iterator)，能以插入顺序生成[key,value]形式的数组.可以通过entries()方法取得这个迭代器.

```javascript
 const m = new Map([
        ['key1','value1'],
        ['key2','value2'],
        ['key3','value3']
    ]);

    for(let pair of m.entries())
    {
        console.log(pair);
    }//['key1', 'value1']
['key2', 'value2']
 ['key3', 'value3']
```

因为entries()是默认迭代器，使用可以直接对映射实例使用扩展操作，把映射转换为数组.

```javascript
 const m = new Map([
        ['key1','value1'],
        ['key2','value2'],
        ['key3','value3']
    ]);

    console.log([...m]);//['key1', 'value1']
                        ['key2', 'value2']
                         ['key3', 'value3']
```

如果不使用迭代器，而是使用回调方式,则可以调用映射forEach(callback,opt_thisArg)方法并传入回调，依次迭代每个键/值对.传入的回调接收可选的第二个参数，这个参数用于重写回调内部this的值.

```javascript
const m = new Map([
        ['key1','value1'],
        ['key2','value2'],
        ['key3','value3']
    ]);

    m.forEach((val,key)=> console.log(`${key}-->${val}`));
    /*key1-->value1
      key2-->value2
      key3-->value3*/
```

keys()和values()分别返回以插入顺序生成键和值的迭代器.

键和值在迭代器遍历时是可以修改的，但映射内部的引用则无法修改.

```javascript
 const m1 = new Map([
         ['key1','value1']
     ]);
 //作为键的原始字符串是不能修改的

     for(let key of m1.keys())
     {
          key = 'newkey';
          console.log(key);//newkey
          console.log(m1.get('key1'));//value1
     }
```

```javascript
 const keyObj = {id:1};

        const m = new Map([
            [keyObj,'vall']
        ]);

        //修改了作为键的对象的属性，但对象在映射内部仍然引用相同的值.

        for(let key of m.keys())
        {
            key.id = 'newkey';
            console.log(key);//{id: 'newkey'}
            console.log(m.get(keyObj));//vall
        }

        console.log(keyObj);//{id: 'newkey'}
```

6.4.3  选择Object还是Map :

1.内存占用：  obj和map的工程级实现在不同浏览器间存在明显差异,但存储单个键/值对所占用的内存数量会随键的数量线性增加.给定固定大小的内存，map大约可以比obj多存储50%的键/值对.

2.插入性能：  向obj和map中插入新键/值对的消耗相当大，如果代码涉及大量插入操作,那么显然map的性能更好.

3.查找速度：如果只包含少量键/值对,则object有时候速度更快.如果代码涉及大量查找操作，那么某些情况下可能选择Obj更好一些.

4.删除性能： 如果代码涉及大量删除操作，那么应该选择Map.