# Fragments:

React中一个常见模式是一个组件返回多个元素.Fragements允许将子列表分组,而无需向DOM添加额外节点.

```js
render() {
  return (
    <React.Fragment>
      <ChildA />
      <ChildB />
      <ChildC />
    </React.Fragment>
  );
}
```

还有一种新的短语发可用与声明它们.

## 动机

一种常见模式是组件返回一个子元素列表.以此React代码片段为例:

```js
class Table extends React.Component {
  render() {
    return (
      <table>
        <tr>
          <Columns />
        </tr>
      </table>
    );
  }
}
```

<Colums/>需要返回多个<td>元素以使渲染的HTML有效.如果在<Colums />的render()中使用了父div,则生成的HTML将无效.

```js
class Columns extends React.Component {
  render() {
    return (
      <div>
        <td>Hello</td>
        <td>World</td>
      </div>
    );
  }
}
```

得到以恶搞<Table/>输出:

```js
<table>
  <tr>
    <div>
      <td>Hello</td>
      <td>World</td>
    </div>
  </tr>
</table>
```

Fragments解决了这个问题.

## 用法

```js
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}
```

这样可以正确的输出<Table/>

```js
<table>
  <tr>
    <td>Hello</td>
    <td>World</td>
  </tr>
</table>
```

## 短语法

可以使用一种新的,且更简介的语法来声明Fragements.它看起来像空标签:

```js
class Columns extends React.Component {
  render() {
    return (
      <>
        <td>Hello</td>
        <td>World</td>
      </>
    );
  }
}
```

可以像使用其他任意元素一样使用<></>,但它并不支持key或属性.

## 带key的Fragments

使用显示<React.Fragment>语法声明的片段可能具有key.一个使用场景是将一个集合映射到一个Fragments数组----举个例子,创建一个描述列表:

```js
function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        // 没有`key`，React 会发出一个关键警告
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}
```

key是唯一可以传递给Fragment的属性.