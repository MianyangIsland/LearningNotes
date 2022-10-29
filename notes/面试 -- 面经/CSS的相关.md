## CSS的单位px,em,rem,%,vw,vh:

1. px:

   px就是像素的缩写,相对长度单位,相对于屏幕分辨率

2. em:

   参考物为父元素的font-size,具有继承的特点.浏览器默认字体是16px,整个页面内1em不是一个固定的值.

   字体大小同样都是1.5em,具有继承的特点.浏览器默认字体是16px,整个页面内1em不是一个固定的值.

   字体大小同样都是1.5em，但是效果却截然不同，按照W3C提供的公式，我们可以计算下：

          class为id1的div字体大小继承自父元素body：16px*1.5em = 24px     
       
          class为id2的div字体大小继承自父元素id1：24px*1.5em = 36px
       
          class为id3的div字体大小继承自父元素id2：36px*1.5em = 54px

3. rem:

   rem是CSS3新增的一个相对单位,但相对的只是HTML根元素.通过它既可以做到只修改根元素就成比例地调整所有字体大小,又可以避免字体大小逐层复位地连锁反应.

4. %

   %半分比,相对长度单位,相对于父元素地百分比值,使用时必须从根容器就设置好百分比.

   元素宽度高与字体大小使用区别:

   (1) 尽量使用相对尺寸单位

    使用相对尺寸单位计量,则在调整页面的布局的时候,不需要遍历所有的内部DOM结构,重新设置内部子元素的尺寸.如果是随父容器或者整体页面布局而改变尺寸,则使用%更好,如元素的高度和宽度设置.

   (2) 字体尺寸尽量使用em,rem:

   为了字体大小的可维护性和伸缩性,推荐使用em,如果存在3层以及3层以上的字体相对尺寸的谁,可以考虑rem.

5. vw和vh

   vm,vh,vmin,vmax是一种视窗单位,也是相对单位.它相对的不是父节点或者页面的根节点.而是由视窗大小来决定,单位1,代表类似1%.视窗是你的浏览器实际显示内同的区域,换句话说是你页面浏览器不包括工具栏和按钮部分.

   具体描述如下:

   vw: 视窗宽度的百分比(1vw代表视窗的宽度为1%).

   vh:视窗高度的百分比.

   vmin: 去当前vw和vh中较小的那一个值

   vmax:取当前vw和vh中较大的那一个值.

   vh和vw相对于视口的宽度和高度,1vh等于1/100的视口宽度,1vw等于1/100的视口宽度,比如：浏览器高度900px，宽度为750px, 1 vh = 900px/100 = 9 px，1vw = 750px/100 = 7.5 px， 很容易实现与同屏幕等高的框。 

   

## CSS常用水平垂直居中的几种方法

### 一.利用margin:auto

元素有宽度和高度时,利用margin:auto设置元素水平垂直居中.

html:

```html
  <div class="div1">
      <div class="center"></div>
   </div>
```

css:

```css
 <style>
        .div1{
            background-color: #eee;
            width: 200px;
            height: 200px;
            position: relative;
        }
        .div1 .center{
            width: 50px;
            height: 50px;
            background-color: forestgreen;
            margin: auto;
            position: absolute;
            left: 0;
            right: 0;
            bottom: 0;
            top: 0;
        }
    </style>
```

二.利用position:absolute

HTML代码;

```html
<div class="div2">
	<div class="center"></div>
</div>
```

当已知元素宽度和高度时,可以设置position:absolute和margin为负的宽高的一般.

css:

```css
    <style>
       .div2{
        background-color: #eee;
        width: 200px;
        height: 200px;
        position: relative;
        margin-top: 20px;
       }
       .div2 .center{
        width: 50px;
        height: 50px;
        background-color: rgb(34,71,139);
        position: absolute;
        left: 50%;
        top: 50%;
        margin-left: -25px;
        margin-top: -25px;
       }
    </style>
```

当已知元素宽度和高度时,也可以利用calc计算属性设置top和left,CSS代码如下:

```css
.div2{
        background-color: #eee;
        width: 200px;
        height: 200px;
        position: relative;
        margin-top: 20px;
       }
       .div2 .center{
        width: 50px;
        height: 50px;
        background-color: rgb(34,71,139);
        position: absolute;
        left: calc(50% - 25px);
        top: calc(50% - 25px);
       }
```

当元素高度和宽度未知的时候,可以设置position:absolute和transform:translate(-50%,-50%);

```css
.center {
  width: 50px;
  height: 50px;
  background-color: rgb(34, 71, 139);
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
```

三.弹性盒子:

通过为其父元素设置disply:flex来实现居中.

HTML:

```html
<div class="div4">
       <div class="center">

       </div>
   </div>
```

CSS:

```css
.div4{
        background-color: #eee;
        width: 200px;
        height: 200px;
        position: relative;
        margin-top: 20px;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
       }
       .div4 .center{
        width: 50px;
        height: 50px;
        background-color: rgb(240,248,166);
       }
```

4.利用水平对齐和行高

设置text-align 和 line-height 实现**单行**文本水平垂直居中.

HTML

```html
<div class="div5">
     <p class="center">我要居中</p>
   </div>
```

CSS:

```html
<style>
       .div5{
        background-color: #eee;
        width: 200px;
        height: 200px;
        margin-top: 20px;
       }
       .div5 .center{
        text-align: center;
        line-height: 200px;
       }
    </style>
```

## CSS实现一侧宽度固定,另一侧宽度自适应.

要实现这样的效果,本质是要实现元素宽度的自适应,而元素宽度有:

1. 使用行块级元素宽度默认继承父元素宽度,如div.
2. 设置块元素的宽度为父元素的100%.
3. 还有一个可能比较少了解的,就是绝对定位的元素设置left:0和right:0就可以使得元素的宽度展开到父元素的宽度(如果元素的宽度未设置具体宽度).
4. flex弹性布局.

如果要是西安一侧的宽度自适应,上述中2显然不合适.

html部分的代码:

```html
<div class="box">
    <div class="a">固定宽度的一侧</div>
    <div class="b">自适应的一侧</div>
</div>
```

方法一:对固定宽度的元素设置float,自适应的一侧(块级元素)通过margin避免两元素的重叠,本质还是利用块级元素具有默认继承父元素宽度的特性.

```css
       .a{
        float: left;
        width: 200px;
        height: 200px;
        background-color: aqua;
       }
       .b{
        height: 200px;
        margin-left: 200px;
        background-color: blue;
       }
```

方法二: 对自适应元素设置absolute定位,通过left:0, right:0 对自适应元素进行宽度拉伸,再通过设置margin-left避免元素重叠:

```css
  .box{
        position: relative;
       }
       .a{
        width: 200px;
        height: 200px;
        background-color: aqua;
       }
       .b{
        height: 200px;
        position: absolute;
        top: 0;
        right: 0;
        left: 0;
        background-color: blue;
        margin-left: 200px;
       }
```

方法三:flex布局:

```css
.box{
        display: flex;
     }
     .a{
        width: 200px;
        height: 200px;
        background-color: aqua;
     }
     .b{
        height: 200px;
        flex: 1;
        background-color: blue;
     }
```



## flex 的三个参数flex: 1 0 auto

flex属性是flex-grow , flex-shrink , flex-basis三个属性的缩写.

flex-grow :  定义项目的放大比例:

 默认为0, 即 即使存在剩余空间,也不会放大.

 所有项目的flex-grow为1:等于剩余空间(自动放大占位);

 flex-grow为n的项目,占据空间(放大的比例)是flex-grow为1的n倍.

flex-shrink: 定义项目的缩小比例:

默认为1,即 如果空间不足,该项目将缩小.

所有项目的flex-shrink 为1时:当空间不足时,缩小的比例相同.

flex-shrink 为0: 空间不足时,该项目不是缩小.

flex-shrink 为n的项目, 空间不足时缩小的比例时flex-shrink为1的n倍.

flex-basis: 定义在分配多余空间之前,项目占据的主轴空间(main size),浏览器更具此属性计算主轴是否有多余空间.

 默认值为auto , 即 项目原本大小;

 设置后项目将占据固定空间.

所以flex属性的默认值为: 0 1 auto (不方法会缩小).

flex为none: 0 0 auto (不放大也不缩小).

flex 为 auto : 1 1 auto(放大且缩小).

flex:1 即为 flex-grow : 1 , 经常用作自适应布局,将父容器的display:flex,侧边栏大小固定后,将内容区flex: 1 , 内容区则会自动放大占满剩余区.

## CSS选择器权重和优先级

浏览器通过优先级来判断那些属性值与一个元素最为相关,从而在该元素上应用这些属性值.

优先级是匹配的选择器中每一种选择器类型的数值决定的.

当同一个元素有多个声明的时候,优先级才会有意义.因为每一个直接作用于元素的CSS规则总是会接管/覆盖(take over)该元素从祖先继承而来的规则.

当优先级与多个CSS声明中任意一个声明的优先级相等的时候,CSS中最后的那个声明将会被应用到元素上.

常见选择器的权重

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221162345604.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2piajY1Njg4Mzl6,size_16,color_FFFFFF,t_70) 

行内样式+1000 , id选择器 + 100,属性选择器.class或者伪类+10,元素选择器,或者伪类+1 , 通配符+0.

每一组选择器在判断优先级的时候,是相加的关系.

