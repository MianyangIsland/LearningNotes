一.引出生命周期函数:

   1.render调用的时机:1)初始化渲染  2) 状态完成更新之后.

  2.componentDidMount()  //组件挂在完成之后调用,只调用一次

3.叫法:

生命周期回调函数 <=> 生命周期钩子函数 <=> 生命周期函数 <=> 生命周期钩子

4.总结：组件从创建到死亡会经历一些特定的阶段.React组件中包含一系列钩子函数（生命周期回调函数）会在特定的时刻调用.我们在定义组件时，会在特定的生命周期回调函数中做特定的工作。

二.react生命周期图：

![](C:\Users\22719\Pictures\Camera Roll\react生命周期图（旧）.png)

三.生命周期函数过程演示：

```javascript
class Count extends Component{
  
    //构造器
    constructor(props)
    {
        super(props)
        //初始化状态
        console.log('constructor');
        this.state={
            count:0
        }
    }

    add=()=>{
        const {count} = this.state;
        this.setState({
            count:count+1
        })
    }

    //强制更新按钮的回调
    force=()=>{
        //强制更新
      this.forceUpdate(
        {
           
        }
      )
     
    }

    //卸载组件按钮的回调
    death=()=>{
        ReactDOM.unmountComponentAtNode(document.getElementById('root'));
    }

    //组件将要挂载
    componentWillMount()
    {
        console.log('count ---componentWillMount');
    }

    //组件挂载完成
    componentDidMount()
    {
        console.log('count ---componentDidMount');
    }

    //组件将要卸载
    componentWillUnmount()
    {
        console.log('count ---componentWillUnmount');
    }

    //控制组件更新的"阀门",如果不写这个函数,那么react会自动为你补上，并且返回值默认为true.
    //如果写了  那么手动返回fasle的话  组件就不会被更新.
    shouldComponentUpdate()
    {
     console.log('count---shouldComponentUpdate');
     return true;
    }

    //组件将要更新的钩子
    componentWillUpdate()
    {
        console.log('count---componentWillUpdate');
    }

    //组件更新完成的钩子
    componentDidUpdate()
    {
        console.log('count ----componentDidUpdate');
    }

    render(){
        console.log('render');
        const {count} = this.state
        return(
            <Fragment>
                <h2>当前计数为：{count}</h2>
                <button onClick={this.add}>点我+1</button>
                <button onClick={this.death}>点我卸载组件</button>
                <button onClick={this.force}>不更改状态的任何数据,组件强制更新</button>
            </Fragment>
        )
    }
}

class A extends Component{
    state ={
        carName:'东风标志318'
    }

    changeCar=()=>{
    this.setState({
        carName:'丰田霸道'
    })
    }
    render()
    {
        return(
         <Fragment>
         <div>我是A组件</div>
         <button onClick={this.changeCar}>年底该换车了</button>
         <div >我是A组件</div>
         <B carName={this.state.carName}/>
         </Fragment>
        )
    }
}

class B extends Component{

    componentDidMount()
    {
        console.log('B---componentDidMount');
    }

    //组件将要接收新的props钩子(注意：第一次组件挂载时父组件传递过来props不调用)
    componentWillReceiveProps()
    {
        console.log('B ---componentWillReceiveProps',this.props);
    }
 
    shouldComponentUpdate()
    {
        console.log('B---shouldComponentUpdate');
        return true;
    }

    componentWillUpdate()
    {
        console.log('B---componentWillUpdate');
    }


    componentDidUpdate()
    {
        console.log('B---componentDidUpdate');
    }

    render()
    {
        return(
            <div>我是B组件：接收到的车是{this.props.carName}</div>
        )
    }
}
ReactDOM.render(<A/>,document.getElementById('root'));
```

四.生命周期函数(旧)的总结:

1.初始化阶段：由ReactDOM.render()触发---初次渲染

  1.constructor()-> 2.componentWillMount()-> 3.(常用)render() -> 4. componentDidMount()==>常用，一般做一些初始化的事情,例如：

开启定时器，发送请求，订阅消息

2.更新阶段:由组件内部this.setSate()或父组件重新render触发

//强制更新少了(1)步骤

1.shouldComponentUpdate() -> 2.(常用：一般在这个钩子里面做一些些收尾的事情,例如：关闭定时机器)ComponentWillUpdate() -> 3.render()

-> 4. componentDidUpdate()

3.卸载组件：由ReactDOM.unmountComponentAtNode()触发

1.componentWillUnmount()