## window对象

 BOM的核心是window对象,表示浏览器的实例.window对象在浏览器中有两重身份,一个是ES中的Global对象,另一个就是浏览器窗口的JavaScript接口.这意味着网页中定义的所有对象,变量和函数都是以window对象作为其Global对象,都可以访问其上定义的parseInt()等全局方法.

### Global作用域

因为window对象被复用为ES的Global对象,所以通过var声明的所有全局变量和函数都会变成window对象的属性和方法.

eg:

```js
var age = 29;
var sayAge = () => this.age;

console.log(window.age);//29
console.log(sayAge());//29
console.log(window.sayAge());//29
```

注意,如果在这里使用let或const 替代var,则不会把变量添加给全局对象. 另外,访问未定义的变量是会抛出错误,但是可以在window对象上查询是否存在可能未声明的变量.

### 窗口关系

 top对象始终指向最上层(最外层)窗口,即浏览器窗口本身.而parent对象则始终指向当前窗口的父窗口.如果当前窗口是最上层窗口,则parent等于top(都等于window).

  还有一个self对象,它始终会指向window.实际上,self和window就是同一个对象.之所以还要暴露self,只是为了和top,parent保持一致.

### 窗口位置与像素比

 screenLeft和screenTop属性,用于表示窗口相对于屏幕左侧和顶部的位置,返回值的单位是CSS像素.

 moveTo和moveBy方法移动窗口.这两个方法都接收两个参数,其中moveTo()接收要移动到的新位置的绝对坐标x和y;而moveBy()则接收相对于当前位置在两个方向上移动的像素数.

```js
window.moveTo(200,300); //把窗口移动到坐标位置(200,300)
window.moveBy(-50,0);// 把窗口向左移动50像素
```

### 窗口大小

 所有现代浏览器都支持4个属性:

 innerWidth,innerHeight,outerWidth,outerHeight. 

innerHeight和innerHeight返回浏览器窗口中页面视口的大小. document.documentElement.clientWidth和document.documentElement.clientHeight返回页面视口的宽度和高度.

浏览器自身的精确尺寸不好确定,但可以确定页面视口的大小.

```js
let pageWidth = window.innerWidth;
let pageHeight = window.innerHeight;

if(typeof pageWidth !== 'number'){
    if(document.compatMode == 'CSS1Compat'){
        pageWidth = document.documentElement.clientWidth;
        pageHeight = document.documentElement.clientHeight;
    }
    else
    {
        pageWidth = document.body.clientWidth;
        pageHeight = document.body.clientHeight;
    }
}
```

在移动设备上,window.innerWidth和window.innerHeight 返回视口的大小,也就是屏幕上页面可视区域的大小.

可以使用resizeTo()和resizeBy()方法调整窗口大小.这两个方法都接收两个参数,resizeTo()接收新的宽度和高度值,而resizeBy()接收宽度和高度各要缩放多上.

### 视口位置

有时候,浏览器窗口尺寸通常无法满足完整显示整个页面,为此用户可以通过滚动在有限的视口查看文档. 度量文档相对于视口滚动距离的属性有两对,返回相等的值: window.pageXoffset / window.scrollX和window.pageYoffset/widnow.scrollY.

可以使用scroll(),scrollTo()和scrollBy()方法滚动页面.这3个方法都接收表示相对视口距离的x和y坐标,这两个参数在前两个方法中表示要滚动到坐标,最后一个方法表示滚动的距离.

这几个方法也都接收一个ScrollToOptions字典,除了提供偏移值,还可以通过behavior属性告诉浏览器是否平滑滚动.

```js
window.scrollTo({
    left:0,
    top:0,
    behavior:'auto'//正常滚动
});

window.scrollTo({
    left:'100',
    top:'100',
    behavior:'smooth'//平滑滚动
})
```

导航与打开新窗口:

window.open()方法可以用于导航到指定URL,也可以用于打开新浏览器窗口.这个方法接收4个参数:

1. 要加载的URL,
2. 目标窗口
3. 特性字符串
4. 表示新窗口在浏览器历史记录中是否替代当前加载页面的布尔值.

通常,调用这个方法时只传前3个参数,最后一个参数只有在不打开新窗口时才会使用.

### 定时器

JS在浏览器中是单线程执行的,但允许使用定时器指定在某个时间之后或每隔一段时间就执行相应的代码.

setTimeout()用于指定在一定时间后执行某些代码,而setInterval()用于指定每隔一段时间执行某些代码.

setTimeout()通常接收两个参数: 要执行的代码和在执行回调函数前等待的时间(毫秒为单位).

JS是单线程的,所以每次只能执行一段代码.为了调度不同代码的执行,JS维护了一个任务队列.其中的任务会按照添加到队列的先后顺序执行.setTimeout()的第二个参数只是告诉JS引擎在指定的毫秒数过后把任务添加到这个队列.如果队列是空的,则会立即执行该代码.如果队列不是空的,则代码必须等待前面的任务执行完才能执行.

setTimeout()会返回一个数值ID.这个超时ID是被排期执行代码的唯一标识符,也用于取消该任务.要取消等待中的排期任务,可以调用clearTimeout()方法并传入超时ID.

eg:

```js
let timeoutId = settimeout(()=>alert('Hello world!'),1000);
clearTimeout(timeout);
```

只要是在指定时间之前调用clearTimeout(),就可以取消超时任务.在任务队列再调用clearTimeout()没有效果.

setInteravl()与setTimeout()的使用方法类似,只不过指定的任务会每隔指定时间就执行一次,直到取消循环时或页面卸载.

要取消循环定时,可以调用clearInterval()并传入定时ID.

## location对象

 location是最常用的BOM对象之一,提供了当前窗口中加载文档的信息,以及通常的导航功能.这个对象独特的地方在于,它既是window属性也是document属性. 也就是说,window.location和document.location指向同一个对象. location对象不仅保存着当前加载文档的信息,也保存着把URL解析为离散片段后能够通过属性访问的信息.

### 查询字符串

 虽然location.search返回了从问好开始直到URL末尾的所有内容,但没有办法逐个访问每个查询参数. 下面的函数解析了查询字符串,并返回一个以查询参数为属性的对象.

```js
let getQueryStringArgs = function(){
    let qs = (location.search.length > 0 ? location.search.substring(1) : ''),
    args = {};
    for(let item of qs.split('&').map(kv => kv.split('='))){
        let name = decodeURIComponent(item[0]);
        let value = decodeURIComponent(item[1]);
        if(name.length){
            args[name] = value;
        }
    }
}
```

#### URLSearchParams

URLSearchParams 提供了一组标准API方法,通过它们可以检查和修改查询字符串.给URLSearchParams构造函数传入了一个查询字符串,就可以创建一个实例.

```js
let qs = "?q=javascript&num=10";
let searchParams = new URLSearchParams(qs);

console.log(qs.toString());//q=javascript&num=10;

searchParams.has('num');//true
searchParams.get('num');//10


searchParams.set('page',3);
console.log(searchParams.toString());//q=javascript&num=10&page=3

searchParams.delete('q');
console.log(searchParams.toString());//"num=10&page=3"
```

### 操作地址

 可以通过修改location对象修改浏览器的地址.首先,最常见的是使用assign()方法并传入一个URL.

```js
location.assign("http://www.wrox.com");
```

这行代码会立即启动导航到新URL的操作,同时在浏览器历史记录中增加一条记录.如果给location.href和window.loacation设置一个URL,也会以同一个URL值调用assign()方法.

除了hash之外,只要修改location的一个属性,就会导致页面重新加载新URL.

在前面提到的方式修改URL之后,浏览器历史记录中就会增加相应的记录.当用户单机"后退"按钮时,就会导航到前一个页面.如果不希望增加历史记录,可以使用replace()方法.这个方法接收一个URL参数,但重新加载后不会增加历史记录.调用replace()之后,用户不能回到前一页.

最后一个修改地址的方法reload():

它能重新加载当前显示的页面.调用reload()而不传参数,页面会以最有效的方式重新加载.也就是说,如果页面自上次请求以来没有修改过,浏览器可能会从缓存中加载页面. 如果想强制从服务器重新加载,可以像下面这样给reload()穿个true.

```js
location.reload();//重新加载,可能是从缓存加载
location.reload(true);//重新加载,从服务器加载
```

脚本中位于reload()调用之后的代码可能执行也可能不执行,这取决于网络延迟和系统资源等因素.为此,最好把reload()作为最后一行代码.

## history对象

history对象表示当前窗口首次使用以来用户的导航历史记录.因为history是window属性,所以每个window都有自己的history对象.出于安全考虑,这个对象不会是暴露用户访问过的URL,但可以通过它在不知道实际URL的情况下前进和后退.

### 导航

go():

 可以在用户历史记录中沿任何方向导航,可以前进也可以后退.这个方法之接收一个参数,这个参数可以是一个整数,表示前进或后退多少步负值表示在历史记录中后退,正值表示在历史记录中前进.

go()有两个简写方法: back()和forward()  . 这两个方法分别模拟了浏览器的后退按钮和前进按钮.

history对象还要一个length属性,表示历史记录中又多少个条目.这个属性反映了历史记录的数量,包括可以前进和后退的页面.对于窗口或标签页中加载的第一个页面,history.length 等于1.

