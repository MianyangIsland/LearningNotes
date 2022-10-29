## vuex技术

1. 概念: 专门在Vue中实现集中式状态（数据）管理的一个Vue插件，对vue应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信.
2. 什么时候使用Vuex:
   1. 多个组件依赖同一状态.
   2. 来自不同组件的行为需要变更同一状态.

## Vuex工作原理:

![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9ffcbd2177394e73a198e9ef101a55f9~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp) 

**累加和vuex版本案例:**

scr/store/index.js:

```js
//该文件用于创建Vuex中最为核心的store

//引入Vue
import Vue from 'vue';
//引入Vuex
import Vuex from 'vuex';

//准备actions -- 用于相应组件中的动作.
const actions = {
    addTwo(context, value) {
        if (context.state.sum % 2) {
            context.commit('JIATwo', value);
        }
    },
    addWait(context, value) {
        setTimeout(() => {
            context.commit('JIAWait', value);
        }, 1000)
    }
};

//准备mutations -- 用于操作数据(state)
const mutations = {
    JIA: function(context, value) {
        context.sum += value;
    },
    JIAN: function(context, value) {
        context.sum -= value;
    },
    JIATwo: function(context, value) {
        context.sum += value;
    },
    JIAWait: function(context, value) {
        context.sum += value;
    }
};

//准备state -- 用于存储数据
const state = {
    sum: 0
};

Vue.use(Vuex);

//创建并暴露store
export default new Vuex.Store({
    actions,
    mutations,
    state
})
```

src/main.js:

```js
import Vue from 'vue'
import App from './App.vue'
import store from './store/index';
Vue.config.productionTip = false;

new Vue({
    render: h => h(App),
    store
}).$mount('#app')
```

App.vue:

```vue
<template>
  <div class="App">
   <counter/>
  </div>
</template>

<script>
import Counter from './components/Counter.vue';
export default {
  name: "App",
  data: function () {
    return {
      n:1
    };
  },
  components: {
  Counter
  },
 
};
</script>

<style lang="css">
</style>
```

Counter.vue组件:

```vue
<template>
  <div>
    <h1>当前的数值为{{ $store.state.sum }}</h1>
    <select v-model="n">
      <option :value="1">1</option>
      <option :value="2">2</option>
      <option :value="3">3</option>
    </select>
    <button @click="add">+</button>
    <button @click="jian">-</button>
    <button @click="addTwo">偶数加</button>
    <button @click="addWait">过一会加</button>
  </div>
</template>

<script>
export default {
  name: "Counter",
  data: function () {
    return {
      n: 1,
    };
  },
  methods: {
    add() {
      this.$store.commit("JIA", this.n);
    },
    jian() {
      this.$store.commit("JIAN", this.n);
    },
    addTwo() {
        this.$store.dispatch('addTwo',this.n);
    },
    addWait() {
        this.$store.dispatch('addWait',this.n);
    },
  },
};
</script>

<style scoped>
</style>
```

**getters配置项:**

1. 概念： 当state中的数据需要经过加工后再使用时，可以使用getters加工.

2. 作用: 用于将state中的数据进行加工.

3. 使用：

   在src/store/index.js中配置:

   ```js
   //该文件用于创建Vuex中最为核心的store
   
   //引入Vue
   import Vue from 'vue';
   //引入Vuex
   import Vuex from 'vuex';
   
   //准备actions -- 用于相应组件中的动作.
   const actions = {
       addTwo(context, value) {
           if (context.state.sum % 2) {
               context.commit('JIATwo', value);
           }
       },
       addWait(context, value) {
           setTimeout(() => {
               context.commit('JIAWait', value);
           }, 1000)
       }
   };
   
   //准备mutations -- 用于操作数据(state)
   const mutations = {
       JIA: function(context, value) {
           context.sum += value;
       },
       JIAN: function(context, value) {
           context.sum -= value;
       },
       JIATwo: function(context, value) {
           context.sum += value;
       },
       JIAWait: function(context, value) {
           context.sum += value;
       }
   };
   
   //准备state -- 用于存储数据
   const state = {
       sum: 0
   };
   
   const getters = {
       bigSum(state) {
           return state.sum * 10;
       }
   }
   
   Vue.use(Vuex);
   
   //创建并暴露store
   export default new Vuex.Store({
       actions,
       mutations,
       state,
       getters
   })
   ```

   在Counter中使用:

   ```vue
   <template>
     <div>
       <h1>当前的数值为{{ $store.state.sum }}</h1>
       <h1>当前的数值扩大十倍为: {{$store.getters.bigSum}}</h1>
       <select v-model="n">
         <option :value="1">1</option>
         <option :value="2">2</option>
         <option :value="3">3</option>
       </select>
       <button @click="add">+</button>
       <button @click="jian">-</button>
       <button @click="addTwo">偶数加</button>
       <button @click="addWait">过一会加</button>
     </div>
   </template>
   
   <script>
   export default {
     name: "Counter",
     data: function () {
       return {
         n: 1,
       };
     },
     methods: {
       add() {
         this.$store.commit("JIA", this.n);
       },
       jian() {
         this.$store.commit("JIAN", this.n);
       },
       addTwo() {
           this.$store.dispatch('addTwo',this.n);
       },
       addWait() {
           this.$store.dispatch('addWait',this.n);
       },
     },
   };
   </script>
   
   <style scoped>
   </style>
   ```



**mapState和mapGetter**

1. mapState方法： 用于帮助我们映射state中的数据为计算属性:

2. mapGetters方法： 用于帮助我们映射getters中的数据为计算属性:

3. 用法示例:

   ```js
   //index.js
   //该文件用于创建Vuex中最为核心的store
   
   //引入Vue
   import Vue from 'vue';
   //引入Vuex
   import Vuex from 'vuex';
   
   //准备actions -- 用于相应组件中的动作.
   const actions = {
       addTwo(context, value) {
           if (context.state.sum % 2) {
               context.commit('JIATwo', value);
           }
       },
       addWait(context, value) {
           setTimeout(() => {
               context.commit('JIAWait', value);
           }, 1000)
       }
   };
   
   //准备mutations -- 用于操作数据(state)
   const mutations = {
       JIA: function(context, value) {
           context.sum += value;
       },
       JIAN: function(context, value) {
           context.sum -= value;
       },
       JIATwo: function(context, value) {
           context.sum += value;
       },
       JIAWait: function(context, value) {
           context.sum += value;
       }
   };
   
   //准备state -- 用于存储数据
   const state = {
       sum: 0,
       school: '西安邮电大学',
       subject: 'web前端'
   };
   
   const getters = {
       bigSum(state) {
           return state.sum * 10;
       }
   }
   
   Vue.use(Vuex);
   
   //创建并暴露store
   export default new Vuex.Store({
       actions,
       mutations,
       state,
       getters
   })
   ```

   ```vue
   //Counter.vue 文件中
   <template>
     <div>
       <h1>当前的数值为{{sum }}</h1>
       <h1>当前的数值扩大十倍为: {{bigSum}}</h1>
       <select v-model="n">
         <option :value="1">1</option>
         <option :value="2">2</option>
         <option :value="3">3</option>
       </select>
       <button @click="add">+</button>
       <button @click="jian">-</button>
       <button @click="addTwo">偶数加</button>
       <button @click="addWait">过一会加</button>
       <h2>我在{{school}},学习{{subject}}</h2>
     </div>
   </template>
   
   <script>
   import {mapState,mapGetters} from 'vuex';
   export default {
     name: "Counter",
     data: function () {
       return {
         n: 1,
       };
     },
     methods: {
       add() {
         this.$store.commit("JIA", this.n);
       },
       jian() {
         this.$store.commit("JIAN", this.n);
       },
       addTwo() {
           this.$store.dispatch('addTwo',this.n);
       },
       addWait() {
           this.$store.dispatch('addWait',this.n);
       },
     },
     computed:{
         //借助mapState生成计算属性, sum,school,subject(对象写法)
       //   ...mapState({sum:'sum',school:'school',subject:'subject'}),
   
         //借助mapState生成计算属性, sum,school,subject(数组写法)
         ...mapState(['sum','school','subject']),
   
         //借助mapGetters生成计算属性，bigSum(对象写法)
         ...mapGetters({bigSum:'bigSum'}),
   
         //借助mapGetters生成计算属性，bigSum(数组写法)
       //   ...mapGetters('bigSum')
   
     }
   };
   </script>
   
   <style scoped>
   </style>
   ```

   

   



**mapActions 与 mapMutations**

1. mapActions方法： 用于帮助我们生成与actions 对话的方法 ，即：包含#store.dispatch(xxx)的函数.

2. mapMutations方法： 用于帮助我们生成与mutations对话的方法，即：包含$store.commit(xxx)的函数:

3. 用法示例:

   

   

   

   

**Vuex的模块化**:

**模块化 + 命名空间**

1. 目的： 让代理更好维护，让多种数据分类更加明确.

2. 修改store.js:

   ```js
   const countAbount = {
       namespaced:true,//开启命名空间
       state:{x:1},
       mutations:{.....},
       actions:{.....},
       getters:{
           bigSum(state){
               return state.sum * 10
           }
       }
   }
   
   const personAbout = {
       namespaced:true,//开启命名空间
       state:{.....},
       mutations:{.....},
       actions:{...}
   }
   
   const store = new Vuex.Store({
       modules:{
           countAbout,
           personAbout
       }
   })
   ```

3. 开启命名空间后，组件中读取state数据:

   ```js
   //方式一: 自己直接读取
   this.$store.state.personAount.list
   //方式二： 借助mapState读取:
   ...mapState('countAbout',['bigSum'])
   ```

4. 开启命名空间后，组件中读取getters数据:

   ```js
   //方式一：自己直接读取
   this.$store.getters['personAbout/firstPersonName']
   //方式二:借助mapGetters读取:
   ...mapGetters('countAbout',['bigSum'])
   ```

5. 开启命名空间后，组件中调用dispatch

   ```js
   //方式一：自己直接dispatch
   this.$store.dispatch('personAbout/addPersonWang',person);
   //方式二: 借助mapActions:
   ...mapActions('countAbout',{increment:'jiaOdd',increment:'jiaWait'});
   ```

6. 开启命名空间后,组件中调用commit:

   ```js
   //方式一：自己直接commit
   this.$store.commit('personAbout/ADD_PERSON',person);
   //方式二:
   ...mapMutations('countAbout',{incremnt:'JIA',decrement:'JIAN'})
   ```

   

使用示例:

/store/countAbout.js:

```js
export default {
    namespaced: true,
    actions: {
        addTwo(context, value) {
            if (context.state.sum % 2) {
                context.commit('JIATwo', value);
            }
        },
        addWait(context, value) {
            setTimeout(() => {
                context.commit('JIAWait', value);
            }, 1000)
        }
    },
    mutations: {
        JIA: function(context, value) {
            context.sum += value;
        },
        JIAN: function(context, value) {
            context.sum -= value;
        },
        JIATwo: function(context, value) {
            context.sum += value;
        },
        JIAWait: function(context, value) {
            context.sum += value;
        }
    },
    state: {
        sum: 0,
        school: '西安邮电大学',
        subject: 'web前端'
    },
    getters: {
        bigSum(state) {
            return state.sum * 10;
        }
    }
}
```

/src/store/personAbout.js:

```js
import { nanoid } from "nanoid";
export default {
    namespaced: true,
    actions: {
        addWang(context, value) {
            if (value.indexOf('王') === 0) {
                let obj = { id: nanoid(), name: value }
                context.commit('ADDPerson', obj)
            } else {
                alert('必须姓王!');
            }
        }
    },
    mutations: {
        ADDPerson(state, value) {
            state.personList.unshift(value);
        }
    },
    state: {
        personList: [
            { id: '001', name: '张珂' }
        ]
    },
    getters: {
        firstPersonName(state) {
            return state.personList[0].name;
        }
    }
}
```

store/index.js:

```js
//该文件用于创建Vuex中最为核心的store

//引入Vue
import Vue from 'vue';
//引入Vuex
import Vuex from 'vuex';

import personAbout from './PersonAbout';
import countAbout from './countAbout';



Vue.use(Vuex);

//创建并暴露store
export default new Vuex.Store({
    modules: {
        personAbout,
        countAbout
    }
})
```

count.vue:

```vue
<template>
  <div>
    <h1>当前的数值为{{sum }}</h1>
    <h1>当前的数值扩大十倍为: {{bigSum}}</h1>
    <select v-model="n">
      <option :value="1">1</option>
      <option :value="2">2</option>
      <option :value="3">3</option>
    </select>
    <button @click="increment(n)">+</button>
    <button @click="decrement(n)">-</button>
    <button @click="incrementOdd(n)">偶数加</button>
    <button @click="decrementWait(n)">过一会加</button>
    <h2>我在{{school}},学习{{subject}}</h2>
    <h1 style="color:red">Person组件内的当前人数为{{personList.length}}</h1>
  </div>
</template>

<script>
import {mapState,mapGetters,mapMutations,mapActions} from 'vuex';
export default {
  name: "Counter",
  data: function () {
    return {
      n: 1,
    };
  },
  methods: {
   
    ...mapMutations('countAbout',{increment:'JIA',decrement:'JIAN'}),
     
    ...mapActions('countAbout',{incrementOdd:'addTwo',decrementWait:'addWait'})
  },
  computed:{
      //借助mapState生成计算属性, sum,school,subject(对象写法)
      ...mapState('countAbout',{sum:'sum',school:'school',subject:'subject'}),


      //借助mapGetters生成计算属性，bigSum(对象写法)
      ...mapGetters('countAbout',{bigSum:'bigSum'}),

     ...mapState('personAbout',{personList:'personList'})
  }
};
</script>

<style scoped>
</style>
```

person.vue:

```vue
<template>
    <div>
        <h1 style="color:red">sum组件内当前的和为{{sum}}</h1>
        <h1 >第一个人的名字为{{firstPersonName}}</h1>
        <input type="text" v-model="name"/> <button @click="addPerson">添加当前的人</button>
        <button @click="addWang(name)">点击添加王姓男子</button>
        <br/>
        <ul>
            <li v-for="p in personList" :key="p.id">{{p.name}}</li>
        </ul>
    </div>
</template>

<script>
import {mapState,mapGetters,mapActions,mapMutations} from 'vuex';
import {nanoid} from 'nanoid';

export default {
    name:'Person',
    data:function(){
        return {
          name:''
        }
    },
    computed:{
      ...mapState('countAbout',{sum:'sum'}),
      ...mapState('personAbout',{personList:'personList'}),
      ...mapGetters('personAbout',{firstPersonName:'firstPersonName'})
    },
    methods:{
       addPerson(){
           let name = this.name;
           let obj = {id:nanoid(),name};
           this.$store.commit('personAbout/ADDPerson',obj);
           this.name = '';
       },
       ...mapActions('personAbout',{addWang:'addWang'})
    }
}
</script>

<style scoped>

</style>
```

