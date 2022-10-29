## 计算属性与监视

**1.computed函数**

与vue2.x中computed配置功能一致.

写法:

```vue
import {computed} from 'vue'
setup(){
    ...
    //计算属性-简写
    let fullName = computed(()=>{
        return person.firstName + person.lastName;
    })
    
    //计算属性-完整
    let fullName = computed(()=>{
        get(){
            return person.firstName + person.lastName;
        },
        set(value){
            const nameArr = value.split("-")
            person.firstName = nameArr[0];
            person.lastName = nameArr[1];
        }
    })
}
```

**2.watch监视的问题**

与Vue2.x中watch配置功能一致.

两个小坑: 

  监视reactive定义的响应式数据时：oldValue无法正确获取，强制开启了深度监视（deep配置失效).

 监视reactive定义的响应式数据中的某个属性(对象)时：deep配置有效.

eg:

```vue
<template>
    <div>
        <h2>当前求和为: {{sum}}</h2>
        <button @click="sum++">点我+1</button>
        <hr/>
        <h2>当前的信息为: {{msg}}</h2>
        <button @click="msg+='~'">修改当前信息</button>
        <hr/>
        <h2>姓名: {{person.name}}</h2>
        <h2>年龄: {{person.age}}</h2>
        <h2>薪资： {{person.job.j1.salary}}K</h2>
        <button @click="person.name+='!'">修改姓名</button>
        <button @click="person.age++">修改年龄</button>
        <button @click="person.job.j1.salary++">涨薪</button>
    </div>
</template>

<script>
import {reactive, ref , watch} from 'vue'
export default {
    name:'Demo',
    setup(){
        let sum = ref(0);
        let msg = ref('你好啊');
        let person = reactive({
            name:'张三',
            age:18,
            job:{
                j1:{
                    salary:20
                }
            }
        })
        //情况一：监视ref所定义的响应式数据
        // watch(sum,(newValue,oldValue)=>{
        //    console.log('sum值变了',newValue,oldValue)
        // },{immediate:true})

        //情况二：监视ref所定义的多个响应式数据
        // watch([sum,msg],(newValue,oldValue)=>{
        //      console.log('sum或msg变了',newValue,oldValue)
        // },{immediate:true})

        //情况三: 监视reactive所定义的一个响应式数据,此处无法正确的获取oldValue
        // 注意强制开启了深度监视(deep配置无效)
        // watch(person,(newValue,oldValue)=>{
        //     console.log('person变化了',newValue,oldValue);
        // },{deep:fasle})//此处的配置无效

        //情况四: 监视reactive所定义的一个响应式数据中的某个属性
        // watch(()=>person.age,(newValue,oldValue)=>{
        //     console.log('person的age变化了',newValue,oldValue);
        // })

        //情况五:监视reactive所定义的一个响应式数据中的某些属性
        //  watch([()=>person.age,()=>person.name],(newValue,oldValue)=>{
        //     console.log('person的age或name变化了',newValue,oldValue);
        // })

        //特殊情况:
        watch(()=>person.job,(newValue,oldValue)=>{
          console.log('person中的job变化了,',newValue,oldValue)
        },{deep:true})//此处由于监视的是reactive元素定义的对象中的某个属性(对象)，所以deep配置有效
        return {
            sum,
            msg,
            person
        }
    }
}
</script>

<style scoped>

</style>
```

**3.watchEffect函数**

watch的套路是：既要之命监视的属性，也要指明监视的回调.

watchEffect的套路是：不用指明监视那个属性，监视的回调中用到了哪个属性，那就监视那个属性.

watchEffect有点像computed:

   但computed注重的计算出来的值（回调函数的返回值），所以必须要写返回值.

   而watchEffect更注重的是过程（回调函数的函数体),所以不用写返回值.

```vue
//watchEffect所指定的回调中用到的数据只要发生了变化，则直接重新执行回调.
watchEffect(()=>{
    const x1 = sum.value;
    const x2 = person.age;
    console.log('watchEffect配置的回调执行了!');
})
```