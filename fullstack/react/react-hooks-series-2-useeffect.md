---
title: React Hooks 系列（二）：深入 useEffect
---

# React Hooks 系列（二）：深入 useEffect

本篇深入讲解 `useEffect` 的常见使用模式、取消/清理、竞争条件（race conditions）以及如何避免闭包带来的问题。

## 话题纲要

- useEffect 的执行时机回顾
- 清理（cleanup）模式：订阅、定时器、取消请求
- 处理异步请求与 race condition
- 闭包陷阱与最新值访问模式
- 使用自定义 Hook 抽离复杂 effect

（本文为草稿；如需更多示例或把某段示例移到可运行 demo，请告诉我。）