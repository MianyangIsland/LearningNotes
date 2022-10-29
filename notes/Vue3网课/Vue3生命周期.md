## Vue3生命周期

Vue3.0中可以继续使用Vue2.x中的生命周期钩子，但有两个被更名:

 beforeDestory改名为beforeUnmount

destoryed改名为unmounted.

Vue3.0也提供了Composition API形式的生命周期钩子，与Vue2.x中钩子对应关系如下:

beforeCreate ===> setup()

created ===> setup()

beforeMount ===> onBeforeMount

mounted ===> onMounted

beforeUpdate ===> onBeforeUpdate

updated ===> onUpdated

beforedUnmounted ===> onBeforeUnmount

unmounted ===> onUnmounted

eg:

```vue
<template>
  <div>
    <h2>当前求和为: {{ sum }}</h2>
    <button @click="sum++">点我+1</button>
    <hr />
  </div>
</template>

<script>
import { onBeforeMount, onBeforeUnmount, onBeforeUpdate, onMounted, onUpdated, reactive, ref, watchEffect } from "vue";
export default {
  name: "Demo",
  setup() {
    let sum = ref(0);

    //通过组合式API的形式去使用生命周期钩子
    onBeforeMount(()=>{
        console.log('-- onBeforeMount--');
    })
    onMounted(()=>{
        console.log('-- onMounted--');
    })
    
    onBeforeUpdate(()=>{
        console.log('-- onBeforeUpdate--');
    })

    onUpdated(()=>{
         console.log('-- onUpdated--');
    })

    onBeforeUnmount(()=>{
        console.log('-- onBeforeUnmount--')
    })
    onMounted(()=>{
         console.log('-- onMounted--')
    })
    return {
      sum,
    };
  },
 /** 
 //通过配置项的形式使用生命周期钩子
//   beforCreate() {
//     console.log("----beforeCreate----");
//   },
//   created() {
//     console.log("----created----");
//   },
//   beforeMount() {
//     console.log("----beforeMount----");
//   },
//   mounted() {
//     console.log("----mounted----");
//   },
//   beforeUpdate() {
//     console.log("---- beforeUpdate----");
//   },
//   updated() {
//     console.log("----  updated----");
//   },
//   beforeUnmount() {
//     console.log("---- beforeUnMount----");
//   },
//   unmounted() {
//     console.log("---- unmounted----");
//   },
*/
};
</script>

<style scoped>
</style>
```

Vue2生命周期图:

![img](https://img-blog.csdnimg.cn/20210225181535652.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyMDUzMw==,size_16,color_FFFFFF,t_70) 

