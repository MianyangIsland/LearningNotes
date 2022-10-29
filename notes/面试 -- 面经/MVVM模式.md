## MVVM模式

一句话总结web前端MVVM: 操作数据,就是操作视图,就是操作DOM(无须直接操作DOM).

无需操作DOM! 借助MVVM框架,开发者只需完成包含**声明绑定**的视图模板,编写ViewModel中业务数据变更逻辑,View层则完全实现了自动化.这将极大的降低前端应用的操作复杂度,极大提升应用的开发效率.MVVM最标志性的特性就是**数据绑定**,MVVM的核心理念就是通过**声明式的数据绑定**来实现View层和其他层的分离.完全解耦View层这种理念,也使得Web前端的单元测试用例编写变的更容易.

MVVM,说到底还是一种分层架构.它的分层如下:

Model: 域模型 , 用于持久化

View: 作为视图模板存在

ViewModel : 作为视图的模型,为视图服务.

**Model层**

Model层 , 对应数据层的域模型 , 它主要做域模型同步 . 通过Ajax/fetch等API完成客户端和服务端业务Model的同步.在层间关系里,它主要用于抽象出ViewModel中视图的Model.

**View层**

View层,作为视图模板存在,在MVVM里,整个View是一个动态模板.除了定义结构,布局外,它展示的是ViewModel层的数据和状态.View 层不负责处理状态, View不负责处理状态,View层做的是 **数据绑定的声明,指令的声明,事件绑定的声明**.

**ViewModel层**

ViewModel层把View需要的层数据暴露,并对View层的**数据绑定声明,指令声明,事件绑定声明负责**,也就是处理View层的具体业务逻辑.ViewModel底层会做好绑定属性的监听.当ViewModel中数据变化,View层会得到更新,而当View中声明了数据的双向绑定(通常是表单元素),框架也会监听View层(表单)值的变化.一旦值变化,View层绑定的ViewModel中的数据也会得到自动更新.

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2017/8/21/695acd06665b36ac9dbb953df763b6de~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.image) 

如图所示,在前端MVVM框架中,往往没有清晰,独立的Model层.在实际业务开发中,我们通常按Web Component规范来组件化的开发应用,Model层的域模型往往分散在一个或几个Component的ViewModle层,而ViewModel层也会引入一些View层相关的中间状态,目的就是为了更好的为View层服务.

开发者在View层的视图模板中声明**数据绑定,事件绑定**后,在ViewModel中进行业务逻辑的**数据**处理.事件触发后,ViewModel中**数据**变更,View层自动更新.因为MVVM框架的引入,开发者只需关注业务逻辑,完成数据抽象,聚焦树,MVVM的视图引擎会帮你搞定View.因为数据驱动,一切变得更加简单.

**MVVM框架的工作**

不可置否,MVVM框架极大的提升了应用的开发效率.MVVM框架到底做了什么?

视图引擎

视图引擎: 为view层作为视图模板提供强力支持,开发者不需要操作DOM,由视图引擎来做.

数据存取器:

可以通过Object.defineProperty()API轻松定义,或通过自行封装存取函数的方式曲线完成.内部往往封装了**发布/订阅**模式,以此来完成堆数据的监听,数据变更时通知更新.是**数据绑定**实现的基础.

组件机制:

组件机制: MVVM框架提供组件的定义,继承,声明周期,组件间通信机制..

