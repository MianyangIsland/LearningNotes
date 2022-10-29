12.1 window对象:

BOM的核心是window对象,表示浏览器的实例.window对象在浏览器中有两重身份,一个是ES中的Global对象,另一个就是浏览器窗口的JS接口.这意味着网页中定义的所有对象,变量和函数都以window作为其Global对象,都可以访问其上定义的parseInt()等全局方法.

12.1.1Global作用域:因为window对象被复用为ES的Global对象,所以通过var声明的所有全局变量和函数都会变成window对象的属性和方法.

12.1.3窗口位置与像素比: 

1.window对象的位置可以通过不同的属性和方法来确定.现代浏览器提供了screenLeft和screenTop属性,用于表示窗口相对于屏幕左侧和顶部的位置,返回值的单位为CSS像素.

2.可以使用moveTo()和moveBy()方法移动窗口,这两个方法都接收两个参数,其中moveTo()接收要移动到的新位置的绝对坐标x和y,而moveBy()则接收相对当前位置在两个方向上移动的像素数.

3.像素比:略.

12.1.4 窗口大小:

innerWidth和innerHeight返回浏览器窗口中页面视口的大小(不包含浏览器边框和工具栏).

document.documentElement.clientWidth和doucment.documentElement.clientHeight返回页面视口的宽度和高度.

在移动设备上,window.innerHeight和window.innerHeight返回视口大小,也就是屏幕上可视区域的大小.

可以使用resizeTo()和resizeBy()方法调整窗口大小.这两个参数都接收两个参数,resizeTo()接收新的宽度和高度值,而resizeBy()接收宽度和高度各要缩放多少.

12.1.5 视口位置:

度量文档(HTML)相对于视口滚动距离的属性有两对.返回相等的值:window.pageXoffset/window.scrollX和window.pageYoffset.window.scrollBy.

可以使用scroll(),scrollTo()和scorllBy()方法滚动页面.这三个方法都接收表示相对视口的x和y坐标,这两个参数在前两个方法中表示要滚动到的坐标,在最后一个方法中表示滚动的距离.

12.1.6 导航与打开新窗口:

window.open()方法可以用于导航到指定URL,也可以用于打开新浏览器窗口.这个方法接收4个参数:要加载的URL,目标窗口,性质字符串和表示新窗口在浏览器历史记录中是否代替当前加载页面的布尔值.通常这个方法只传3个参数,最后一个参数只有在不打开新窗口时才会使用.

12.1.7定时器:

setTimeout()用于指定一定时间后执行某些代码,而setTimeInterval()用于指定每隔一段时间执行某些代码.

setTimeout()方法通常接收两个参数:要执行的代码和在执行回调函数前等待的时间(毫秒).会返回一个表示该超时排期的数值ID.这个超时ID是被排期执行代码的唯一标识符,可用于取消该任务.要取消等待中的排期任务,可以调用clearTimeout()方法并传入超时ID.

```js
let timeoutId = setTimeout(()=>alert('hello world!'),1000);
        clearTimeout(timeoutId);
```

setTimeInterval()同样可以接收两个参数:要执行的代码以及把下一次执行定时代码的任务添加到队列要等待的时间.(毫秒)

clearInterval()传入定时ID,取消循环定时.

12.1.8 系统对话框:

alert():只接收一个参数.传入的字符串会显示一个在系统对话框中.如果传给alert()的参数不是一个原始字符串,则会调用这个值的toString()方法将其转换为字符串.警告框通常用于向用户展示一些他们无法控制的信息,比如报错.

confirm():确认框有两个按钮:Cancel和OK.用户通过点击不同的按钮表面希望接下来执行什么操作.

返回值:true == Ok,false == Cancel.

提示框:prompt():提示用户输入信息.除了OK和Cancel按钮,还会显示一个文本框,让用户输入内容.接收两个参数:要显示给用户的文本,以及文本框的默认值.如果点击Cancel,或者对话框被关闭,则prompt()返回null.

window.print()//显示打印对话框.

window.find()//显示查找对话框.

12.2 localtion对象:

最有用的BOM之一,提供了当前窗口中加载文档的信息,以及通常的导航功能.也是document属性.

12.5 history对象:

12.5.1导航:

1.go()方法可以在用户历史记录中沿任何方向导航,可以前进也可以后退.这个方法只接收一个参数,这个参数可以是一个参数,表示前进或后退多少步.负数表示后退,正值表示前进.

go()方法有两种简写方法:back()和forward().模拟浏览器的后退按钮和前进按钮.

length属性:表示历史记录中有多个条目,这个属性反映了历史记录的数量,包括可以前进和后退的页面.

12.5.2历史状态管理:

history.pushState()方法API可以让开发者改变浏览器URL而不会加载新页面.

接收三个参数:一个state对象,一个新状态的标题和一个(可选的)相对URL.执行后,状态信息就会被推到历史记录中,浏览器地址栏也会改变以反映新的相对URL.