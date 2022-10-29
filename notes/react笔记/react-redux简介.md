一.react-redux模型图

1.所有的UI组件都应该包裹一个容器组件，他们是父子关系.

2.容器组件是真正和redux打交道的，里面可以随意的使用redux的api.

3.UI组件中不能使用任何redux的api.

4.容器组件会传给UI组件：（1）.redux中所保存的状态。（2）.用于操作状态的方法.

5.备注：容器给UI传递：状态，操作状态的方法，均通过props传递.

![img](https://img-blog.csdnimg.cn/20210510124058586.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NzgzNjYw,size_16,color_FFFFFF,t_70) 

二.容器组件的建立：

  ```javascript
1.import CountUI from '../../components/Count'  //引入Count的UI组件.
2.import {connect} from 'react-redux';//引入connect用于链接UI组件与redux.
//mapStateToProps函数返回的对象中的key就作为传递给UI组件props的key,value就作为传递给UI组件props的value--状态
function mapStateToProps(state){
    return {
        count:state
    }
}
//mapDispatchToProps函数返回的对象中的key就作为传递给UI组件props的key,value就作为传递给UI组件props的value--操作状态的方法
function mapDispatchToProps(dispatch){
    return {
        jia:(number)=>{
            //通知redux执行加法
            dispatch(createIncrementAction(number))
        }
    }
}
3.export default connet(mapStateToProps,mapDispatchToProps)(CountUI);//使用connet()()创建并暴露一个Count的容器组件.
4.在App.jsx（上一级）里面，将store通过props传递给下面的CountUI组件
  ```

三.总结（一）:

  (1)明确两个概念：

​    1）UI组件：不能使用任何redux的api,只负责页面的呈现，交互等.

​     2)容器组件：负责和redux通信，将结果交给UI组件.

(2)如何创建一个容器组件----靠react-redux的connet函数.

​          connect(mapStateToProps,mapDispatchToProps)(UI组件)

​                           -mapStateToProps:映射状态，返回值是一个对象.

​                          -mapDispatchToProps:映射操作状态的方法，返回值是一个对象.

(3).备注：容器组件中的store是靠props传进去的，而不是在容器组件中直接引入.

四.优化部分：

 （1）.容器组件和UI组件混成一个文件.

   (2).无需自己给容器组件传递store,给<App/>包裹一个<Provider srore={store}>即可.  

   (3)使用了react-redux后再也不用自己检测redux中状态,容器组件可以自动完成这个工作.

  (4)mapDispatchToProps也可以简单的写成一个对象.

  (5) 一个组件要和redux"打交道"要经过那几步？

​               1）定义好UI组件---不暴露

​                2）引入connect生成一个容器组件，并暴露，写法如下：

​                               connect(

​                                state=>({key:value}),//映射状态

​                                  {key:xxxxxxAction}//映射操作状态方法.

​                                 )(UI组件)

​                     4)在UI组件中通过this.props.xxxxx读取和操作状态.