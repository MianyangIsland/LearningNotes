第十四章:DOM

文档对象模型是HTML和XML文档的编程接口.DOM表示由多层节点构成的文档,通过它开发者可以添加,删除和修改页面的各个部分.

14.1 节点层级:

document节点表示每个文档的根节点.在这里,根节点的唯一子节点是<html>元素,我们称之为文档元素.文档元素是文档最外层的元素,所有其他元素都存在于这个元素之内.每个文档只能有一个文档元素.在HTML页面中,文档元素始终是<html>元素.在XML文档中,则没有这样预定义的元素.

14.1.1Node类型:

DOMLevel1描述了名为Node的接口,这个接口是所有DOM节点都必须实现的.Node接口在Javascript中被实现为Node类型,在除IE之外的所有浏览器都可以直接访问这个类型.在Javascript中,所有节点类型都继承Node类型,因此所有类型都共享相同的基本属性和方法.

每个节点都有nodeType属性,表示该节点的类型.节点类型由定义在Node类型上的12个数值常量表示.

1.nodeName与nodeValue:保存着有关节点的信息.

2.节点关系:每个节点都有一个childNodes属性,其中包含一个NodeList实例.NodeList是一个类数组对象,用于存储可以按位置存取的有序节点.注意:NodeList并不是Array的实例,但可以使用中括号访问它的值,而且它也有length属性.使用ES6的Array.from()静态方法,可以把NodeList对象转换为数组.

```javascript
let arrayOfNodes = Array.from(someNode.childNodes);
```

每个节点都有一个parentNode属性,指向其DOM树中的父元素.

父节点和它的第一个以及最后一个节点也有专门属性:firstChild和lastChild分别指向childNodes中的第一个和最后一个子节点.如果只有一个一个子节点,则firstChild和lastChild指向同一个节点.如果没有子节点,则firstChild和lastChild的值都是null.

hasChildNodes();这个方法如果返回true则说明节点有一个或多个子节点.

3.操纵节点:

appendChild(),用于在childNodes列表末尾添加节点.添加新节点会更新相关的指针关系,包括父节点和之前的最后一个子节点.appendChild()方法会返回新添加的节点.

```javascript
let returnedNode = someNode.appendChild(newNode);
        alert(returnedNode == newNode);//true
        alert(someNode.lastchild == newNode);//true
```

如果把文档中已近存在的节点传给appendChild(),则这个节点会从之前的位置被转移到新位置.即使DOM通过各种关系指针维系,一个节点也不会在文档中同时出现两个或多个地方.

insertBefore()方法.这个方法接收两个参数:要出入的节点和参照节点.要插入的节点就会变成参照节点的前一个同胞节点,并被返回.如果参照节点为null,则insertBefore()与appendChild()效果相同.

appendChild()方法和insertBefore()在插入节点时不会删除任何已有节点.

replaceChild()方法接收两个参数:要插入的节点和要替换的节点.要替换的节点会被返回并从文档中完全删除,要插入的节点会取而代之.

removeChild():移除节点.这个方法接收一个参数,即要移除的节点.被移除的节点会被返回.

上面介绍的4个方法都用于操纵某个节点的子元素,也就是说使用它们之前必须先取得父节点.并非所有节点类型都有子节点,如果在不支持子节点的节点上调用这些方法,则会导致抛出错误.

4.其他方法:

cloneNode(),会返回与调用它的节点一模一样的节点.cloneNode()方法接收一个布尔值参数,表示是否深复刻.在传入true参数时,会进行深复刻,即复刻节点及其整个子DOM树.如果传入false,则只会复刻调用该方法的节点.复刻返回的节点属于文档所有,但尚未指定父节点,所以可称为孤儿节点.可以通过appendChild(),insertBefore()或replaceChild()方法把孤儿节点添加到文档中.

```javascript
<ul id="ul">
        <li>item 1</li>
        <li>item 2</li>
        <li>item 3</li>
    </ul>
let myList = document.getElementById('ul');
        let deepList = myList.cloneNode(true);
        console.log(deepList.childNodes.length);//7

        let shallowList =  myList.cloneNode(false);
        console.log(shallowList.childNodes.length);//0
```