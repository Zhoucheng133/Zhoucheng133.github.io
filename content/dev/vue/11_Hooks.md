---
title: "Hooks"
weight: 12
type: docs
---
可以使用外部的TypeScript或者JavaScript

```vue
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
