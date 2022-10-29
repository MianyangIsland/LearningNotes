**一 .shallowReactive与shallowRef**

shallowReatcive :只处理对象最外层属性的响应式（浅响应式).

shallowRef : 只处理基本数据类型的响应式，不进行对象的响应式处理.

什么时候使用? 

   如果有一个对象数据，结构比较深，但变化时只是最外层属性变化 ===> shallowRractive.

如果有一个对象，后续功能不会修改对象中的属性，而是新的对象来替换 ===> shallowRef.

**二.readonly 与 shallowReadonly**

readonly:让一个响应式数据变为只读的（深只读).

shallowReadonly:让一个响应式数据变为只读的（浅只读).

应用场景: 不希望数据被修改时.



**三.toRaw与markRaw**

toRaw: 

  作用： 将一个由reactive生成的响应式对象转为普通对象.

 使用场景： 用于读取响应式对象对应的普通对象，对这个普通对象的所有操作，不会引起页面更新.

markRaw:

 作用： 标记一个对象，使其永远不会再成为响应式对象.

应用场景： 

1. 有些值不应被设置为响应式的，例如复杂的第三方类库等.
2. 当渲染具有不可变数据源的大列表时，跳过响应式转换可以提高性能.

**四.customRef(自定义Ref)**

作用：创建一个自定义的ref , 并对其依赖项跟踪和更新触发进行显示控制.

eg:

```vue
<template>
  <div class="hello">
     <input type="text" v-model="keyWord" />
     <h3>{{keyWord}}</h3>
  </div>
</template>

<script>
import {customRef} from 'vue';
export default {
  name: 'HelloWorld',
  setup(){
   
   //customer实现防抖
   function myRef(value,delay)
    {
       let timer ;
      return customRef((track,tigger) => {
        return{
          get(){
            console.log(`有人从myRef这个容器中读取数据了，我把${value}给他了`);
            track()//通知Vue追踪value的辩护（提前和het商量一下，让他认为这个value值是有用的)
            return value;
          },
          set(newValue){
            console.log(`有人把myRef这个容器中数据改为了: ${newValue}`);
            clearTimeout(timer);
            timer = setTimeout(()=>{
              value = newValue;
              tigger() //通知Vue去重新解析模板
            },delay)
          }
        }
      })
    }

      //let keyWord = ref('hello')
      let keyWord = myRef('hello',1000);//使用程序员自定义的Ref

    return {
      keyWord
    }
  },
  props: {
  
  },
  
}
</script>


<style scoped>

</style>
```

**五.provide与inject**

作用：实现祖孙组件间通信.

套路：父组件有一个`provide`选项来提供数据，子组件有一个`inject`选项来开始使用这些数据

具体写法:

1. 祖组件中:

   ```vue
   setup(){
       ......
       let car = reactive({name:'奔驰',price:'40万'});
       provide('car',car);
   }
   ```

2. 孙组件中:

   ```vue
   setup(props,conext){
       .....
       const car = inject('car');
       return {car};
   }
   ```

**六.响应式数据的判断**

isRef:检查一个值是否为一个ref对象.

isReactive:检查一个对象是否由reactive创建的响应式处理.

isReadonly:检查一个对象是否由readonly创建的只读代理.

isProxy:检查一个对象是否是由reactive或者readonly方法创建的代理.

**七.Composition API的优势**

1. Options API存在的问题:

   使用传统OptionsAPI中，新增或修改一个需求，就需要分别在data,methods,computed里修改.

2. Composition API的优势

   我们可以更加优雅的组织我们的代码，函数。让相关功能的代码更加有序的组织在一起.(借助hooks实现).



