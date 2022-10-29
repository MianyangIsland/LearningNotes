一.字符串形式的ref: (官网不推荐使用,采用下面两种方法)

 ~~~javascript
class Demo extends Component{
    render()
    {
        return(
            <Fragment>
                <input ref="input1" type="text" placeholder='点击按钮提示数据'/>&nbsp;
                <button ref="btn" onClick={this.showDate}>点我提示左侧数据</button>&nbsp;
                <input onBlur={this.showDate2} ref="input2" type="text" placeholder='失去焦点提示数据'></input>&nbsp;
            </Fragment>
        )
    }
    showDate = ()=>{
       const {input1} = this.refs;
       alert(input1.value);
    }
    showDate2 = ()=>{
        const {input2} = this.refs;
        alert(input2.value);
    }
}
 ~~~

二.回调函数形式的ref:

 eg:

```javascript
class Demo extends Component{
    render()
    {
        return(
            <Fragment>
                <input ref={currentNode => this.input1 = currentNode} type="text" placeholder='点击按钮提示数据'/>&nbsp;
                <button ref="btn" onClick={this.showDate}>点我提示左侧数据</button>&nbsp;
                <input ref={currentNode => this.input2 = currentNode} onBlur={this.showDate2}  type="text" placeholder='失去焦点提示数据'></input>&nbsp;
            </Fragment>
        )
    }
    showDate = ()=>{
       const {input1} = this;
       alert(input1.value);
    }
    showDate2 = ()=>{
        const {input2} = this;
        alert(input2.value);
    }
}

//ref={currentNode => this.input1 = currentNode}
//通过回调函数形式的ref，把当前ref所处的结点传入该回调函数,并且把当前结点放在了组件实例自身上成为一个属性，然后该属性起名为input1.
```

三.回调ref中的调用次数问题:

  如果ref回调函数是以内联函数（直接在标签里面写的函数）的方式定义的，在更新过程中它会被执行两次,第一次传入参数null(为了清空之前结点内的内容),然后第二次会传入参数DOM元素，这是因为在每次渲染的时候会创建一个新的函数实例,所以React清空旧的ref并且设置新的,通过将ref的回调函数定义成class的绑定函数的方式，可以避免频繁被调用的问题.

  eg:

  内联函数形式： {/*<input ref={currentNode => {this.input1 = currentNode;console.log('@',currentNode)}} type='text'></input>  */} 

 class绑定函数方式：

```javascript
  <input ref={this.saveInput} type='text'></input>  

 render()
   {
       return(
           <Fragment>
               <h2>今天天气很{this.state.isHot?'炎热':'凉爽'}</h2>
               {/*<input ref={currentNode => {this.input1 = currentNode;console.log('@',currentNode)}} type='text'></input>  */}
                <input ref={this.saveInput} type='text'></input> 
               <button  onClick={this.showInfo}>点我提示数据</button>
               <button onClick={this.changeWeather}>点我切换天气</button>
           </Fragment>
       )
   }
   saveInput=(c)=>{
    console.log('@',c);
   this.input1 = c;
}
```

四.createRef的使用:

 ```javascript
class Demo extends Component{
    /* React.createRef调用后可以返回一个容器,该容器可以存储被ref所表示的结点
     该容器是"专人专用的"也就是一个结点对应一个容器*/
   myRef=React.createRef();
   myRef2=React.createRef();
   render()
   {
       return(
           <Fragment>
               <input ref={this.myRef} type='text'></input> 
               <button  onClick={this.showInfo}>点我提示数据</button>
               <input onBlur={this.showDate} ref={this.myRef2}></input>
           </Fragment>
       )
   }
   showInfo=()=>{
       alert(this.myRef.current.value)
     }
   showDate=()=>{
       alert(this.myRef2.current.value)
   }
  
}
 ```

