---
title: React 章节
---

# React 章节

本目录收集与 React 有关的笔记、教程与系列文章。主要面向使用函数组件与 Hooks 的现代 React 开发。

## 目录结构

- `react-hooks-series-1-intro.md` — React Hooks 系列（一）：入门与核心概念
- 未来文章（示例）：
  - `react-hooks-series-2-useeffect.md` — 深入 useEffect
  - `react-hooks-series-3-performance.md` — 性能优化（useMemo / useCallback）
  - `react-hooks-series-4-state.md` — 复杂状态管理（useReducer + Context）
- `zustand/` — Zustand 相关文章与示例
  - `zustand/zustand-intro.md` — Zustand 入门：一个轻量的 React 状态管理方案

## 写作模板（Markdown/YAML front-matter）

建议文章以 front-matter 开头，便于静态站点生成器使用。例如：

---
+title: "文章标题"
+date: 2025-10-19
+tags: [react, hooks]
+---

然后撰写正文，适当添加代码块、示例和小结。

## 贡献说明

- 直接在对应目录下创建/编辑 Markdown 文件。
- 使用 `xxx.md` 作占位时请尽快替换为有意义的文件名。
- 提交 PR 时，请在 PR 描述中写明变更目的与预览链接（如果有）。

## 目标读者

本章节面向对 React 有基本了解（例如 JSX、组件概念）并希望深入 Hooks、性能与工程化实践的开发者。


如果你希望我把 README 换成中文/英文双语，或自动生成目录索引（基于当前文件名生成索引列表），我可以继续实现。