## 什么是Flex布局

Flex布局表示弹性布局,可以为盒装模型提供最大的灵活性.

**适用范围**

任何一种元素也可以适用Flex布局. 行内元素也可以使用Flex布局.

Webkit内核的浏览器,必须加上-webkit前缀.

### Flex布局中的一些基本概念

容器和项目:

什么叫容器: 采用flex布局的元素被称作容器.

什么叫项目: 在flex布局中的子元素被称作项目.

即父级元素采用flex布局,则父级元素为容器,全部子元素自动成为项目.

容器默认存在两根轴:水平的主轴和垂直的交叉轴.主轴的开始位置(与边框的交叉点)叫做main start,结束位置叫main end ; 交叉轴的开始位置叫做corss start,结束位置叫做cross end.

项目默认沿主轴排列.单个项目占据的主轴空间叫做main size , 占据的交叉轴空间叫做corss size.

容器的一些属性:

**flex-direction属性: 设置容器主轴的方向.**

包含四个属性值:

row :默认值 , 表示沿水平方向,由左到右.

row-reverse: 表示沿水平方向,由右到左.

column:表示垂直方向,由上到下.

column-reverse:表示垂直方向,由上到下.

**flex-wrap属性**: flex-wrap属性用于设置当项目在容器中一行无法显示的时候如何处理.

包含三个属性值:

nowrap : 表示不换行.

说明: 设置的项目的宽度就失效了,强行在一行显示.

wrap: 正常换行 , 第一个位于第一行的第一个.

wrap-reverse:向上换行,第一行位于下方.

**flex-flow属性**

flex-flow属性是flex-dercition 和 flex-wrap属性的简写,默认值为[row nowrap];

第一个属性值为flex-direction 的属性值.

第二个属性值为 flex-wrap.

**justify-content属性**

justify-content 属性用于设置项目在容器中的对齐方式.

```css
.wrap{
    justify-content: flex-start | flex-end | center |space-between | space-around
}
```

该属性主要由五个属性值:

flex-start : 默认值,左对齐.

flex-end :右对齐

center: 居中对齐.

space-between :两端对齐.

space-around : 每个项目两侧的间距相等.

**align-items属性**

align-items定义了项目在交叉轴上是如何对齐显示的.

```css
.wrap{
    align-items:flex-start | flex-end | center | baseline | stretch
}
```

该属性主要由五个属性值: (以交叉轴从上向下为例).

flex-start : 交叉轴的起点对齐.

flex-end : 交叉轴的终点对齐.

center: 交叉轴居中对齐.

baseline:项目的第一行文字的基线对齐.

stretch: 默认值: 如果项目未设置高度或者高度为auto, 将占满整个容器的高度.

**align-content 属性**

align-content属性定义了多根轴线的对齐方式.如果项目只有一根轴线,该属性不起作用.

flex-start: 与交叉轴的起点对齐.

flex-end: 与交叉轴的终点对齐.

center: 与交叉轴的中点对齐.

space-between: 与交叉轴两端对齐,轴线之间的间隔平均分布.

space-around: 每根轴线两侧的间隔都相等.所以,轴线之前的间隔比轴线与边框的间隔大一倍.

stretch(默认值): 轴线占满整个交叉轴.

### 项目的一些属性

order属性设置项目排序的位置,默认值为0,数值越小越靠前.

flex-grow属性:

flex-grow属性用来控制当前项目是否放大显示.默认值为0,表示即使容器有剩余空间也不放大显示.如果设置为1,则平均分摊后放大显示.

flex-shrink 属性

flex-shrink 属性表示元素的缩小比例.默认值为1,如果空间不够用时所有的项目同比缩小.如果一个项目的设置为0,则空间不足时该项目也不缩小.

flex-basis属性 ; **flex-basis属性表示表示项目占据主轴空间的值。默认为auto，表示项目当前默认的大小。如果设置为一个固定的值，则该项目在容器中占据固定的大小。** 

flex属性: flex属性是flex-grow属性,flex-shrink属性,flex-basis属性的简写.默认值为: 0 1 auto.

#### align-self 属性

**align-self属性表示当前项目可以和其他项目拥有不一样的对齐方式。它有六个可能的值。默认值为auto**
　　　auto：和父元素align-self的值一致

flex-start：顶端对齐

flex-end：底部对齐

center：竖直方向上居中对齐

baseline：item第一行文字的底部对齐

stretch：当item未设置高度时，item将和容器等高对齐



