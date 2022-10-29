## 1.拉开序幕的setup

1.理解： Vue3.0中一个新的配置项，值为一个函数.

2.setup是所有Composition API(组合API)的表演舞台.

3.组件中所用到的：数据，方法等等，均要配置在setup配置中.

4.setup函数的两种返回值:

1. 若返回一个对象，则对象中的属性，方法，在模板中均可以直接使用。（重点关注).
2. 若返回一个渲染函数：则可以自定义渲染内容。（了解）

5.注意点:

1. 尽量不要与Vue2.x配置混用:

   1.Vue2.x配置（data,methods,computed...)中可以访问到setup中的属性，方法.

   2.但在setup中不能访问到vue2.x配置(data,methods,computed...)

   3.如果有重名，setup优先.

2. setup不能是一个async函数，因为返回值不再是return对象，而是promise,模板看不到return对象中的属性.

**2.ref函数**

- 作用：定义一个响应式的数据.

- 语法： const xxx = ref(initValue);

  创建一个包含响应式数据的引用对象(refernece对象，简称ref对象).

  JS中操作数据： xxx.value

  模板中读取数据：不需要.value,直接：<div>{{xxx}}</div>

- 备注:

  接收的数据可以是：基本类型，也可以是对象类型.

  基本类型的数据：响应式依然是靠Object.defineproperty()的get与set完成的.

  对象数据类型：内部`求助`了vue3.0中的一个新函数----reactive函数.

**3.reacttive函数**

- 作用：定义一个对象类型的响应式数据（基本类型不要用它，要用ref函数).
- 语法：`const 代理对象 = reactive(源对象)`接收一个对象（或数据），返回一个代理对象(proxy的示例对象).
- reactive定义的响应式数据是“深层次的”。
- 内部基于ES6的Proxy实现，通过代理对象源对象内部数据进行操作.

reatcive函数使用示例:

```vue
<template>
  <div class="hello">
    <h1>一个人的信息:</h1>
    <h2>学生的姓名叫{{person.name}}</h2>
    <h2>学生的年龄叫{{person.age}}</h2>
    <h2>工作:{{person.job.type}}</h2>
    <h2>薪水: {{person.job.money}}</h2>
    <h2>爱好: {{person.hobby}}</h2>
    <button @click="changeData">修改信息</button>
  </div>
</template>

<script>
import {reactive} from 'vue';
export default {
  name: 'App',
  setup(){
    let person = reactive({
      name:'张珂',
      age:20,
      job:{
        type:'前端开发工程师',
        money:'30k'
      },
      hobby:['抽烟','喝酒','烫头']
    })

    function changeData(){
      person.name = '黄欲烈';
      person.age = 21;
      person.job.type = '高级架构师';
      person.job.money ='60k';
      person.hobby = ['吸毒','赌博','嫖娼'];
    }

    return{
      person,
      changeData
    }
  }
  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
```

**4.回顾Vue2的响应式原理**

实现原理： 

   对象类型: 通过Object.defineProperty()对属性的读取，修改进行拦截（数据劫持).

   数组类型： 通过重写更新数组的一系列方法来实现拦截。（对数组的变更方法进行了包裹).

存在的问题：

  新增属性，删除属性，界面不会更新.

  直接通过数组下标修改数组，界面不会自动更新.

```vue
<template>
  <div>
   <h1>我是Vue2写的效果</h1>
   <h2 v-show="person.name">姓名： {{person.name}}</h2>
   <h2>年龄: {{person.age}}</h2>
   <h2 v-show="person.sex">性别:{{person.sex}}</h2>
   <h2>爱好: {{person.hobby}}</h2>
   <button @click="addSex">添加性别</button>
   <button @click="deleteName">删除name属性</button>
   <button @click="updateHobby">修改爱好的第一个属性</button>
  </div>
</template>

<script>
// import Vue from 'vue';
export default {
  name:'App',
  data:function(){
    return{
      person:{
        name:'张珂',
        age:20,
        hobby:['吃饭','学习']
      }
    }
  },
  methods:{
    addSex(){
      // this.person.sex = '男';
      //解决办法
      // this.$set(this.person,'sex','男');
      // Vue.$set(this.person,'sex','男');
    },
    deleteName(){
      // delete this.person.name;

      //解决办法
      // this.$delete(this.person,'name');
      // Vue.$delete(this.person,'name');
    },
    updateHobby(){
      // this.person.hobby[0] = '逛街'

      // this.$set(this.person.hobby,0,'逛街');
      // this.person.hobby.splice(0,1,'逛街');
    }
  }
}
</script>

<style scoped>

</style>
```

**5.Vue3.0响应式原理**

实现原理：

- 通过Proxy(代理)：拦截对象中任意属性的变化，包括：属性值的读写，属性的添加，属性的删除等等.

- 通过Reflect(反射)：对被代理对象的属性进行操作.

- 代码示例:

  ```js
  new Proxy(data,{
      //拦截读取属性
      get(target,prop){
          return Reflect.get(target,prop);
      },
      //拦截设置属性值或添加新属性
      set(target,prop,value){
          return Reflect.set(target,prop,value);
      },
      //拦截删除属性
      deleteProperty(target,prop){
          return Reflect.deleteProperty(target,prop)
      }
  })
  ```

网课代码笔记:

```js
    <script>
        let person = {
            name: '张三',
            age: 18
        }

        //     // 模拟Vue2中实现响应
        // let p = {};
        // Object.defineProperty(p, 'name', {
        //     configurable: true,
        //     get() { //有人读取name时调用
        //         return person.name;
        //     },
        //     set(value) {
        //         console.log('有人修改了name属性，我发现了，我要去更新页面!');
        //         person.name = value;
        //     }
        // })
        // Object.defineProperty(p, 'age', {
        //         configurable: true,
        //         get() { //有人读取age时调用
        //             return person.age;
        //         },
        //         set(value) {
        //             console.log('有人修改了age属性，我发现了，我要去更新页面!');
        //             person.age = value;
        //         }
        //     })

        //模拟Vue中的响应式

        // const p = new Proxy(person, {
        //     //有人读取p的某个属性时调用
        //     get(target, propName) {
        //         console.log(`有人读取了p身上的${propName}属性`);
        //         return target[propName];
        //     },
        //     //有人修改p的某个属性，或给p追加某个属性时调用
        //     set(target, propName, value) {
        //         console.log(`有人修改了p身上的${propName}属性，我要去更新页面了！`);
        //         target[propName] = value;
        //     },
        //     deleteProperty(target, propName) {
        //         console.log(`有人删除了p身上的${propName}属性，我要去更界面了!`);
        //         return delete target[propName];
        //     }
        // })


        const p = new Proxy(person, {
            //有人读取p的某个属性时调用
            get(target, propName) {
                console.log(`有人读取了p身上的${propName}属性`);
                return Reflect.get(target, propName);
            },
            //有人修改p的某个属性，或给p追加某个属性时调用
            set(target, propName, value) {
                console.log(`有人修改了p身上的${propName}属性，我要去更新页面了！`);
                Reflect.set(target, propName, value);
            },
            deleteProperty(target, propName) {
                console.log(`有人删除了p身上的${propName}属性，我要去更界面了!`);
                return Reflect.deleteProperty(target, propName);
            }
        })
    </script>
```

**6.reactive对比ref**

- 从定义数据角度对比:

  ref用来定义：基本数据类型.

  reactive用来定义： 对象（或数组）类型数据.

  备注：ref也可以用来定义对象（或数组）类型数据，它内部会自动通过`reactive`转为代理对象.

- 从原理角度对比:

  ref通过Object.defineProperty()的get与set来实现响应式.

  reactive通过使用`proxy`来实现响应式（数据劫持),并通过Reflect操作**源对象**内部的数据.

- 从使用角度对比:

  ref定义的数据：操作数据需要.value,读取数据时模板内直接读取不需要.value.

  reactive定义的数据：操作数据与读取数据：均不需要.value.



**7.setup的两个注意点**

setup执行的时机

 在beforeCreate之前执行一次，this是undefined.

setup的参数：

 props:值为对象，包含:组件外部传递过来，且组件内部声明接收了的属性.

context: 上下文对象.

​     attrs:值为对象，包含：组件外部传递过来，但没有在props配置中声明的属性，相当于this.$attrs.

   slots:收到的插槽内容，相当于this.$slots.

   emit:分发自定义事件的函数，相当于this.$emit.





