---
title: Zustand 入门：一个轻量的 React 状态管理方案
---

# Zustand 入门

Zustand 是一个小巧但功能强大的状态管理库，适用于需要比 Context 小巧但又不想引入 Redux 之类复杂库的场景。本文件将介绍基本用法与常见模式。

## 快速开始

```bash
npm install zustand
```

```jsx
import create from 'zustand'

const useStore = create(set => ({
  count: 0,
  inc: () => set(state => ({ count: state.count + 1 })),
}))

function Counter() {
  const { count, inc } = useStore()
  return <button onClick={inc}>{count}</button>
}
```

## 优点

- API 极简：通过 `create` 创建 store，并在组件中直接读取/订阅。
- 性能好：只会触发使用到切片（slice）的组件重渲染。
- 支持中间件、持久化与跨标签页同步等扩展。

## 进阶话题（后续文章）

- 选择器与浅比较（`useStore(state => state.count)` vs 取整个对象）
- 中间件：持久化（persist）、devtools
- 与 Context / Redux 的比较与迁移策略

（这是入门草稿。需要我为这篇写更完整的示例、测试或对比表格吗？）