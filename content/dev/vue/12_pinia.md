---
title: "pinia"
weight: 13
type: docs
---
## 与Hook区别

Pinia是一个状态管理器，其效果类似于Vue3的`Hook`，但是与`Hook`不同的是，如果不同的Vue文件调用一个`Hook`文件，那么其会存在不同的变量，例如：

```html
<!-- App.vue -->
<template>
  <div>
    {{ num }}
    <button @click="add">测试按钮</button>
  </div>
</template>

<script setup lang="ts">
import useAdd from './hooks/useAdd';

let {num, add}=useAdd();

</script>

<style>
</style>
```

```ts
// useAdd.js
import { ref } from "vue";

export default function(){
  let num=ref(0);

  const add=()=>{
    num.value+=1;
  }

  return {num, add};
}
```

在另外一个vue文件中同样调用useAdd这个文件时

```html
<!-- ChildView.vue -->
<template>
  <div>
    {{ num }}
  </div>
</template>

<script setup lang="ts">
import useAdd from './hooks/useAdd';

let {num, add}=useAdd();

</script>
```

其中的变量`num`并不会随着`App.vue`中调用`add`而增加，可以说虽然调用的是同一个文件，变量名称一样，但是实际的变量都是各自独立的，而Pinia就是为了解决这个问题的

## 安装Pinia

使用包管理器安装

```bash
yarn add pinia
# 或者使用 npm
npm install pinia
```

导入到`main.js`或者`main.ts`

```ts
import { createApp } from 'vue';
import { createPinia } from 'pinia'
import App from './App.vue';

const pinia = createPinia()

createApp(App).use(pinia).mount('#app')
```

## 使用Pinia

需要新建一个`store`文件，用于存放状态（语法糖）：

```ts
import { defineStore } from "pinia";
import { ref } from "vue";
export default defineStore("main", ()=>{
  let count=ref(0);
  return { count };
})
```

<details>
<summary>完整的写法</summary>

```ts
import { defineStore } from "pinia";
export default defineStore("main", {
  actions: {
    // 动作函数
    add(){
      this.count+=1;
    }
  },
  state(){
    // 状态
    return {
      count: 0,
    }
  },
  getters: {
    // 计算属性
    bigSun():number{
      return this.sum*10
    }
  }
})
```

</details>

在页面中使用时

```html
<!-- App.vue -->
<template>
  <div>
    {{ mainStore.count }}
    <button @click="add">Add</button>
  </div>
</template>

<script setup lang="ts">
import main from "./stores/main";
const mainStore=main();
const add=()=>{
  mainStore.count+=1;
  // 或者
  mainStore.$patch({
    count: mainStore.count+1
  })
  // 或者
  mainStore.add();
}
</script>
```

也可以这么使用

```html
<!-- App.vue -->
<template>
  <div>
    {{ count }}
    <button @click="add">Add</button>
  </div>
</template>

<script setup lang="ts">
import main from "./stores/main";
import { storeToRefs } from "pinia";

const mainStore=main();
const { count } = storeToRefs(mainStore);
const add=()=>{
  count+=1;
}
count.$subscribe((mutate, state)=>{
  // 监听变化
})
</script>
```

## 注意

```ts
// store.main.ts
import { defineStore } from "pinia";
import { ref } from "vue";
export default defineStore("main", ()=>{
  let count=ref(0);
  // 如果是响应式的，不要直接赋值
  // 错误写法:
  // let count=0
})
```

```ts
// App.vue
import main from "./stores/main";

// 可以正确执行
const adder_1=()=>{
  main().count+=1;
}

// 错误操作
let { count }=main();
const adder_2=()=>{
  count+=1;
}

// 也可以这么写
let { count }=storeToRefs(main());
const adder_2=()=>{
  count+=1
}
```
