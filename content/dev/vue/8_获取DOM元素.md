---
title: "获取DOM元素"
weight: 9
type: docs
---
```vue
<template>
  <div>
    <button @click="logElement">Log Element</button>
    <div ref="myElement">This is the element</div>
  </div>
</template>

<script setup>

import { ref } from 'vue';

// 注意，这里的变量名称应该和ref后面的命名相同
const myElement = ref(null);

// 注意，因为是通过ref获取到的变量，实际值应该加上.value
function logElement() {
  console.log(myElement.value);
}

</script>

```
