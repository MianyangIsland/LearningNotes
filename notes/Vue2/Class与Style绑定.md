## Class与Style绑定

操作元素的class列表和内联样式是数据绑定的一个常见需求.因为它们都是attribute,所以我们可以用v-bind处理它们: 只需要通过表达式计算出字符串结果即可.不过,字符串拼接麻烦且易错.因此,在将v-bind用于class和style时,Vue.js做了专门的增强.表达式结果的类型除了字符串之外,还可以是对象或数组.

**绑定HTML Class**

**对象语法**

我们可以传给v-bind:class 一个对象 , 以动态地切换class:

```html
<div v-bind:class="{ active: isActive }"></div>
```

上面的语法表示 `active` 这个 class 存在与否将取决于数据 property `isActive` 的真值。 

可以在对象中传入更多字段来动态切换多个class.此外,v-bind:class指令也可以与普通地class attribue共存.当有如下模板:

```js
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>
```

和如下data:

```js
data: {
  isActive: true,
  hasError: false
}
```

结果渲染为:

```html
<div class="static active"></div>
```

绑定地数据对象不必内联定义在模板里:

```html
<div v-bind:class="classObject"></div>
```

```js
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

渲染地结果和上面一样.我们也可以在这里绑定一个返回对象地**计算属性**.这是一个常用且强大的模式:

```html
<div v-bind:class="classObject"></div>
```

```js
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

**数组语法**

可以把一个数组传给 v-bind:class , 以应用一个class列表:

```html
<div v-bind:class="[activeClass, errorClass]"></div>
```

```js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

渲染为:

```js
<div class="active text-danger"></div>
```

如果也想根据条件切换列表中的class, 可以用三元表达式:

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

不过,当有多个条件class时这样写有些繁琐.所以在数组语法中也可以使用对象语法:

```js
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

**绑定内联样式**

**对象语法**

v-bind:style 的对象语法十分直观 --  看着非常像CSS , 但其实是一个JavaScript对象.CSS property名可以用驼峰式或短横线分隔(kebab-case,记得用引号括起来)来命名:

```html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

```js
data: {
  activeColor: 'red',
  fontSize: 30
}
```

直接绑定到一个样式对象通常更好,这会让模板跟清晰:

```html
<div v-bind:style="styleObject"></div>
```

```js
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

**数组语法**

v-bind:style 的数组语法可以将多个样式对象应用到同一个元素上:

```html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

**自动添加前缀**

当 v-bind: style 使用需要添加**浏览器引擎前缀**的CSS property 时, 如 transform , Vue.js会自动给侦测并添加相应的前缀.

**多重值**

从2.3.0 起你可以为style绑定中的 property提供一个包含多个值的数组,常用于提供多个带前缀的值,例如:

```html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

这样写只会渲染数组中最后一个被浏览器支持的值.

