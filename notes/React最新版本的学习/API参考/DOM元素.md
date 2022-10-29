# DOM元素:

React实现了一个独立于浏览器的DOM系统,以提高性能和跨浏览器的兼容性.React借此机会清理了浏览器DOM实现中的一些粗糙边缘.

在React中,所有 DOM 属性和属性（包括事件处理程序）都应该是驼峰式。例如，HTML 属性`tabindex`对应`tabIndex`于 React 中的属性。例外是`aria-*`和`data-*`属性，它应该是小写的。例如，您可以保留`aria-label`为`aria-label`. 

**checked**

当<input>组件的type类型位checkbox或radio时,组件支持checked属性.可以使用它来设置组件是否被选中.这对于构建受控组件很有帮助.而defaultChecked则是非受控组件的属性,用于设置组件首次挂载时是否被选中.

**className**

className属性用于指定CSS的class,此特性适用于所有常规DOM节点和SVG元素,如<div>,<a>及其他标签.

**dangerouslySetInnerHTML:**

`dangerouslySetInnerHTML` 是 React 为浏览器 DOM 提供 `innerHTML` 的替换方案。通常来讲，使用代码直接设置 HTML 存在风险，因为很容易无意中使用户暴露于 跨站脚本的攻击.因此,可以直接在React中设置HTML,但当想在设置dangerouslySetInnerHTML时,需要向其传递包含key为__html的对象.

```js
function createMarkup() {
  return {__html: 'First &middot; Second'};
}

function MyComponent() {
  return <div dangerouslySetInnerHTML={createMarkup()} />;
}

```

htmlFor:

由于for在JS中是保留字,所以React元素中使用了htmlFor来代替.

**onChange**

onChange事件与预期行为一致:每当表单字段变化时,该事件都会被触发.我们故意没有使用浏览器已有的默认行为,是因为onChange在浏览器中的行为和名称不对应,并且React依靠了该事件实时处理用户输入.

