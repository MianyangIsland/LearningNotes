**受控组件基本概念**

对某个组件状态的掌控,它的值是否只能由用户设置,而不能通过代码控制.

在HTML的表单中,它们通常自己维护一套state,并随着用户的输入自己进行UI上的更新,这种行为是不被我们程序所管控的.而如果将React里的state属性和表单元素的值建立依赖关系,再通过onChange事件与setState()结合更新state属性,就能达到控制用户输入过程中表单发生的操作.被React以这种方式控制取值的表单输入元素就叫做**受控组件**.

eg:

```js
class TestComponent extends React.Component {
  constructor (props) {
    super(props);
    this.state = {
      username: "lindaidai"
    }
  }
  onChange (e) {
    console.log(e.target.value);
    this.setState({
      username: e.target.value
    })
  }
  render () {
    return <input name="username" value={this.state.username} onChange={(e) => this.onChange(e)} />
  }
}
```

**select受控组件**

对于其他的表单元素使用起来也差不多,可能就是属性名和事件不同而已.

例如,input类型为text的表单元素中使用的是:

value , onChange.

对于textarea 标签也和它一样是使用value和onChange.

```js
<textarea value={this.state.value} onChange={this.handleChange} />
```

单选select

对于select表单元素来说,React中将其转化为受控组件可能和原生HTML中有一些区别.

在原生中，我们默认一个`select`选项选中使用的是`selected`，比如下面这样： 

```html
<select>
  <option value="sunshine">阳光</option>
  <option value="handsome">帅气</option>
  <option selected value="cute">可爱</option>
  <option value="reserved">高冷</option>
</select>
```

但是如果是使用React受控组件来写的话就不用那么麻烦了,因为它允许在根select标签上使用value属性,去控制选中了那个.

```js
class SelectComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { value: 'cute' };
  }
  handleChange(event) {
    this.setState({value: event.target.value});
  }
  handleSubmit(event) {
    alert('你今日相亲对象的类型是: ' + this.state.value);
    event.preventDefault();
  }
  render() {
    return (
      <form onSubmit={(e) => this.handleSubmit(e)}>
        <label>
          你今日相亲对象的类型是:
          <select value={this.state.value} onChange={(e) => this.handleChange(e)}>
            <option value="sunshine">阳光</option>
            <option value="handsome">帅气</option>
            <option value="cute">可爱</option>
            <option value="reserved">高冷</option>
          </select>
        </label>
        <input type="submit" value="提交" />
      </form>
    );
  }
}
export default SelectComponent;
```

多选select的话,对比单选来说,只有这两处改动:

给select标签设置multiple属性为true.

select标签value绑定的值为一个数组.

```js
class SelectComponent extends React.Component {
  constructor(props) {
    super(props);
    // this.state = { value: 'cute' };
    this.state = { value: ['cute'] };
  }

  handleChange(event) {
    console.log(event.target.value)
    const val = event.target.value;
    const oldValue = this.state.value;
    const i = this.state.value.indexOf(val);
    const newValue = i > -1 ? [...oldValue].splice(i, 1) : [...oldValue, val];
    this.setState({value: newValue});
  }

  handleSubmit(event) {
    alert('你今日相亲对象的类型是: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={(e) => this.handleSubmit(e)}>
        <label>
          你今日相亲对象的类型是:
          <select multiple={true} value={this.state.value} onChange={(e) => this.handleChange(e)}>
            <option value="sunshine">阳光</option>
            <option value="handsome">帅气</option>
            <option value="cute">可爱</option>
            <option value="reserved">高冷</option>
          </select>
        </label>
        <input type="submit" value="提交" />
      </form>
    );
  }
}
export default SelectComponent;
```

## 非受控组件

对于非受控组件,我们需要为每个状态更新(例如this.state.username)编写一个事件处理程序(例如this.setState({usename:e.target.value}));

那么还有一种场景是: 我们仅仅是想获取某个表单元素的值,而不关心它是如何改变的.

对于这种场景 , 由于input标签也是一个DOM元素, 那么我们可以用获取DOM元素信息的方式来获取表单元素的值,也就是使用ref.

eg:

```js
import React, { Component } from 'react';

export class UnControll extends Component {
  constructor (props) {
    super(props);
    this.inputRef = React.createRef();
  }
  handleSubmit = (e) => {
    console.log('我们可以获得input内的值为', this.inputRef.current.value);
    e.preventDefault();
  }
  render () {
    return (
      <form onSubmit={e => this.handleSubmit(e)}>
        <input defaultValue="lindaidai" ref={this.inputRef} />
        <input type="submit" value="提交" />
      </form>
    )
  }
}
```

**特殊的文件file标签**

对于file类型的表单控件 , 它始终是一个**不受控制的组件**,因为它的值只能有用户设置,而不是以编程方式设置.

所以我们应当使用非受控组件的方式来获取它的值,可以这样写:

```js
import React, { Component } from 'react';

export default class FileComponent extends Component {
  constructor (props) {
    super(props);
    this.fileRef = React.createRef();
  }
  handleSubmit = (e) => {
    console.log('我们可以获得file的值为', this.fileRef.current.files);
    e.preventDefault();
  }
  render () {
    return (
      <form onSubmit={e => this.handleSubmit(e)}>
        <input type="file" ref={this.fileRef} />
        <input type="submit" value="提交" />
      </form>
    )
  }
}
```

这里获取到的files是一个数组,当然,如果你没有开启多选的话,这个数组的长度始终是1,开启多选也非常简单,只需要添加multiple属性即可:

```html
<input type="file" multiple ref={this.fileRef} />
```

实际当中,用React官方的话来说,绝大部分时候推荐使用受控组件来实现表单,因为在受控组件中,表单数据由React组件负责处理;当然如果选择非受控组件的话,表单数据就由DOM本身处理.

