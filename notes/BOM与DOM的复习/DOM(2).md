# DOM编程

## 动态脚本

<script>元素用于向网页中插入JavaScript代码,可以是src属性包含的外部文件,也可以是作为该元素内容的源代码.

由有两种方式通过<script>动态为网页添加脚本:引入外部文件和直接插入源代码.

动态加载外部文件很容易实现,比如下面的<script>元素:

```js
<script src="foo.js"></script>
```

可以像这样通过DOM编程创建这个节点:

```js
let script = document.createElement('script');
      script.src = 'foo.js';
      document.body.appendChild(script);
```

## 动态样式

CSS样式在HTML页面中可以通过两个元素加载. <link>元素用于包含CSS外部文件,而<style>元素用于添加嵌入样式.

与动态脚本类似,动态样式也是页面初始加载是并不存在,而是在之后才添加到页面中的.

```js
<link rel="stylesheet" type="text/css" href="style.css">
```

通过外部文件加载样式是一个异步过程.因此,样式的加载和正执行的JS代码并没有先后顺序.

## 使用NodeList

实际上,NodeList就是基于DOM文档的实时查询.

任何时候要迭代NodeList,最好再初始化一个变量保存当时查询时的长度,然后用循环变量与这个变量进行比较.

```js
let divs = document.getElementsByTagName('div');
       for(let i=0 , len = divs.length ; i<len;++i){
        //执行代码
       }
```

因为len保存着循环开始时集合的长度,而这个值不会随着集合增大动态增大,所以就可以避免无限循环.

## MutationObserver接口

MutationObserver接口可以在DOM被修改时执行异步回调. 使用MutationObserver可以观察整个文档,DOM树的一部分,或某个元素.此外还可以观察元素属性,子节点,文本或者前三者任意组合的变化.

### 基本用法

MutationObserver的实例要通过调用MytationObserver构造函数并传入一个回调函数来创建:

```js
let observer = new MutationObserver(()=>console.log('DOM was mutated!'))
```

1.observer()方法:

新创建的MutationObserver实例不会关联DOM的任何部分.要把这个observer与DOM关联起来,需要使用observer()方法.这个方法接收两个必须的参数: 要观察其变化的DOM节点,以及一个MutationObserverInit对象.

MutationObserverInit对象用于控制那些方面的变化,是一个键/值对形式配置选项字典.

```js
let observer = new MutationObserver(()=>console.log('<body> attributes changed'));
        observer.observe(document.body,{attributes:true});
```

执行以上代码后,<body>元素上任何属性发生变化都会被这个MutationObserver实例发现,然后就会异步执行注册的回调函数.

注意: <body>元素后代的修改或其他非属性修改都不会触发回调进入任务队列.

2.回调与MutationRecord

每个回调都会收到一个MutationRecord实例的数组.

MutationRecord实例包含的信息包括发生了什么,以及DOM的那一部分收到了影响.

因为回调之前可能同时发生多个满足观察条件的事件,所以每次执行回调都会传入一个包含按顺序入队的MutationRecord实例的数组.

```js
let observer = new MutationObserver((mutationRecords)=>console.log(mutationRecords));
```

连续修改会生成多个MutationRecord实例,下次回调执行时就会收到包含所有这些实例的数组, 顺序为变化事件发生的顺序.

传给回调函数的第二个参数是观察变化的MutationObserver的实例.

3.disconnect()

默认情况下,只要被观察的元素不被垃圾回收,MutationObserver的回调就会响应DOM变化事件,从而被执行.要提前终止执行回调,可以调用disconnect()方法.

调用disconnect()之后,不仅会停止此后变化事件的回调,也会抛弃已经加入任务队列要异步执行的回调.

4.复用MutationObserver

多次调用observe() 方法,可以复用一个MutationObserver对象观察多个不同的目标节点,此时,MutationRecord的target属性可以标识发生变化事件的目标姐节点.

```js
 let observer = new MutationObserver((MutationRecords)=>{
        console.log(MutationRecords.map(x=>x.target))
       })
```

disconnect()方法是一个"一刀切"方案,调用它会停止观察所有目标.

5.复用MutationObserver

调用disconnect()并不会结束MutationObserver的生命.还可以重新使用这个观察者,再将它关联到新的目标节点.