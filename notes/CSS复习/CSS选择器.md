## CSS选择器总结

**一.常用的四类选择器(基本)**

1. 元素选择器

   作用: 选中对应元素/标签里面的内容

   语法: 标签名{}

2. id选择器

   作用: 选中对应id属性值的元素

   语法: #id属性值{}

3. 类选择器

   作用:选中对应class属性值的元素

   语法: .class属性值{}

4. 通配符选择器

   作用: 选中页面中所有元素

   语法: *{}

**二.复合选择器**(两种)

1. 交集选择器

   **交集选择器就是在两个标签相交的部分,也就是交集进行修改部分**

   **对于交集选择器,可以与id和class共同使用.**

   作用:选中同时符合多个选择器条件的元素

   语法: 选择器1选择器2 ...{}

   注意: 如果选择器中有元素的选择器,元素选择器必须放在前面.

   eg:

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
      <style>
          p.p1{
            color: red;
          }
          
      </style>
   </head>
   <body>
      <p>我是段落</p>
      <p class="p1">我是段落</p>
      <p class="p2">我是段落</p>
   </body>
   </html>
   ```

   

2. 并集选择器

   **与交集选择器不同,并集选择器是对两个标签同时进行修改样式.**

   **并集选择器同样可以与id和class同时使用.**

   作用:同时选中对应选择器的元素

   语法: 选择器1 , 选择器2, 选择器3 ... {}

   eg:

   ```css
   <!DOCTYPE html>
   <html lang="en">
   <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
      <style>
         h2,p{
            color: red;
         }
      </style>
   </head>
   <body>
      <h2>我是标题</h2>
      <p>我是段落</p>
   </body>
   </html>
   ```

**三.关系选择器**

1.子代选择器(>)

子代选择器主要用于选择某个元素的第一级子元素.例如希望选择只作为h1元素子元素的strong元素,可以这样写: h1>strong

```html
<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta http-equiv="X-UA-Compatible" content="IE=edge">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <title>Document</title>
   <style>
      h1>strong{
         color: red;
         font-size: 20px;
         font-family: "微软雅黑";
      }
   </style>
</head>
<body>
   <h1>这个<strong>知识点</strong>很<strong>重要</strong></h1>
   <h1>一定要学<em><strong>好</strong></em>!</h1>
</body>
</html>
```

第一个h1标签中的<strong>是h1的子元素,第二个h1标签中的<strong>是h1的孙元素,所以只有第一个h1标签应用了样式.

2.兄弟选择器(+,~)

**兄弟选择器用来选择与某元素位于同一个父元素之中,且位于该元素之后的兄弟元素.**分别为临近兄弟选择器和普通兄弟选择器两种.

<1>临近兄弟选择器

该选择器使用加号"+"来链接前后两个选择器.**选择器中的两个元素有同一个父亲,而且第二个元素必须紧跟第一个元素.**

```html
<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta http-equiv="X-UA-Compatible" content="IE=edge">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <title>Document</title>
   <style>
      p+h2{
         color: green;
         font-family: "宋体";
         font-size: 20px;
      }
   </style>
</head>
<body>
   <h2>《次北固山下》</h2>
   <p>客路青山外，行舟绿水前。</p>
   <h2>潮平两岸阔，风正一帆悬。</h2>
   <h2>海日生残夜，江春入旧年。</h2>
   <h2>乡书何处达？归雁洛阳边。</h2>
</body>
</html>
```

只有紧跟p元素和h2元素应用了代码中设定的样式.

2.普通兄弟选择器

普通兄弟选择器使用"~"来链接前后两个选择权.**选择器中的两个元素有同一个父亲,但第二个元素不必紧跟第一个元素.**

```html
<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta http-equiv="X-UA-Compatible" content="IE=edge">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <title>Document</title>
   <style>
      p~h2{
         color: green;
         font-family: "宋体";
         font-size: 20px;
      }
   </style>
</head>
<body>
   <h2>《次北固山下》</h2>
   <p>客路青山外，行舟绿水前。</p>
   <h2>潮平两岸阔，风正一帆悬。</h2>
   <h2>海日生残夜，江春入旧年。</h2>
   <h2>乡书何处达？归雁洛阳边。</h2>
</body>
</html>
```

p元素的所有兄弟元素h2都应用了代码中所设定的样式.

3.祖先后代选择器

```html
...
        .c span{
            color: skyblue;
        }
...
<body>
    <div class="c">
        <span>c的子代</span>
        <div>
            <span>c的后代</span>
        </div>
    </div>
    <span>c的兄弟</span>
</body>
```

后代选择器语法

选择器1 选择器2 选择器3 {}

选择器之间用空格隔开.

四.属性选择器

属性选择器可以为拥有指定属性的HTML元素设置样式;属性选择器通过[]来定义,[]内部为元素的属性,属性选择器的权重高于标签选择器.

el[attr=val]选择attr为val的el元素

```html
<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta http-equiv="X-UA-Compatible" content="IE=edge">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <title>Document</title>
   <style>
     div p[class="blue"]{
      color: red;
     }
   </style>
</head>
<body>
   <div>
      <p class="blue">p 标签</p>
      <p class="blue red">p 标签</p>
      <p class="blue">p 标签</p>
   </div>
</body>
</html>
```

el[attr *= val] 只要el元素的attr属性值里包含有val就能被选择.

```html
<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta http-equiv="X-UA-Compatible" content="IE=edge">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <title>Document</title>
   <style>
     .box a[class *= "download"]{
      color: red;
     }
   </style>
</head>
<body>
   <div class="box">
      <a href="#" class="download">下载</a>
      <a href="#" class="musicdownload">下载</a>
      <a href="#" class="downloadmovie">下载</a>
      <a href="#" class="image-download">下载</a>
      <a href="#" class="flast download">下载</a>
   </div>   
</body>
</html>
```

el[attr ~= val]el元素中的arr属性的属性值val单独存在或存在的值与其他属性值之间用空格隔开就能被选择.

```css
.box a[class ~= "download"] {
	color: yellow;
}
```

el[attr != val] el元素中attr属性的属性值单独存在或属性值之间用"-"隔开且属性值val在"-"之前

```css
.box a[class |= "download"] {
	color: skyblue;
	font-size: 20px;
}
```

```html
<div class="box">
	<a href="#" class="download">下载2</a>
	<a href="#" class="music download">下载2</a>
	<a href="#" class="image-download">下载2</a>
	<a href="#" class="download-image">下载2</a>
	<a href="#" class="downloadmovie">下载2</a>
</div>
```

el[attr ^= val] el元素的attr属性值里含有val且val在最前面才能被选择.

```css
.box a[class ^= "download"] {
	color: pink;
}
```

el[attr $= val] el元素的attr属性值里含有val且在最后面

```css
.box a[class $= "download"]{
	color: orange;
}
```

五.伪类选择器

用来描述一个元素的特殊状态!比如第一个元素,某个元素的子元素,鼠标点击的元素.

我们伪类选择器有几种,我们来看看第一种

**静态伪类:只能用于超链接的样式**

 :link 超链接点击之前

 :visited  超链接被访问之后

以上两种方式,只用于超链接.

因为伪类保护隐私,**我们的:visited只能改变颜色属性**,link可以改变.

动态伪类: 针对所有标签都适用的样式.

- **:hover** “悬停”：鼠标放到标签上的时候
- **:active** “激活”： 鼠标点击标签，但是不松手时。
- **:focus** 是某个标签获得焦点时的样式（比如某个输入框获得焦点）

```css
:first-child   第一个子元素

:last-child    最后一个子元素

:nth-child()   选中第n个元素

关于:nth-child()的特殊值

        n   第n个   n的范围0到正无穷（全选）

        even或2n    选中偶数位的元素

        odd或2n+1   选中奇数位得到元素

上面的都是在所有元素中选择

下面都是在相同元素中选择

:first-of-type  第一个子元素

:last-of-type   最后一个子元素

:nth-of-type()    选中第n个元素

```

六,伪元素选择器

同伪类一样,伪元素也是不存在的元素,表示元素的特殊状态.

```css
常见的几个伪元素：

::first-letter    表示第一个字母

::first-line       表示第一行

::selection     表示选中的元素

::before         元素开始的位置前

::after            元素结束的位置后

befor和after必须配合contend一起使用(before,after所写的内容无法选中且永远在最前和最后)
```



选择器优先级

第一等：代表内联样式，如: style=””，权值为1000。
第二等：代表ID选择器，如：#content，权值为0100。
第三等：代表类，伪类和属性选择器，如.content，权值为0010。
第四等：代表类型选择器和伪元素选择器，如div p，权值为0001。
通配符、子选择器、相邻选择器等的。如*、>、+,权值为0000。
继承的样式没有权值。

!import是一个例外  无穷大