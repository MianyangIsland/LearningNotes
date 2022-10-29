# CSS部分

1.继承相关:

css的继承: 就是给父级设置一些属性,子级继承了父级的该属性.官方解释,继承是一种规则,它允许样式不仅应用于特定的html标签元素,而且应用于其后代元素.

**无继承性的属性**

1、display：规定元素应该生成的框的类型

2、文本属性：

vertical-align：垂直文本对齐

text-decoration：规定添加到文本的装饰

text-shadow：文本阴影效果

white-space：空白符的处理

unicode-bidi：设置文本的方向

3、盒子模型的属性：width、height、margin 、margin-top、margin-right、margin-bottom、margin-left、border、 border-style、border-top-style、border-right-style、border-bottom-style、border-left-style、border-width、border-top-width、border-right-right、border-bottom-width、border-left-width、border-color、border-top-color、border-right-color、border-bottom-color、border-left-color、border-top、border-right、border-bottom、border-left、padding、padding-top、padding-right、padding-bottom、padding-left

4、背景属性：background、background-color、background-image、background-repeat、background-position、background-attachment

5、定位属性：float、clear、position、top、right、bottom、left、min-width、min-height、max-width、max-height、overflow、clip、z-index

6、生成内容属性：content、counter-reset、counter-increment

7、轮廓样式属性：outline-style、outline-width、outline-color、outline

8、页面样式属性：size、page-break-before、page-break-after

9、声音样式属性：pause-before、pause-after、pause、cue-before、cue-after、cue、play-during

**有继承性的属性**

1、字体系列属性

font：组合字体

font-family：规定元素的字体系列

font-weight：设置字体的粗细

font-size：设置字体的尺寸

font-style：定义字体的风格

font-variant：设置小型大写字母的字体显示文本，这意味着所有的小写字母均会被转换为大写，但是所有使用小型大写 字体的字母与其余文本相比，其字体尺寸更小。

font-stretch：对当前的 font-family 进行伸缩变形。所有主流浏览器都不支持。

font-size-adjust：为某个元素规定一个 aspect 值，这样就可以保持首选字体的 x-height。

2、文本系列属性

text-indent：文本缩进

text-align：文本水平对齐

line-height：行高

word-spacing：增加或减少单词间的空白（即字间隔）

letter-spacing：增加或减少字符间的空白（字符间距）

text-transform：控制文本大小写

direction：规定文本的书写方向

color：文本颜色 a元素除外

3、元素可见性：visibility

4、表格布局属性：caption-side、border-collapse、border-spacing、empty-cells、table-layout

5、列表布局属性：list-style-type、list-style-image、list-style-position、list-style

6、生成内容属性：quotes

7、光标属性：cursor

8、页面样式属性：page、page-break-inside、windows、orphans

9、声音样式属性：speak、speak-punctuation、speak-numeral、speak-header、speech-rate、volume、voice-family、 pitch、pitch-range、stress、richness、、azimuth、elevation

所有元素可以继承的属性

1. 元素可见性: visiblity
2. 光标属性:cursor

内联元素可以继承的属性

1. 字体系列属性
2. 处理text-indext,text-align之外的文本系列属性.

块级元素可以继承的属性.

1.text-indet , text-align.

**行内元素于块级元素什么区别,然后怎么相互转换**

块级元素:

1. 总是从新的一行开始,即各个块级元素独占一行,默认垂直向下排列;
2. 宽度,高度,margin及padding都是可控的,设置有效,有边距效果;
3. 宽度没有设置时,默认为100%;
4. 块级元素中可以包含块级元素和行内元素.

行内元素:

1. 和其他元素都在一行,即行内元素和其他行内元素都会在一条水平线上排列;
2. 宽度,高度是不可控的,设置无效,由内容决定.
3. 根据标签语义化的理解,行内元素最好只包含行内元素,不包含块级元素.

转换: 

HTML可以将元素分为行内元素,块级元素和行内块元素三种.

使用display属性能够将三者任意转换:

 (1)display:inline ; 转换为行内元素;

 (2)display: block ; 转换为块状元素;

 (3)display: inline-block ; 转换为行内块元素.

**块元素那些属性可以继承?**

text-indent , text-align , visiblity, cursor.

**盒模型**

 1.概念:

  CSS盒模型本质上是一个盒子,封装周围的HTML元素,它包括:外边距(margin),边框(border),内边距(padding),实际内容(content)四个属性.

**样式优先级**

样式类型分为三类:

1.行间

```html
<h1 style="font-size:12px;color:#000;">我的行间CSS样式。</h1>
```

2.内联

```html
<style type="text/css">
   h1{font-size:12px;
      color:#000;
      }
</style>
```

3.外部

```html
<link rel="stylesheet" href="css/style.css">
```

选择器类型

 ID : #id    class : .class   标签: p   通用 *    属性 [type='text'  ] 伪类  :first-line  子选择器,相邻选择器

权重计算规则

第一等：代表内联样式，如: style=””，权值为1000。
第二等：代表ID选择器，如：#content，权值为0100。
第三等：代表类，伪类和属性选择器，如.content，权值为0010。
第四等：代表类型选择器和伪元素选择器，如div p，权值为0001。
通配符、子选择器、相邻选择器等的。如*、>、+,权值为0000。
继承的样式没有权值。

比较规则

遵循如下法则：

- 选择器都有一个权值，权值越大越优先；
- 当权值相等时，后出现的样式表设置要优于先出现的样式表设置；
- 创作者的规则高于浏览者：即网页编写者设置的 CSS 样式的优先权高于浏览器所设置的样式；
- 继承的 CSS 样式不如后来指定的 CSS 样式；
- 在同一组属性设置中标有!important规则的优先级最大
- 通配符、子选择器、相邻选择器等的。虽然权值为0000，但是也比继承的样式优先。

! important

! important的作用是提升优先级,换句话说,加了这句样式的优先级是最高的(比内联样式的优先级还高).

盒子塌陷是什么?

本应该在父盒子内部的元素跑到了外部.

解决方法:

（1）最简单，直接，粗暴的方法就是盒子大小写死，给每个盒子设**定固定的width和height**，直到合适为止，这样的好处是简单方便，兼容性好，适合只改动少量内容不涉及盒子排布的版面。缺点是非自适应，浏览器的窗口大小直接影响用户体验。 

（2）给外部的父盒子也添加浮动，让其也脱离标准文档流，这种方法方便，但是对页面的布局不是很友好，不易维护。 

（3）给父盒子添加overflow属性。

overflow:auto; 有可能出现滚动条，影响美观。

overflow:hidden; 可能会带来内容不可见的问题。

(4)父盒子里最下方引入清除浮动快.最简单的有:

```css
    <br style="clear:both;"/>
```

(5)用after伪元素清除浮动

给外部盒子的after伪元素设置clear属性,再隐藏它.

这其实是对空盒子方案改进,一种纯CSS的解决方案,不再引入冗余元素.

```css
.clearfix {*zoom: 1;}

.clearfix:before,.clearfix:after {

display: table;

line-height: 0;

content: "";

}

.clearfix:after {clear: both;}
```

(6) 父盒子添加border

(7)给父盒子设置padding-top.

**为什么会出现盒子塌陷?**

当父元素没设置足够大小时,而子元素设置了浮动的属性,子元素就会跳出父元素的边界(脱离文档流),尤其是当父元素的高度为auto时,而父元素中又没有其他非浮动的可见元素时,父盒子的高度就会塌陷为零,我们称这是CSS高度塌陷.

**css伪类与为元素区别**

1.伪类

其核心就是用来选择DOM树之外的信息,不能够被普通选择器选择的文档之外的元素,用来添加一些选择器的特殊效果.

比如:hover :active :visited :link : firsted-child :focus : lang等.

由于状态的变化是非静态的,所以元素达到一个特定状态时,它可能得到一个伪类的样式; 当状态改变时,它又会失去这个样式.

2.伪元素

DOM树没有定义的虚拟元素, 核心就是需要创建通常不存在与文档中的元素 , ⽐如::before ::after 它选择的是元素指定内容，表示选择元素内容的之前内容或之后内容。伪元素控制的内容和元素是没有差别的，但是它本身只是基于元素的抽象，并不存在于⽂档中，所以称为伪元素。⽤于将特殊的效果添加到某些选择器.

3.伪类与为元素的区别:

伪类和伪元素都是用来表示文档树以外的"元素".

伪类和伪元素分别用单冒号和双冒号表示.

伪类和伪元素的区别，关键点在于如果没有伪元素(或伪类)，是否需要添加元素才能达到效果,如果是则伪元素,反之则是伪类.

伪类和为元素都步出现在源文件和DOM树中.也就是说在html源文件中看不到伪类和伪元素.

伪类其实基于普通DOM元素而产生的不同状态,它是DOM元素的某一特征>

伪元素能够创建在DOM树中不存在的抽象对象,而且这些抽象对象可以访问到.

#### min-width/max-width 和 min-height/max-height 属性间的覆盖规则？

1. max-wdith会覆盖width,即使width是行内样式或者设置了!import
2. min-width会覆盖max-width,此规则发生在min-width和max-width冲突的时候.

## 布局

1.未知高度元素垂直居中,垂直居中的实现方式有那些?

1. 绝对定位+css3 transform:translate(-50%,-50%);

   ```js
   .wrap{
     position:relative;
   }
   .child{
     position: absolute;
     top:50%;
     left:50%;
     -webkit-transform:translate(-50%,-50%);
   ```

2. css3的flex布局

   ```js
   .wrap{
     display:flex;
     justify-content:center;
   }
   .child{
     align-self:center;
   }
   ```

2.实现图片垂直居中

1. 使用flex实现图片垂直居中

   利用: display:flex ; align-item:center 实现垂直居中.

   ```html
   <div class="flexbox">
       <img src="1.jpg" alt=""></div>
       
       body{ background:#999}
   .flexbox{width: 300px;height: 250px;background:#fff;display: flex;align-items: center}
   .flexbox img{width: 100px;height: 100px;align-items: center;}
   ```

2. 利用Display :table ;实现img图片垂直居中.

   给最外层的div设置display属性为table;img的父元素div设置display:table-cell,vertical-align:middle;如果也想实现水平居中,可以给最外层的div元素添加text-align:center属性.

3. 用绝对定位实现垂直居中.

   1.给img的父元素添加相对定位属性(position:relative),同时,要给子元素也就是图片img元素添加绝对定位属性(position:absolute).

   2.将图片元素的top属性设置为50%.

   3.给img元素设置一个负的margin-top值,这个值为你想要实现垂直居中的元素高度的一半,如果不确定元素的高度,可以不使用margin-top,而是使用transform:translateY(-50%);属性。

   记住：如果你想要同时实现水平居中，那么你可以用实现垂直居中的一样的技巧来实现。

3.文本元素如何居中?

CSS设置文字水平居中

在CSS中可以使用text-align属性来设置文字水平居中。该属性规定元素中的文本的水平对齐方式，通过使用center值设置文本居中。

text-align是一个基本的属性，它会影响一个元素中的文本行互相间的对齐方式。值left、right和center会导致元素中的文本分别左对齐、右对齐和居中，想要使文本居中，直接使用center即可。

该属性设置文本和img标签等一些内联对象（或与之类似的元素）的居中。

该属性有如下几个特点：

1）text-align的center应用在一个容器上，它只针对容器里面的文字以及容器里面的display为inline或者inline-block的容器，如果里面的容器display为block，则里面的容器的内容不会居中。

2）text-align具有向下传递性，会不断地向子元素传递。如果设置一个div，则其子div中的内容也会居中。

CSS设置字体垂直居中

   单行文字垂直居中

   对于单行文本，我们只需要将文本行高(line-height属性)和所在区域高度(height)设置一致就可以了.

多行文本垂直居中

说明：多行文本垂直居中分为两种情况，一个是父级元素高度不固定，随着内容变化；另一个是父级元素高度固定。

父级元素高度不固定

父级高度不固定的时，高度只能通过内部文本来撑开。所以，我们可以通过设置内填充（padding）的值来使文本看起来垂直居中，只需设置padding-top和padding-bottom的值相等：

父级元素高度固定

使用vertical-align:middle +display:table-cell 使文字垂直居中

```html
  <!DOCTYPE html>
   <html>
       <head>
           <meta charset="UTF-8">
           <title>css 垂直居中</title>
           <style>
               .box {
                   width: 300px;
                   height: 300px;
                   background: paleturquoise;
                   vertical-align:middle;
                   display:table-cell;
               }
           </style>

       </head>

       <body>

           <div class="box">css 垂直居中了--文本文字,文本文字,文本文字,文本文字,文本文字,文本文字。</div>

       </body>

   </html>
```



说明：vertical-align:middle +display:table-cell能够使单行文字、多行文字都居中。但是因为 table-cell 是 inline 类型，所以会导致原来的块级元素每个 div 一行移动到了同一行。如果需要分列两行，需要在外面额外添加容器对位置进行控制。 

#### 用flex实现九宫格讲思路

利用了padding-top和flex-warp:wrap , 当设置background-color时，是包括盒子模型中的content和padding的，但是为什么不设置height呢？因为父元素没有高度，所以定义height:30%是没有用的，且若想每个block都为正方形，最好的方式就是设置padding-top/padding-bottom：a%，因为此时的百分比是父元素宽度的百分比，而width也为父元素宽度的百分比，所以block可以成为正方形。 

```html
<!DOCTYPE html>
<html>
<style>
.block {
    padding-top: 30%;
    margin-top: 3%;
    border-radius: 10%;
    background-color: orange;
    width: 30%;
}
.container-flex2  {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
}
</style>
<body>
   <div class="container-flex2">
        <div class="block"></div>
        <div class="block"></div>
        <div class="block"></div>
        <div class="block"></div>
        <div class="block"></div>
        <div class="block"></div>
        <div class="block"></div>
        <div class="block"></div>
        <div class="block"></div>
    </div>
</body>
</html>
```

**CSS实现一个等腰三角形**

主要是通过把高度设置成0,边框高度设置宽一些,设置其中三个边透明,只留一个边显示.

等边三角形是特殊的三角形,它的三条边都相等,顶角为60度,而高是边长的3*(1/2)/2倍,约等于0.866……假设底为160px，则高约为138.56px，因此要做边长为160px的等边三角形，可以这么做： 

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>测试</title>
    <style type="text/css">
        div {
             width:0px;height:0px;margin:100px auto;
             border-left:80px solid transparent; 
             border-right:80px solid transparent; 
             border-bottom:138.56px solid #A962CE; /*--三角形的高--*/
        }
    </style>
</head>
<body>
    <div>
    </div>
</body>
</html>
```

实现扇形,圆形:

border-raduis圆角的四个值按顺序取值分别为:左上,右上,右下,坐下.这里只设置一个值,代表四个角的取值都为50%.

原理: border-raduis:50% 弯曲元素的边框以创建圆.

由于圆在任何给定点具有相同的半径，故宽和高都需要保证一样的值，不同的值将创建椭圆。 

```html
<div class="circle"></div>
<style>
    .circle {
          border-radius: 50%;
          width: 80px;
          height: 80px;
           background: #666;
    }
</style>
```

扇形:

 1.利用border-raduis,实现90度角的扇形:

原理:左上角是圆角,其余三个角都是直角,左上角的值为宽和高一样的值,其他三个角的值不变(等于0);

```html
<div class="sector"></div>
<style>
.sector{
  border-radius:80px 0 0;
  width: 80px;
  height: 80px;
  background: #666;
}</style>
```

**旋转45度**

CSS中使用rotate方法来实现对元素的旋转,在参数中加入角度值,旋转方式为顺时针旋转.

