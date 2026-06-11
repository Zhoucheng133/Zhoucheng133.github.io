---
title: "Jotai"
weight: 13
type: docs
---

## 安装

```bash
npm install jotai
# 如果使用yarn
# yarn add jotai
```

## 使用

### 创建一个原子

```ts
import { atom } from 'jotai';

export const countAtom = atom(0);
export const textAtom = atom('Hello Jotai');
```

### 在Hook内使用

```tsx
import React from 'react';
import { useAtom } from 'jotai';
import { countAtom } from './store';

export function Counter() {
  const [count, setCount] = useAtom(countAtom);

  return (
    <div>
      <p>当前计数: {count}</p>
      <button onClick={() => setCount((c) => c + 1)}>加 1</button>
      <button onClick={() => setCount(0)}>重置</button>
    </div>
  );
}
```

### 在Hook外使用

```tsx
import { getDefaultStore } from 'jotai';
import { countAtom } from './store';

const store = getDefaultStore();

export function logCount() {
  const count = store.get(countAtom);
  console.log(count);
}

export function setCount(count: number) {
  store.set(countAtom, count);
}
```