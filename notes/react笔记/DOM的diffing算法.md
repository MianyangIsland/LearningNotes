一.验证diffing算法：

```javascript
class Time extends Component{
    state = {
        date:new Date()
    }

    componentDidMount()
    {
        setInterval(()=>{
           this.setState({
               date:new Date()
           })
        },1000)
    }

    render()
    {
        return(
            <Fragment>
                <h1>Hello React</h1>
                <input type='text'></input><br/>
                <span>现在是： {this.state.date.toTimeString()}
                <input type="text"></input>
                </span>
            </Fragment>
        )
    }
}
```

在这里面,span标签的里面的input标签里面的内容输入之后并不丢失,所以在这面的话，这个diffing算法的历度的最小单位是结点标签,当调用render函数之后呢,react底层对比render返回的dom元素与页面上以及挂在的dom的区别,把变化了的dom进行重新渲染,而没有发生改变的dom就继续挂在到页面上，不做改变.

二.key的作用:

经典面试题：

   1）.react/vue 中的key有什么作用?(key的内部原理是什么？)

   2)  .为什么遍历列表,key最好你要index?

1.虚拟DOM中的key的作用:

​     1) 简单的说：key是虚拟DOM对象的标识,在更新显示时key起着极其重要的作用.

​    2) 详细的说：当状态中的数据发生变化时,react会根据新数据生成新的虚拟DOM,随后React进行新虚拟DOM与旧虚拟DOM的diff比较，比较规则如下：

​      a.旧虚拟DOM中找到了新虚拟DOM相同的key:

​         (1).若虚拟DOM中内容没变,直接使用之前的真实DOM.

​        (2). 若虚拟DOM中内容变了，则生成新的真实DOM,随后替换掉页面之前的真实DOM.

​      b.旧虚拟DOM中未找到与虚拟DOM相同的key

​           根据数据创建新的真实DOM，随后渲染到页面.

2.用index作为key可能会引发的问题:

​       1.若对数据进行：逆序添加，逆序删除等破坏顺序操作:

​             会产生没有必要的真实DOM更新 ==》 界面效果没问题，但效率低.

​       2.如果结构中还包括输入类DOM:

​               会产生错误DOM更新  ==》 界面有问题.

​     3.注意： 如果不存在对数据的逆序添加，逆序删除等破坏顺序操作.

​               仅用于渲染列表于展示，使用index作为key是没有问题的.

3.开发中如何选择key？

​             1.最好使用每条数据的唯一标识符作为key,比如id,手机号，身份证号，学号等唯一值.

​              2.如果确定只是简单的展示数据，用index也是可以的.

eg:

```javascript
class Person extends Component{
    state = {
        person:[
            {id:1,name:'郭勇',age:18},
            {id:2,name:'徐中海',age:19},
            {id:3,name:'魏俊华',age:20}
        ]
    }

    add= ()=>{
        const {person} = this.state;
        let p = {
            id:person.length+1,
            name:'刘辉',
            age:21
        }

        this.setState({
            person:[p,...person]
        })

      
    }

    render()
    {
        return(
            <Fragment>
                <h1>展示人员信息</h1>
                <h2>利用index作为key展示信息</h2>
                <button onClick={this.add}>添加一个刘辉</button>
                <ul>
                    {
                        this.state.person.map((perosnObj,index)=>{
                           return <li key={index}>{perosnObj.name}----{perosnObj.age} <input type="text"></input></li>
                        })
                    }
                </ul>
                <hr/>
                <hr/>
               <h2>用id作为key展示数据</h2>
               <ul>
                    {
                        this.state.person.map((perosnObj)=>{
                           return <li key={perosnObj.id}>{perosnObj.name}----{perosnObj.age} <input type="text"></input></li>
                        })
                    }
                </ul>
            </Fragment>
        )
    }
}

```



