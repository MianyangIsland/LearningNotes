## 自定义hook函数

什么是hook? --- 本质是一个函数，把setup函数中使用的Composition API 进行了封装.

类似于vue2.x中的mixin.

自定义hook的优势：复用代码，让setup中的逻辑更清楚易懂.

eg:

src/hooks/usePoint.js文件:

```js
import { onBeforeUnmount, onMounted, reactive } from 'vue';

export default function() {
    let point = reactive({
        x: 0,
        y: 0
    })

    function savePoint(event) {
        point.x = event.pageX;
        point.y = event.pageY;
    }
    onMounted(() => {
        window.addEventListener('click', savePoint);
    })

    onBeforeUnmount(() => {
        window.removeEventListener('click', savePoint);
    })

    return point;
}
```

Demo组件:

```vue
<template>
  <div>
    <h2>当前鼠标在页面上的位置为: x:{{point.x}} y:{{point.y}}</h2>
  </div>
</template>

<script>
import usePoint from '../hooks/usePointe';
export default {
  name: "Demo",
  setup() {
   const point = usePoint();

   return {point}
  },
};
</script>

<style scoped>
</style>
```

