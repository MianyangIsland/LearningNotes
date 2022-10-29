# DOM扩展

### querySelector()

querySelector()方法接收CSS选择符参数,返回匹配该模式的第一个后代元素,如果没有匹配项则返回null.

在Document上使用querySelector()方法时,会从文档元素开始搜索;在Element上使用querySelector()方法时,则只会从当前元素的后代中查询.

### querySelectorAll()

querySelectorAll()方法跟querySelector()一样,也接收一个用于查询的参数,但它会返回所有匹配的节点,而不止一个.

这个方法返回的是一个NodeList的静态实例.

注意,querySelectorAll()返回的NodeList实例一个属性和方法都不缺,但它是一个静态的"快照",而非"实时"查询.这样的底层实现避免了使用Nodelist对象可能造成的性能问题.

### matches()

matches()方法接收一个CSS选择符参数,如果元素匹配则该选择符返回true,否则返回false.

使用这个方法可以方便的检测某个元素会不会被querySelector()或querySelectorAll()方法返回.

## 元素遍历

childElementCount: 返回子元素数量.

firstElementChild : 指向第一个Element类型的子元素.

lastElementChild , 指向最后一个Element类型的子元素.

previousElementSibling : 指向前一个Element类型的同胞元素.

nextElementSibling: 指向最后一个Element类型的同胞元素.

## HTML 5 :

### CSS类扩展

1.getElementsByClassName(): HTML5新增的最受欢迎的一个方法,暴露在docuemnt对象和所有HTML元素上.

getElementsByClassName()方法接收一个参数,即包含一个或多个类名的字符串,返回类名中包含相应类的元素的NodeList. 如果提供了多个类名,则顺序无关紧要.

这个方法只会返回以调用它的对象为根元素的子树中所有匹配的元素.

### classList属性

要操作类名,可以通过className属性实现添加,删除和替换.但className是一个字符串,所以每次操作之后都需要重新设置这个值才能生效,即使只改动了部分字符串也一样.

classList是一个新的集合类型DOMTokeList的实例.与其他DOM集合类型一样,DOMTokenList也有length属性表示自己包含多少项,也可以通过item()或中括号取得个别的元素.

add(value): 向类名列表中添加指定的字符串值value.如果这个值已经存在则什么也不做.

contains(value): 返回布尔值,表示给定的value是否存在.

remove(value) : 从类名列表中删除指定的字符串值value.

toggle(value) : 如果类名列表中已经存在指定值value,则删除;如果不存在,则添加.

### 焦点管理

document.activeElement ,始终包含当前拥有焦点的DOM元素.页面加载时,可以通过用户输入让某个元素自动获得焦点.

默认情况下,document.activeElement在页面刚加载之后会设置为document.body. 而在页面完全加载之前,document.activeElement的值为null.

其次是document.hasFoucs()方法,该方法返回布尔值,表示文档是否拥有焦点:

第一个方法可以用来查询文档,确定哪个元素拥有焦点,第二个方法可以查询文档是否获得了焦点.

## HTMLDocument扩展:

1.readyState 属性:

document.readyState属性可能有两个值:

loading : 表示文档正在加载.

complete , 表示文档加载完成

实际开发中,最好是把document.readyState 当成一个指示器,以判断文档是否加载完毕. 在这个属性得到广泛支持以前,通常要依赖onload事件处理程序设置一个标记,表示文档加载完了.

```js
if(document.readyState == 'complete'){
    //执行操作
}
```

## 插入标记

1.innerHTML属性:

在读取innerHTML属性时,会返回元素后代的HTML字符串,包括元素,注释和文本节点.而在写入innerHTML时,则会根据提供的字符串值以新的DOM子树替代元素中包含的所有节点.