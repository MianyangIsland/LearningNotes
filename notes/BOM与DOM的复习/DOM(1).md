## 文档对象模型(DOM)

DOM表示由多层节点构成的文档,通过它开发者可以添加,删除和修改页面的各个部分. DOM 现在是真正跨平台,预言无关的表示和操作网页的方式.

### Node类型

DOM1描述了名为Node的接口,这个接口是所有DOM节点类型都必须实现的.Node接口在JS中被实现为Node类型,在JS中,所有节点类型都继承Node类型,因此所有类型都共享相同的基本属性和方法.

每个节点都有nodeType属性,表示该节点的类型.节点类型由定义在Node类型上的12个数值常量表示:

浏览器并不支持所有节点类型.开发者最常用到的是节点和文本节点.

1.nodeName 与 nodeValue:

nodeName与nodeValue保存着有关节点的信息.这两个属性的值完全取决于节点类型.在使用这个属性前,最好先检测节点类型:

```js
if(someNode.nodeType ===1 ){
    value = someNode.nodeName;//显示元素的标签名
}
```

对于元素而言,nodeName始终等于元素的标签名,而nodeValue则始终为null.

2.节点关系:

文档中的所有节点都与其他节点有关系.这些关系可以形容为家族关系,相当于把文档树比作家谱.在HTML中,<body>元素是<html>元素的子元素,而<html>元素则是<body>的父元素.<head>元素是<body>元素的兄弟元素,因为他们有共同的父元素<html>.

每个节点都有一个childNodes属性,其中包含一个NodeList实例.NodeList是一个类数组对象,用于存储可以按位置存取的有序节点.注意,NodeList并不是Array的实例,但可以使用中括号访问它的值,而且它也有length属性.Nodelist对象独特的地方在于,它其实是一个对DOM结构的查询,因此DOM结构的变化也会自动地在NodeList中反映出来.我们通常说Nodelist是实时地活动对象,而不是第一次访问时所获得内容地快照.

使用Array.prototype.slice()可以像前面介绍arguments时一样把NodeList对象转换为数组.

也可以使用ES6的Array.from(someNode.ChildNodes);

每个节点都有一个parentNode属性,指向其DOM树中的父元素.childNodes中的所有节点都有一个共同的父元素,因此它们的parentNode属性指向同一个节点.此外,childeNodes列表中的节点都是同一列表中其他节点的同胞节点.而使用previousSibling和nextSibling可以在这个列表节点间导航. 这个列表中的第一个节点的previousSibling属性是null,最后一个节点的nextSibling属性也是null.

注意: 如果childNodes中只有一个节点,则它的previousSibling和nextSibling属性都是nulll.

父节点和它的第一个以及最后一个子节点也有专门的属性: firstChild和lastChild.

hasChildNodes(),这个方法如果返回true则说明节点有一个或多个子节点.

3.操纵节点:

appendChild(): 用于在childNodes列表末尾添加节点 .  appendChild()方法返回新添加的节点.

```js
let returnedNode = someNode.appendChild(newNode);
alert(returnNode == newNode);
alert(someNode.lastChild == newNode);
```

如果把文档中已经存在的节点传给appendChild(),则这个节点会从之前的位置被转移到新位置.

insertBefor():用于把节点放到childNodes中的特定位置而不是末尾. 这个方法接收两个参数:要插入的节点和参照节点. 调用这个方法后,要插入的节点会变成参照节点的前一个同胞节点,并被返回.

 注意:如果参照节点是null,则insertBefore()与appendChild()效果相同.

appendChild()和insetBrfore()在插入节点时不会删除任何已有节点.

replcaeChild()方法接收两个参数:要插入的节点和要替换的节点.要替换的节点会被返回并从文档树中被移除,要插入的节点会取而代之.

removeChild()方法可以移除节点. 这个方法接收一个参数,即要移除的节点. 被移除的节点会被返回.

4.其他方法:

所有节点还共享了两个方法. 第一个是cloneNode(),会返回与调用它的节点一模一样的节点. **cloneNode()方法接收一个布尔值参数,表示是否深复制.在传入true参数时,会进行深复制,即复制节点及其整个子DOM树.**如果传入false,则只会复制调用该方法的节点.

注意: cloneNode()方法不会复制添加到DOM节点的JS属性,比如事件处理程序.这个方法只复制HTML属性,以及可选地复制子节点.

## Document类型

Document类型是JavaScript中表示文档节点的类型.在浏览器中,文档对象document是HTMLDocument的实例,表示整个HTML页面.docuemnt是window对象的属性,因此是一个全局对象. Document类型的节点有以下特征:

nodeType等于9;

nodeName值为 "#document";

nodeValue值为 null;

parentNode 值为 null;

ownerDocument值为null;

1.文档子节点;

documentElement 属性,始终指向HTML页面中的<html>元素 , 可以快捷访问访问子节点.

```js
let html = document.documentElement;
alert(html === document.childNodes[0]);
alert(html === document.firstChild);
```

作为HTMLDocument的实例,document对象还有一个body属性,直接指向<body>元素.

```js
let body = document.body;
```

所有主要浏览器都支持document.documentElement 和 document.body.

2.文档信息:

title: 包含<title>元素中的文本,通常显示在浏览器窗口或标签页的标题栏.通过这个属性可以读写页面的标题,修改后的标题也会反映在浏览器标题栏上.

```js
let originalTitle = document.title;
        document.title = "New page List";
```

URL: 包含当前页面的完整URL(地址栏中的URL),

domain: 包含页面的域名.

referrer: 包含链接到当前页面的那个页面的URL.如果当前页面没有来源,则referrer属性包含空字符串.

在这些属性中,只有domain属性是可以设置的.出于安全考虑,给domain属性设置的值是有限制的. 如果URL包含子域名如p2p.wrox.com , 则可以将domain设置为"www.wrox",不能给这个属性设置URL中不包含的值.

```js
 // 页面来自p2p.wrox.com
       document.domain = 'wrox.com';  //成功!
       document.domain = 'nczonline.net'; //出错!
```

浏览器对domain还有一个限制,即这个属性一旦放松就不能再收紧. 比如,把document.domain设置为"wrox.com"之后,就不能再将其设置回"p2p.wrox.com";

3.定位元素:

 getElementById()方法接收一个参数,即要获取元素的ID,如果找到了则返回这个元素,如果没有找到则返回null. 参数ID必须跟元素在页面中的id属性值完全匹配,包括大小写.

**如果页面中存在多个具有相同ID的元素,则getElementById()返回在文档中出现的第一个元素.**

getElementsByTagName()是另一个常用来获取元素引用的方法.这个方法接收一个参数,即要获取元素的标签名,返回包含零个或多个元素的NodeList.

namedItem(),可以通过标签的name属性取得某一项的引用.

对于name属性的元素,还可以直接使用中括号来获取.

```js
let myImage = images['myImage'];
```

要取得文档中的所有元素,可以给getElementByTagName()传入 *.

getElementsByName()方法会返回具有给定name属性的所有元素.getElementsByName()方法常用于单选按钮.

4.特殊集合:

document.anchors 包含文档中所有带name属性的<a>元素.

document.forms包含文档中所有<form>元素.

document.images包含文档中所有<img>元素.

document.links包含文档中所有带href属性的<a>元素.

6.文档写入:

document对象有一个古老的能力,即向网页输出流中写入内容.

write()简单写入文本,而writeln()还会在字符串末尾追加一个换行符(\n).

7.创建元素

docuemnt.createElement()方法创建新元素.这个方法接收一个参数,即要创建元素的标签名.在HTML文档中,标签名是不区分大小写的.

可以再为其添加属性,添加更多子元素.

```js
div.id = "mNewDiv";
div.className = "box"
```

要把元素添加到文档树,可以使用appendChild(),insertBefore()或replaceChild.

### Text类型

Text节点由Text类型表示,包含按字面解释的纯文本,也可能包含转义后的HTML字符,但不含HTML代码.

Text类型的节点具有以下特征:

nodeType等于3, nodeName值为"#text" , nodeValue值为节点中包含的文本; parentNode值为Element对象. 不包含子节点.

**Text节点中包含的文本可以通过nodeValue属性访问,也可以通过data属性访问,这两个属性包含相同的值**.修改nodeValue或data属性的值,也会在另一个属性反映出来.

appendData(text):向节点末尾添加文本text.

deleteData(offset,count): 从位置offset开始删除count个字符.

insertData(offset,text) 在位置offset插入text.

replaceData(offset,count,text) 用text替换从位置offset到offset + count的文本.

splitText(offset) : 在位置offset将当前文本拆分为两个文本节点.

substringData(offset,count): 提取从位置offset到offset+count的文本.

length: 获取文本节点中包含的字符数量.

1.创建文本节点:

document.createTextNode() : 创建新文本节点,它接收一个参数,即要插入节点的文本.

