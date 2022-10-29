1.2 推迟执行脚本:

​    defer属性：脚本会被延迟到整个页面都解析完毕再运行. 相当于告诉浏览器立即下载，但延迟执行.

 1.3异步执行脚本:

​    async属性：告诉浏览器，不必等脚本下载和执行完再加载页面，同样也不必等到该异步脚本下载和执行后再加载其他脚本。 所以，异步脚本不应该在加载期间修改DOM.

1.4动态加载脚本:

   因为js可以使用DOM API ,所以通过向DOM 中动态添加script元素同样可以加载指定的脚本.只要创建一个script元素并将其添加到DOM即可.

   ```javascript
let script = document.createElement('script');
       script.src = 'xxxx.js';
    document.head.appendChild(script);
   ```

默认情况下，以这种方式创建的script元素是以异步方式加载的，相当于添加了async属性.由于并不是所有浏览器都支持async属性，所以如果要同一动态脚本的加载行为，可以明确将其设置为同步加载.

```javascript
let script = document.createElement('script');
       script.src = 'xxxx.js';
       script.async = false;
    document.head.appendChild(script);
```

总结：加载js脚本的三种方法:

  1.行内js  在script标签中书写js代码

  2.scr属性  引入外部js

  3.动态加载脚本.

 1.5:XHTML中的变化:看书  不重要.

 2.2:  行内代码与外部文件:

​     推荐使用外部文件:    1. 可维护性，  2.缓存.  3.适应未来.

 2.4 <noscript>元素

  用于给不支持js的浏览器提供替代内容.

<noscript>元素可以包含任何可以出现在<body>中的html元素，<script>除外.适用场景：1.浏览器不支持脚本. 2. 浏览器对脚本的支持被关闭.

任何一个条件被满足，包含在<noscript>中的内容就会被渲染。否则，浏览器不会渲染<noscript>中的内容.

  