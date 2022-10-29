一.事件处理:

1)通过onXxx属性指定事件处理函数（注意大小写）

   a.React使用的是自定义（合成）事件,而不是使用原生DOM事件:(为了更好的兼容性)

   b.React中的事件是通过事件委托方式处理（委托给组件最外层的元素）--为了高效

2)通过event.target得到发生事件的DOM元素对象.

  请勿过度使用ref,当事件发生的元素与你要操作的元素为同一个元素的时候,就可以避免使用ref.

eg:

  ```javascript
class Demo extends Component{
   
   render()
   {
       return(
           <Fragment>
               <input type='text' onBlur={this.showDate} placeholder='失去焦点提示数据'></input>
           </Fragment>
       );
   }
   showDate=(event)=>{
      console.log(event.target.value);
   }
  
}
  ```

二.react非受控组件:

 页面里面，所有的输入DOM的值,采用现用现取的方法获取其数据,那么这个组件就是非受控组件.

eg:

```javascript
class Login extends Component{
    handleSubmit=(event)=>{
        event.preventDefault();//阻止默认事件,组件表单提交
        const {username,password} = this;
        alert(`您输入的用户名为${username.value},您输入的密码为${password.value}`)
    }
    render() {
        return (
             <Fragment>
                 <form  onSubmit={this.handleSubmit}>
                     用户名：<input ref={c => this.username =c} type="text" name='username'></input>
                     密码：<input ref={c => this.password = c} type='password' name='password'></input>
                     <button>登录</button>
                 </form>
             </Fragment>
        );
    }
};
```



 三.受控组件：可以有效解决过度使用ref的问题.

输入值伴随着改变绑定到状态里面

eg:

```javascript
class Login extends Component{
//初始化数据
   state={
       username:'',
       password:''
   }
//保存输入值到状态中
   saveUsername = (event)=>{
     this.setState({
         username:event.target.value
     })     
 }

 savePassword=(event)=>{
     this.setState({
         password:event.target.value
     })
 }
//表单提交的回调
    handleSubmit=(event)=>{
        event.preventDefault();//阻止默认事件,组件表单提交
        const {username,password} = this.state;
        alert(`您输入的用户名为${username},您输入的密码为${password}`)
    }
    render() {
        return (
             <Fragment>
                 <form  onSubmit={this.handleSubmit}>
                     用户名：<input onChange={this.saveUsername} ref={c => this.username =c} type="text" name='username'></input>
                     密码：<input ref={this.savePassword} type='password' name='password'></input>
                     <button>登录</button>
                 </form>
             </Fragment>
        );
    }
};
```

五.函数柯里化:

  1.对象相关知识的复习:

 ```javascript
<script type="text/javascript">
        //错误的读取变量方法:
       let a = 'name';
       let obj = {};
       obj.a = 'tom';
       console.log(obj);//{a:'tom'}
       //正确写法:
      
       obj[a]='tom';
       console.log(obj);//{name:'tom'}
       
    </script>
 ```

 2.高阶函数:如果一个函数符合下面2个规范中的任何一个，那么该函数就是高阶函数.

   1) 若A函数，接收的参数是一个函数,那么A就可以称之为高阶函数.

   2) 若A函数，调用的返回值依然是一个函数,那么A就可以称之为高阶函数.

  常见的高阶函数: Promise,setTimeout,arr.map()....

  3) 函数的柯里化: 通过函数调用继续返回函数的方式，实现多次接收参数最后统一处理的函数编码形式.

eg:

```javascript
<script type="text/javascript">
      let sum = (a)=>{
          return (b)=>{
              return (c)=>{
                  return a+b+c;
              }
          }
      }
      const result = sum(1)(2)(3);
      console.log(result);
    </script>
```

```javascript
class Login extends Component{
        //初始化数据
        state={
            username:'',
            password:''
        }
//保存输入值到状态中
  saveFormDate=(dateType)=>{
    return (event)=>{
      this.setState({
          [dateType]:event.target.value
      })
    }
  }
// 表单提交的回调
    handleSubmit=(event)=>{
        event.preventDefault();//阻止默认事件,组件表单提交
        const {username,password} = this.state;
        alert(`您输入的用户名为${username},您输入的密码为${password}`)
    }
    render() {
        return (
             <Fragment>
                 <form  onSubmit={this.handleSubmit}>
                     用户名：<input onChange={this.saveFormDate('username')}  type="text" name='username'></input>
                     密码：<input onChange={this.saveFormDate('password')} type='password' name='password'></input>
                     <button>登录</button>
                 </form>
             </Fragment>
        );
    }
};
```

注意:这里onChage事件接受的是函数的返回值,所以this.saveFormdate()返回的是这个函数的返回值,所以利用函数的柯里化解决这个问题,返回一个函数去接收event.

不用函数柯里化的写法:

 ```javascript
class Login extends Component{
        //初始化数据
        state={
            username:'',
            password:''
        }
//保存输入值到状态中
  saveFormDate=(dateType,value)=>{
    this.setState({
        [dateType]:value
    })
  }
// 表单提交的回调
    handleSubmit=(event)=>{
        event.preventDefault();//阻止默认事件,组件表单提交
        const {username,password} = this.state;
        alert(`您输入的用户名为${username},您输入的密码为${password}`)
    }
    render() {
        return (
             <Fragment>
                 <form  onSubmit={this.handleSubmit}>
                     用户名：<input onChange={(event)=>{this.saveFormDate('username',event.target.value)}}  type="text" name='username'></input>
                     密码：<input onChange={(event)=>{this.saveFormDate('password',event.target.value)}} type='password' name='password'></input>
                     <button>登录</button>
                 </form>
             </Fragment>
        );
    }
};
 ```

