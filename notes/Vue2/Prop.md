## Prop

**Prop的大小写**

HTML中的attribute 名是大小写不敏感的, 所以浏览器会把所有大写字符解释为小写字符.这意味着放你使用DOM中的模板时 , camelCase(驼峰命名法)的prop名需要使用其等价的kebab-case (短横线分隔命名)命名:

```txt
HTML中的attribute 名是大小写不敏感的,所以浏览器会把所有大写字符解释为小写字符 . 这意味着当你使用DOM中的模板时,camelCase(驼峰命名法)的prop名需要使用其等价的kebab-case(短横线分隔命名)命名:
```

```js
Vue.component('blog-post', {
  // 在 JavaScript 中是 camelCase 的
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})
```

```html
<!-- 在 HTML 中是 kebab-case 的 -->
<blog-post post-title="hello!"></blog-post>
```

重申一次 , 如果你使用字符串模板,那么这个限制就不存在了.

**Prop类型**

到这里,我们只看到了以字符串数组形式列出的prop:

```js
props: ['title', 'likes', 'isPublished', 'commentIds', 'author']
```

但是,通常你希望每个prop都有指定的值类型.这时,你可以以对象形似列出prop,这些property的名称和值分别是prop各自的名称和类型:

## 传递静态或动态Prop

像这样 , 你已经知道了可以像这样给prop传入一个静态的值:

```html
<blog-post title="My journey with Vue"></blog-post>
```

你也知道prop可以通过v-bind 动态赋值 , 例如:

```html
<!-- 动态赋予一个变量的值 -->
<blog-post v-bind:title="post.title"></blog-post>

<!-- 动态赋予一个复杂表达式的值 -->
<blog-post
  v-bind:title="post.title + ' by ' + post.author.name"
></blog-post>
```

我们传入的值都是字符串类型 , 但实际上 任何 类型的值都可以传给一个prop.

**传入一个数字**

```html
<!-- 即便 `42` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:likes="42"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:likes="post.likes"></blog-post>
```

**传入一个布尔值**

```html
<!-- 包含该 prop 没有值的情况在内，都意味着 `true`。-->
<blog-post is-published></blog-post>

<!-- 即便 `false` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:is-published="false"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:is-published="post.isPublished"></blog-post>
```

**传入一个数组**

```html
<!-- 即便数组是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:comment-ids="[234, 266, 273]"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:comment-ids="post.commentIds"></blog-post>
```

**传入一个对象**

```html
<!-- 即便对象是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post
  v-bind:author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:author="post.author"></blog-post>
```

**传入一个对象的所有property**

如果你想要将一个对象的所有property 都作为prop 传入 , 你可以使用不带参数的 v-bind (取代 v-bind:prop-name). 例如 , 对于一个给定的对象: post:

```js
post: {
  id: 1,
  title: 'My Journey with Vue'
}
```

下面的模板:

```html
<blog-post v-bind="post"></blog-post>
```

等价于:

```html
<blog-post
  v-bind:id="post.id"
  v-bind:title="post.title"
></blog-post>
```

**单向数据流**

所有的prop都使得其父子prop之间形成了一个**单向下行绑定**: 父级prop 的更新会向下流动到子组件中 , 但是反过来则不行 . 这样会防止从子组件意外变更父级组件的状态 , 从而导致你的应用的数据流向难以理解.

额外的 , 每次父级组件发生变更时,子组件中所有的 prop 都将会刷新未最新的值 . 这意味着你 **不** 应该在一个子组件内部改变prop . 如果你这样做了 , Vue会在浏览器的控制台中发出警告.

这里有两种常见的试图变更一个prop 的情形:

1**.这个prop用来传递一个初始值 ; 这个子组件接下来希望将其作为一个本地的prop数据来使用 .**  在这种情况下 , 最好定义一个本地的data property 并将这个prop 用作其初始值:

```js
props: ['initialCounter'],
data: function () {
  return {
    counter: this.initialCounter
  }
}
```

2.**这个prop以一种原始的值传入且需要进行转换 .** 在这种情况下 , 最好使用这个prop的值来定义一个计算属性:

```js
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```

Warning: 在JavaScript 中对象和数组是通过引用传入的 , 所以对于一个数组或对象类型的prop来说 , 在子组件中改变变更这个对象或数组本身**将会**影响到父组件的状态.

## Prop验证

我们可以为组件的prop指定验证要求 , 例如你知道这些类型 . 如果有一个需求没有被满足 , 则Vue会在浏览器控制台中警告你 . 这在开发一个被别人用到的组件时尤其有帮助.

为了定制prop的验证方式 , 可以为prop 中的值提供一个带有验证需求的对象 , 而不是一个字符串数组.例如:

```js
Vue.component('my-component', {
  props: {
    // 基础的类型检查 (`null` 和 `undefined` 会通过任何类型验证)
    propA: Number,
    // 多个可能的类型
    propB: [String, Number],
    // 必填的字符串
    propC: {
      type: String,
      required: true
    },
    // 带有默认值的数字
    propD: {
      type: Number,
      default: 100
    },
    // 带有默认值的对象
    propE: {
      type: Object,
      // 对象或数组默认值必须从一个工厂函数获取
      default: function () {
        return { message: 'hello' }
      }
    },
    // 自定义验证函数
    propF: {
      validator: function (value) {
        // 这个值必须匹配下列字符串中的一个
        return ['success', 'warning', 'danger'].includes(value)
      }
    }
  }
})
```

当prop验证失败的时候 , (开发环境构建版本的)Vue将会产生一个控制台的警告.

Warning: 那些prop会在一个组件实例创建**之前**进行验证 , 所以实例的property (如 data , computed 等)在default 或 validator 函数中是不可用的.

**类型检查**

type可以是下列原生构造函数中的一个:

- `String`
- `Number`
- `Boolean`
- `Array`
- `Object`
- `Date`
- `Function`
- `Symbol`

额外的 , type 还可以是一个自定义的构造函数 , 并且通过 instanceof 来进行检查确认 . 例如 , 给定下列现成的构造函数:

```js
function Person (firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
}
```

可以使用:

```js
Vue.component('blog-post', {
  props: {
    author: Person
  }
})
```

来验证 author prop 的值是否是通过 new Person 创建的.

**非Prop的 Attribute** 

一个非 prop 的 attribute 是指传向一个组件 , 但是该组件并没有相应prop 定义的attribute .

因为显式定义的 prop 适用于向一个子组件传入信息，然而组件库的作者并不总能预见组件会被用于怎样的场景。这也是为什么组件可以接受任意的 attribute，而这些 attribute 会被添加到这个组件的根元素上。 

例如 , 想象一个你通过Bootstrap 插件使用了一个第三方的 <bootstrap-date-input> 组件, 这个插件需要在其<input>上用到一个 data-date-picker attribute . 我们可以将这个attribute 添加到你的组件实例上:

```js
<bootstrap-date-input data-date-picker="activated"></bootstrap-date-input>
```

**替换/合并已有的Attribute**

想象一下 <bootstrap-date-input> 的模板是这样的:

```html
<input type="date" class="form-control">
```

为了给我们的日期选择器插件定制一个主题，我们可能需要像这样添加一个特别的类名： 

```html
<bootstrap-date-input
  data-date-picker="activated"
  class="date-picker-theme-dark"
></bootstrap-date-input>
```

在这种情况下,我们定义了两个不同的class的值:

- `form-control`，这是在组件的模板内设置好的
- `date-picker-theme-dark`，这是从组件的父级传入的

对于绝大多数attribute 来说 , 从外部提供给组件的值会替换掉组件内部设置好的值 . 所以如果传入 type="text" 就会替换掉 type='date'并把它破坏! 庆幸的是 , class和style attribute 会稍微智能一下 , 即两边的值会被合并起来 , 从而得到最终值: form-control date-picker-theme-dark .

**禁用Attribute 继承**

如果**不**希望组件的根元素继承attribute , 可以在组件的选项中设置 inheritAttrs:false . 例如:

```js
Vue.component('my-component', {
  inheritAttrs: false,
  // ...
})
```

这尤其适合配合实例的 $attrs property 使用 , 该property 包含了传递给一个组件的 attribute 名和  attribute 值 , 例如:

```js
{
  required: true,
  placeholder: 'Enter your username'
}
```

有了 inheritAttrs : false 和 $attrs , 就可以手动决定这些 attribute 会被赋予哪个元素 . 在撰写**基础组件**的时候是常会用到的:

```js
Vue.component('base-input', {
  inheritAttrs: false,
  props: ['label', 'value'],
  template: `
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      >
    </label>
  `
})
```

Warning: inheritAttrs:false 选项**不**会影响 style 和 class 的绑定.

这个模式允许你在使用基础组件的时候更像是使用原始的 HTML 元素，而不会担心哪个元素是真正的根元素： 

```js
<base-input
  label="Username:"
  v-model="username"
  required
  placeholder="Enter your username"
></base-input>
```

