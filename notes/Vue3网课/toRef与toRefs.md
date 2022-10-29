## 10.toRef

作用：创建一个ref对象，其value值指向另一个对象中的某个属性.

语法：const name = toRef(person,'name');

应用:要将响应式对象中的某个属性单独提供给外部使用时.

扩展：toRef与toRef功能一致，但可以批量创建多个ref对象，语法:`toRef(person)`;

eg:

```vue
<template>
  <div class="hello">
      <h4>{{person}}</h4>
      <h2>{{name}}</h2>
      <h2>{{age}}</h2>
      <h2>{{job.j1.salary}}K</h2>
      <button @click="name+='~'">修改名字</button>
       <button @click="age++">修改年龄</button>
        <button @click="job.j1.salary++">涨薪</button>
  </div>
</template>

<script>
import {reactive , toRefs} from 'vue';
export default {
  name: 'HelloWorld',
  setup(){
    let person = reactive({
      name:'张珂',
      age:20,
      job:{
        j1:{
          salary:20
        }
      }
    })

   return{
     person,
     ...toRefs(person)
   }
  },
  props: {
  
  },
  
}
</script>


<style scoped>

</style>

```

