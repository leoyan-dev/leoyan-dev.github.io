---
title: React Hooks 系列（一）：入门与核心概念
---

# React Hooks 系列（一）：入门与核心概念

这是关于 React Hooks 的系列文章的第一篇。目标是带你从零建立 Hooks 的直观理解，掌握最常用的内建 Hook（useState、useEffect、useRef 等）、遵循 Hook 规则，以及通过小例子上手写自定义 Hook。

本文的结构：

- 为什么要使用 Hooks
- Hook 的基本规则
- useState：状态在函数组件里的管理
- useEffect：副作用与生命周期替代
- useRef：保持可变值与 DOM 引用
- 自定义 Hook 简单示例
- 常见误区与调试技巧
- 接下来（系列计划）

---

## 为什么要使用 Hooks

在 React 出现 Hooks 之前，组件状态与副作用逻辑往往分布在类组件的生命周期方法中。Hooks 提供了在函数组件中使用状态和其他 React 特性的能力，带来几个好处：

- 更简洁：函数组件 + Hook 通常比等价的类组件更短、更直观。
- 更可组合：通过自定义 Hook，让逻辑以函数的形式被复用和测试。
- 更符合现代 JS 风格：把逻辑拆成小块，像使用普通函数一样组合它们。

Hooks 并不是要替代类组件的所有用途（类仍然可用），但它是推荐的方式，尤其是新项目和新功能。

## Hook 的基本规则（非常重要）

1. 只在 React 的最顶层调用 Hook（不要在循环、条件或嵌套函数中调用）。
2. 只在 React 函数组件或自定义 Hook 中调用 Hook。

违反这些规则会导致 Hook 的内部状态在渲染之间错位，出现难以排查的 bug。React 官方提供 eslint 插件（eslint-plugin-react-hooks）来帮助自动检测。

## useState：函数组件的本地状态

useState 的基本用法：

```jsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>当前计数：{count}</p>
      <button onClick={() => setCount(c => c + 1)}>+1</button>
    </div>
  );
}
```

要点：

- 初始值可以是任何类型（数字、对象、数组等），也可以是惰性初始化函数：`useState(() => expensiveInit())`。
- setState 支持传入 updater 函数（推荐在基于上一个状态更新时使用）：`setCount(c => c + 1)`。

当状态是对象或数组时，请注意不要直接修改原对象，而应创建新引用：

```js
// 正确：
setUser(u => ({ ...u, name: 'New' }));

// 错误（会直接修改原对象，React 可能无法检测变化）：
user.name = 'New';
setUser(user);
```

## useEffect：副作用与替代生命周期

useEffect 用来处理网络请求、订阅、手动 DOM 操作和清理等副作用。它会在渲染后执行：

```jsx
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    let cancelled = false;

    fetch(`/api/users/${userId}`)
      .then(r => r.json())
      .then(data => {
        if (!cancelled) setUser(data);
      });

    return () => {
      // 清理逻辑：避免在组件卸载后仍尝试 setState
      cancelled = true;
    };
  }, [userId]); // 依赖数组：当 userId 变化时重新执行 effect

  if (!user) return <div>加载中…</div>;
  return <div>{user.name}</div>;
}
```

要点：

- 依赖数组决定何时重新运行 effect：空数组 `[]` 表示只在挂载和卸载时运行一次；没有数组则每次渲染都运行。
- effect 内可以返回一个清理函数，用于取消订阅或中断异步操作。
- 把 effect 写得尽量单一职责（每个 effect 处理一类副作用），便于管理依赖。

常见问题：闭包陷阱。例如在 effect 中使用过期的 state/props。解决办法通常是把所有依赖都放入依赖数组，或者在必要时使用 `useRef` 保存最新引用。

## useRef：可变的容器与 DOM 引用

useRef 返回一个可变的 ref 对象：`{ current: ... }`，更新 `ref.current` 不会触发重新渲染。常见用途：

- 存储 DOM 元素引用：`const el = useRef(null); <div ref={el} />`
- 持有跨渲染周期的可变值（比如定时器 ID 或最新值），而不引起渲染

示例：保存上一次的值（会在后面自定义 Hook 中复用）：

```jsx
import { useRef, useEffect } from 'react';

function usePrevious(value) {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  });
  return ref.current;
}

export default usePrevious;
```

## 自定义 Hook（一个简单示例）

自定义 Hook 本质上是一个以 `use` 开头的函数，内部可以组合多个 Hook 来封装逻辑。

示例：节流或防抖 Hook（极简版防抖）：

```jsx
import { useRef, useCallback } from 'react';

function useDebouncedCallback(fn, delay) {
  const timer = useRef(null);

  const debounced = useCallback((...args) => {
    if (timer.current) clearTimeout(timer.current);
    timer.current = setTimeout(() => fn(...args), delay);
  }, [fn, delay]);

  return debounced;
}

export { useDebouncedCallback };
```

使用自定义 Hook 可以把组件中的副作用/事件处理逻辑抽离、复用并单独测试。

## 常见误区与调试技巧

- 忘记把依赖放到依赖数组：会导致 effect 用到的是旧值或不会按预期更新。
- 在条件语句中调用 Hook：会破坏 Hook 的调用顺序。
- 频繁创建内联函数或对象（例如在 render 中每次都 new 一个对象作为 prop）会导致子组件不必要的重渲染；可用 `useCallback` / `useMemo` 在必要时缓存。

调试建议：

- 启用 React DevTools，观察组件渲染与 Hook 状态。
- 使用 eslint-plugin-react-hooks 检查依赖和规则违规。
- 把复杂 effect 拆分成多个小 effect，有助于定位问题。

## 小结与下一步

这一篇覆盖了 Hooks 的入门与核心概念。下一篇我计划：

- 系列（二）：深入 useEffect（常见模式、取消、race condition 与最佳实践）
- 系列（三）：性能优化（useMemo、useCallback、避免不必要渲染）
- 系列（四）：复杂状态管理（useReducer + context，替代部分 Redux 场景）

如果你希望我现在把文章直接写成带 front-matter 的博客模板（例如 Jekyll/Hugo/Next/MDX 的格式），或想我把内容翻译为英文、加入更多示例/测试代码，告诉我你希望的输出格式和细节，我会继续按你的要求更新。

---

文件路径：`fullstack/react/react-hooks-series-1-intro.md`
