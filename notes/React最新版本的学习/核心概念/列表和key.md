## 列表和key:

首先,让我们看下在JavaScript中如何转化列表:

如下代码,我没使用map()函数让数组中的每一项变双倍,然后我们得到了一个新的列表doubled并打印出来:

```js
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((number) => number * 2);
console.log(doubled);
```

在React中,把数组转化为元素列表的过程是相似的.

渲染多个组件:

你可以通过使用{}在jsx内构建一个元素集合.

```js
import React from 'react';
import './App.css';

function NumberList(props)
{
  const numbers = props.numbers;
  const listItems = numbers.map((number)=>{
    return <li key={number}>{number}</li>
  })

  return(
    <ul>
      {listItems}
    </ul>
  )
}
export default class App extends React.Component{

  constructor(props)
  {
    super(props);
  }

  state={
    'numbers':[1,2,3,4,5]
  }

 render()
 {
   return(
     <div>
       <NumberList numbers={this.state.numbers}/>
     </div>
   )
 }  
}
```



# key:

key帮助react识别哪些元素改变了,比如被添加或删除.因此需要给数组中的每一个元素赋予一个确定的标识.一个元素的key最好是这个元素在列表中拥有的一个独一无二的字符串.通常,我们使用书中的id来作为元素的key.当元素没有确定的id的时候,万不得已可以使用元素索引index作为key.如果列表项目的顺序可能会变化,我们不建议使用索引来作用key值,因为这样做会导致性能变差,还可能引起组件状态的问题.



一个好的经验法则是:在map()方法中的元素需要设置key属性.



### key值在兄弟节点之间必须是唯一的:

数组元素中使用的key在其兄弟节点之间应该是独一无二的.然而,它们不需要是全局唯一的.当我们生成两个不同数组时,我们可以下h使用相同的key值.

key会传递信息给React,但不会传递给你的组件.

```js
import React from 'react';
import './App.css';


function Blog(props) {
  const sidebar = (
    <ul>
      {props.posts.map((post) =>
        <li key={post.id}>
          {post.title}
        </li>
      )}
    </ul>
  );
  const content = props.posts.map((post) =>
    <div key={post.id}>
      <h3>{post.title}</h3>
      <p>{post.content}</p>
    </div>
  );
  return (
    <div>
      {sidebar}
      <hr />
      {content}
    </div>
  );
}

export default class App extends React.Component{

  constructor(props)
  {
    super(props);
  }

  state={
    posts:[
      {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},
      {id: 2, title: 'Installation', content: 'You can install React from npm.'}
    ]
  }

 render()
 {
   return(
     <div>
       <Blog posts={this.state.posts}/>
     </div>
   )
 }  
}
```



### 在JSX中嵌入map():

jsx允许在大括号中嵌入任何表达式,所以我们可以内联mao()返回的结果.

```js
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      )}
    </ul>
  );
}
```

