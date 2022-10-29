15.1 Selectors API:

Selectors API是W3C推荐标准,规定了浏览器原生支持的CSS查询API.支持这一特性的所有Javascript库都会实现一个基本的CSS解析器,然后使用已有的DOM方法搜索文档并匹配目标节点.虽然库开发者在不断改进其性能,但javascript代码能做到的毕竟有限.通过浏览器原生支持这个API,解析和遍历DOM树可以通过底层编译语言实现,性能也有了数量级的提升.

15.1.1 querySelector():

querySelector()方法接收CSS选择符参数,返回匹配该模式的第一个后代元素,如果没有匹配项则返回null.

```js
//取得<body>元素
        let body = document.querySelector('body');
        //取得ID为"myDiv"的元素
        let myDiv = document.querySelector("#myDiv");
        //取得类名为"selected"的第一个元素
        let selected = document.querySelector('.selected');
        //取得类名为"button"的图片:
        let img = document.querySelector('img.button');
```

15.1.2 querySelectorAll():

querySelectorAll()方法跟querySelcetor()一样,也接收一个用于查询的参数,但它会返回所有匹配的节点,而不止一个.这个方法返回的是一个NodeList的静态实例.该实例一个属性和方法都不缺,但它是一个静态的"快照",而非"实时"的查询.这样的底层实现避免了使用NodeList对象可能造成的性能问题.

返回的NodeList对象可以通过for-of循环,item()方法或中括号语法取得个别元素.

```js
let strongElemets = document.querySelectorAll("p strong");

        for(let strong of strongElemets)
        {
            strong.className = 'important';
        }

        for(let i=0;i<strongElemets.length;++i)
        {
            strongElemets.item(i).className = 'important';
        }

        for(let i=0;i<strongElemets.length;++i)
        {
            strongElemets[i].className = 'important';
        }
```

与querySelector()方法一样,如果选择符有语法错误或碰到不支持的选择符,则querySelector-All()方法会抛出错误.

15.1.3 matches():

matches()方法接收一个CSS选择符参数,如果元素匹配则该选择符返回true,否则返回flase.

使用这个方法可以方便的检测某个元素会不会被querySelector()或querySelectorAll()方法返回.

