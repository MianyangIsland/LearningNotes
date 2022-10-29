14.2.1 动态脚本:

 <script>元素用于向网页中插入javascript代码,可以是scr属性包含的外部文件,也可以是作为该元素内容的源代码.动态脚本就是在页面内容初始加载时不存在,之后又通过DOM包含的脚本.与对应的HTML元素一样,有两种方式通过<script>动态为网页添加脚本:引入外部文件和直接插入源代码.

```js
<script src="doo.js"></script>


 let script = document.createElement("script");
      script.scr = 'foo.js';
      document.body.appendChild(script);
```

注意,在上面最后一行把<script>元素添加到页面之前,是不会开始下载外部文件的.当然也可以把它添加到<head>元素,同样可以实现动态脚本加载.

另一个动态插入JavaScript的方式是嵌入源代码.

<script>元素上有一个text属性,可以用来添加javascript代码.

只要是使用innserHTML创建的<script>元素,始终不会运行.

14.2.2 动态样式:

CSS样式在HTML页面中可以通过两个元素加载.<link>元素用于包含CSS外部文件,而<style>元素用于添加嵌入样式.与动态脚本类似,动态样式也是页面初始加载时不存在,而是在之后才添加到页面中的.

```js
<link rel="stylesheet" href="style.css" type="text/css">
```

通过外部文件加载样式是一个异步过程.因此,样式的加载和正执行的JavaScript代码并没有先后顺序.一般来说,也没必要知道样式什么时候加载完成.

14.2.3操作表格:略

14.2.4 使用NodeList:

理解Nodelist对象和相关的NamedNodeMap,HTMLCollection,是理解DOM编程的关键,这三个集合类型都是"实时的",意味着文档结构的变化会实时地在它们身上反映出来,因此它们地值始终代表最新状态.实际上,NodeList就是基于DOM文档地实时查询.

一般来说,最好限制操作NodeList地次数.因为每次查询都会搜索整个文档,所以最好把查询到地NodeList缓存起来.

14.3 MutationObserver接口:

MutationOberver接口可以在DOM被修改时异步执行回调.使用MutationObserver可以观察整个文档,DOM树的一部分,或某个元素.此外还可以观察元素属性,子节点,文本,或者前三者任意组合的变化.

14.3.1 基本用法:

MutationObserver的实例要通过调用MutationOberver构造函数并传入一个回调函数来创建:

```js
let oberver = new MutationObserver(()=>console.log('DOM was mutated!'));
```



1.observer()方法:

新创建的MutationObserver实例不会关联DOM的任何部分.要把这个observer与DOM关联起来,需要使用observer()方法.这个方法接收两个必须的参数:要观察其变化的DOM节点,以及一个MutationObserverInit对象.

MutationObserverInit对象用于控制观察那些方面的变化,是一个键/值对形式配置选项的字典.

```js
let observer = new MutationObserver(()=>console.log('body attributes changed!'));

      observer.observe(document.body,{attributes:true});

      document.body.className = 'foo';
      console.log('Changed body class');
```

回调并非与实际的DOM变化同步执行.

2.回调与MutationRecord :

每个回调都会接收到一个MutationRecord实例的数组.MutationRecord实例包含的信息包括发生了什么变化,以及DOM的哪一部分受到了影响.因为回调执行之前可能同时发生多个满足观察条件的事件,所以每次执行回调都会传入一个包含顺序入队的MutationObserver实例的数组:

```js
let observer = new MutationObserver((MutationRecord)=>{
         console.log(MutationRecord);
       });

       observer.observe(document.body,{attributes:true});

       document.body.setAttribute('foo','bar');
```



连续修改会生成多个MutationRecord实例,下次回调执行时就会收到包含所有这些实例的数组,顺序为变化事件发生的顺序.

传给回调的第二个参数是观察变化的MutationObserver的实例.

```javascript
//[MutationRecord] MutationObserver {}
let observer = new MutationObserver((MutationRecord,MutationObserver)=>{
        console.log(MutationRecord,MutationObserver);
      })

      observer.observe(document.body,{attributes:true});
      document.body.className = 'foo';
```

3.disconnect()方法:

默认情况下,只要观察的元素不被垃圾回收,MutationObserver的回调就会响应DOM变化事件,从而被执行.要提前终止回调,可以调用disconnect()方法.

```js
let observer = new MutationObserver(()=>{
         console.log('attributes changed!');
       })

       observer.observe(document.body,{attributes:true});
       document.body.className = 'foo';
       observer.disconnect();
       document.body.className  = 'foo';
       //没有日志输出
```

4.复用MutationObserver:

多次调用observe()方法,可以复用一个MutationObserver对象观察多个不同的目标的节点.此时,MutationRecord的target属性可以标识发生变化事件的目标节点.

```js
let observer = new MutationObserver((MutationRecord)=>{
         console.log(MutationRecord.map((x)=>x.target));
       })
       let childA = document.createElement('div');
       let childB = document.createElement('span');

       observer.observe(childA,{attributes:true});
       observer.observe(childB,{attributes:true});

       childA.setAttribute('foo','bar');
       childB.setAttribute('foo','baz');
        // [div, span]
```

5.重用MutationObserver :

调用disconnect()并不会结束MutationObserver的生命.还可以重新使用这个观察者,再将它关联到新的目标节点.

14.3.2 MutationObserverInit与观察范围.

相关内容请见p437.

14.3.3 异步回调与记录队列:

MutationObserver接口是出于性能考虑而设计的,其核心是异步回调与记录队列模型.为了在大量变化事件发生时不影响性能,每次变化的信息(由观察者实例决定)会保存在MutationRecord实例中,然后添加到记录队列.这个队列对每个MutationObserver实例都是唯一的,是所有DOM变化事件的有序列表.

1.记录队列.

2.takeRecords()方法:

调用MutationObserver实例的takeRecords()方法可以清空记录队列,取出并返回其中的所有MutationRecord实例.

这在希望断开与观察目标的联系,但又希望处理由于调用disconnect()而被抛弃的记录队列中的MutationRecord实例时比较有用.

14.3.4  性能,内存与垃圾回收:

略.