14.1.2 Document类型:

在浏览器中,文档对象document是HTMLDoucment的实例,表示整个HTML页面.document是window对象的属性,因此是一个全局对象.Document类型的节点有以下特征:

nodeType等于9; nodeName值为"#document";nodeValue值为null;parentNode值为null;ownerDocument值为null;

Document类型可以表示HTML页面或其他XML文档,但最常用的还是通过HTMLDocument的实例取得document对象.document对象可用于获取关于页面信息以及操纵其外观和底层结构.

1.文档子节点:

documentElement属性,始终指向HTML页面的<html>元素.虽然document.childNodes中始终有<html>元素,但使用documentElement属性可以更快更直接的访问该元素.

body属性:直接指向<body>元素.

所有主流浏览器都支持document.documentElement和document.body;

2.文档信息:

title属性:包含<title>元素的文本,通常显示在浏览器窗口或标签也的标题栏.通过这个属性可以读写页面的标题,修改后的标题也会反映在浏览器标题栏上.不过,title属性并不会改变<title>元素.

```javascript
//读取文档标签
         let originalTitle = document.title;
        //修改文档标签
         document.title = '郭勇我日你先人!';
```

URL:包含当前页面完整的URL(地址栏中的URL).

domain包含页面的域名.

referrer包含链接到当前页面的那个页面的URL.如果当前页面没有来源,则referrer属性包含空字符串.

所有这些信息都可以在请求的HTTP头部信息中获取,只是在javascript中通过这几个属性暴露出来而已.

```js
let url = document.URL;
        let domain = document.domain;
        let referrer = document.referrer;
        console.log(url);
        console.log(domain);
        console.log(referrer);
```

在这些属性中,只有domain属性是可以设置的.出于安全考虑,给domain属性设置的值是有限制的.如果URL包含子域名如p2p.worx.com,则可以将domain设置为worx.com.不能给这个属性设置URL中不包含的值.

当页面中包含来自某个子域的窗口(<frame>)或内嵌窗口(<iframe>)时,设置document.domain是有用的.因为跨源通信存在安全隐患,所以在不同子域的页面见无法通过js通信.此时,在每个页面上把document.domain设置为相同的值,这些页面就可以访问对方的JS对象了.

浏览器对domain属性还有一个限制,即这个属性一旦放松就不能再收紧.比如,把document.domain设置为"wrox.com"之后,就不能再将其设置回"p2p.wrox.com",后者会导致错误.

3.定位元素:

getElementById()方法接收一个参数,即要获取元素的ID,如果找到了则返回这个元素,如果没有找到则返回null.参数ID必须跟元素在页面中的id属性值完全匹配,包括大小写.

如果页面中存在多个具有相同ID的元素,则getElementById()返回在文档中出现的第一个元素.

getElementByTagName()是另一个常用来获取元素引用的方法.这个方法接收一个参数,即要获取元素名的签名,返回包含零个或多个元素的NodeList.在HTML文档中,这个方法返回一个HTMLCollection对象.

HTMLCollection对象还有一个额外的方法namedItem(),可通过标签的name属性取得某一项的引用.

```js
<img src="" name="myImage" alt="">
    <img src="" alt="">
    <img src="" alt="">
    <img src="" alt="">
    <script>
        let images = document.getElementsByTagName('img');
        console.log(images);
        let myImage = images.namedItem('myImage');
        console.log(myImage)
    </script>
```

这样,HTMLCollection就提供了除索引之外的另一种获取列表项的方式,从而为取得元素提供了便利.

对于name属性的元素,还可以直接使用中括号来获取.

```js
let myImage = images.namedItem["myImage"];
```

对于HTMLCollection对象而言,中括号既可以接收数值索引,也可以接收字符串索引.而在后台,数值索引会调用item(),字符串索引会调用namedItem().

要取得文档中所有元素,可以给getElementByTagName()传入*.在js和CSS中,*一般被认为是匹配一切的字符串.

getElementsByName():这个方法会返回具有给定name属性的所有元素.getElementsByName()方法最常用于单选按钮,因为同一字段的单选按钮必须具有相同的name属性才能确保把正确的值发送给服务器.

```js
<ul>
       <li>
           <input type="radio" value="red" name="color" id="colorRed">
           <label for="colorRed">red</label>
       </li>
       <li>
        <input type="radio" value="green" name="color" id="colorGreen">
        <label for="colorGreen">Green</label>
    </li>
    <li>
        <input type="radio" value="blue" name="color" id="colorBlue">
        <label for="colorBlue">Blue</label>
    </li>
      <button id="submit">点击提交</button>
   </ul>

    <script>
        let btn  = document.getElementById('submit');
        btn.onclick=function()
        {
            let radios = document.getElementsByName('color');
         console.log(radios);
        }
    </script>
```

4.特殊集合:

document.anchors包含文档中所有带name属性的<a>元素.

document.forms包含文档中所有<img>元素(与document.getElementsByTagName("img")返回结果相同);

document.links包含文档中所有带href属性的<a>元素.

5.DOM兼容性检测:

document.implement属性是一个对象,其中提供了与浏览器DOM实现相关的信息和能力.DOM Level 1在document.implement上只定义了一个方法,即hasFeature().这个方法接收两个参数:特性名称和DOM版本.如果浏览器支持指定的特性和版本,则hasFeature()方法返回true.

由于实现不一致,因此其返回值并不可靠,目前以及废弃,无论检测什么都返回true.

6.文档写入:

write()和writeln()方法都接收一个字符串参数,可以将这个字符串写入网页中.write()简单地写入文本,而writen()还会在字符串末尾追加一个换行符号("\n");这意味着会创建一个DOM元素,以后也可以访问>

write()和writlen()方法经常用于动态包含外部资源.

open()和close()方法分别用于打开和关闭网页输出流.在调用write()和writlen()时,这两个方法都不是必需的.

14.1.3Element类型:

Elemnet表示XML或HTML元素,对外暴露出访问元素标签名,子节点和属性的能力.Element类型的节点具有以下特性:

nodeType等于1; nodeName值为元素的标签名; nodeValue值为null; parentNode值为Document或Element对象;

可以通过nodeName或tagName属性来获取元素的签名.这两个属性返回同样的值.

1.HTML元素:

所有HTML元素都通过HTMLElement类型表示,包括其直接和间接实例.另外,HTMLElement直接继承Element并增加了一些属性.

id:元素在文档中的唯一标识符号;

title,包含元素的额外信息,通常以提示条形式展示;

lang,元素内容的语言代码;

dir,语言的书写方向;

className,相当于class属性,用于指定元素的CSS类.

2.取得属性:

getAttribute()属性名与它们实际的属性名是一样的.如果给定的属性不存在,则getAttribute()返回null;

getAttribute()方法也能取得不是HTML语言正式属性的自定义属性的值.

3.设置属性:

setAttribute()m这个方法接收两个参数:要设置的属性名和属性的值.如果属性已近存在,则setAttribute()会以指定的值替换原来的值;如果属性不存在,则setAttribute()会以指定的值创建该属性.

最后一个方法removeAttribute()用于从元素中删除属性.这样不单单是清楚属性的值,而是会把整个属性从元素中去掉.

4.attributes属性:

element类型是唯一使用attributes的DOM节点类型.attributes属性包含一个NamedNodeMap实例,是一个类似NodeList的"实时"集合.元素的每个属性表示为Attr节点,并保存在这个NamedNodeMap实例,NamedNodeMap对象包含下列方法:

getNamedItem(name),返回nodeName属性等于name的节点.

removeNamedItem(name),删除nodeName属性等于name的节点:

setNamedItem(node),向列表中添加node节点,以其nodeName为索引;

item(pos),返回索引位置pos处的节点.

attributes属性中的每个节点的nodeName是对应属性的名字,nodeValue是属性的值.

```js
let oldAttr = Element.attributes.getNamedItem('id').nodeValue;
```

5.创建元素:

可以使用document.createElement()方法创建新元素.这个方法接收一个参数,即要创建元素的标签名.

```js
 let div = document.createElement('div');
        document.body.appendChild(div);
```

要把元素添加到文档树,可以使用appendChild(),insertBefore()或replaceChild().元素被添加到文档树之后,浏览器会立即将其渲染出来.

6.元素后代:

childNodes属性包含元素所有的子节点,这些子节点可能是其他元素,文本节点,注释或处理指令.

14.1.4 Text类型:

Text节点由Text类型表示,包含按字面解释的纯文本,也可能包含转义后的HTML字符,但不含HTML代码.

Text类型的节点具有以下特征:

nodeType等于3; nodeName值为'#text';nodeValue值为节点中包含的文本; parentNode值为Element对象;不支持子节点.

Text节点中包含的文本可以通过nodeValue属性访问,也可以通过data属性访问,这两个属性包含相同的值.修改nodeValue或data的值,也会在另一个属性反映出来.

appendData(text):向节点末尾添加文本text;

deleteData(offset,count),从位置offset开始删除count个字符.

insertData(offset,text),在位置offset插入text;

replace(offset,count,text),用text替换从位置offset到offset+count的文本;

splitText(offset),在位置offset将当前文本节点拆分为两个文本节点;

substringData(offset,count),提取从位置offset到offset+count的文本.

length属性获取文本节点中包含的字符数量.这个值等于nodeValue.length和data.length.

1.创建文本节点:

document.createTextNode()可以用来创建新文本节点,它接收一个参数,即要插入节点的文本.

跟设置已有文本节点的值一样,这些要插入的文本也会应用HTML和XML编码.

```js
let textNode = document.createTextNode("<strong>Hello </strong>World!");
      let element = document.createElement('div');
      element.appendChild(textNode);
      document.body.appendChild(element);
```



2.规范化文本节点:normalize():这个方法可以合并相邻的文本节点.是Node类型中定义的.在包含两个或多个相邻文本节点的父节点上调用normalize()时,所有同胞文本节点被合并为一个文本节点,这个文本节点的nodeValue就等于之前所有同胞节点nodeValue拼接在一起得到的字符串.

14.1.5 Coment类型:

DOM中的注释通过Comment类型表示.Comment类型的节点具有以下特诊:

nodeType等于8; nodeName值为"#comment"; nodeValue值为注释的内容; parentNode值为Document或Element对象.不支持子节点.

可以使用document.createComment()方法创建注释节点,参数为注释文本.