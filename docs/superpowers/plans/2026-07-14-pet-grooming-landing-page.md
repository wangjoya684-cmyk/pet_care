# 宠物洗护店单页 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 构建一个温暖治愈、以预约转化为核心、可离线打开的中文宠物洗护店单页。

**Architecture:** 仅创建一个 `index.html`，语义化 HTML 负责内容结构，内嵌 CSS 负责视觉与响应式，内嵌原生 JavaScript 负责日期限制、表单校验和成功反馈。页面不依赖网络或构建工具。

**Tech Stack:** HTML5、CSS3、原生 JavaScript

## Global Constraints

- CSS 与 JavaScript 全部内嵌于 `index.html`。
- 不依赖外部字体、图片、库、构建工具或后端服务。
- 预约信息不发送、不保存、不持久化。
- 页面语言为简体中文，支持键盘操作与减少动态效果设置。
- 桌面端与移动端均不得出现横向溢出。

---

### Task 1: 页面结构与品牌视觉

**Files:**
- Create: `index.html`

**Interfaces:**
- Consumes: 无。
- Produces: `#services`、`#process`、`#reviews`、`#booking` 四个导航锚点，以及 `#booking-form` 表单。

- [ ] **Step 1: 建立语义结构**

创建 `header`、`main`、八个内容区块和 `footer`；导航链接分别指向四个固定锚点，所有“立即预约”链接指向 `#booking`。

- [ ] **Step 2: 实现视觉系统**

在内嵌 `<style>` 中定义奶油白、焦糖橙、鼠尾草绿变量，完成圆角卡片、按钮、CSS 宠物插画、爪印与泡泡装饰，并为 `:focus-visible` 提供清晰轮廓。

- [ ] **Step 3: 实现响应式规则**

使用 `@media (max-width: 860px)` 将双栏与三栏布局折叠，并使用 `@media (prefers-reduced-motion: reduce)` 关闭平滑滚动及装饰动画。

- [ ] **Step 4: 检查静态结构**

Run: `rg -n "id=\"(services|process|reviews|booking|booking-form)\"|https?://" index.html`

Expected: 五个 ID 均出现，且没有 `http://` 或 `https://` 外部资源。

### Task 2: 预约校验与成功反馈

**Files:**
- Modify: `index.html`

**Interfaces:**
- Consumes: `HTMLFormElement#booking-form`、`HTMLInputElement#phone`、`HTMLInputElement#date`、`HTMLElement#form-status`。
- Produces: `validatePhone(value: string): boolean`；有效提交时重置表单并更新 `#form-status`。

- [ ] **Step 1: 添加字段与浏览器级约束**

加入 `name`、`phone`、`petType`、`service`、`date` 字段；全部使用可见 `<label>`，必填字段设置 `required`，手机字段设置 `inputmode="numeric"`。

- [ ] **Step 2: 添加最小日期与手机号校验**

实现 `validatePhone(value)`，以 `/^1[3-9]\\d{9}$/` 校验中国大陆手机号；将日期输入框 `min` 设置为本地当天的 `YYYY-MM-DD`。

- [ ] **Step 3: 添加提交状态**

监听 `submit`：阻止网络提交；无效时调用 `setCustomValidity()` 与 `reportValidity()`；有效时 `reset()` 并显示“预约信息已收到，我们会尽快与您确认时间”。状态区域使用 `role="status"` 和 `aria-live="polite"`。

- [ ] **Step 4: 执行源码验收**

Run: `rg -n "validatePhone|setCustomValidity|reportValidity|aria-live|dateInput.min|form.reset" index.html`

Expected: 每个校验与反馈关键点均至少匹配一次。

### Task 3: 离线与基础质量验证

**Files:**
- Test: `index.html`

**Interfaces:**
- Consumes: 完成后的单页。
- Produces: 无代码接口；输出可复核的验收结果。

- [ ] **Step 1: 检查文件完整性与占位符**

Run: `rg -n "TBD|TODO|lorem|https?://" index.html`

Expected: 无匹配。

- [ ] **Step 2: 检查关键语义元素**

Run: `rg -n "<header|<nav|<main|<section|<form|<footer|prefers-reduced-motion" index.html`

Expected: 所有语义元素及减少动态效果规则均有匹配。

- [ ] **Step 3: 浏览器验证**

在桌面宽度与约 390px 手机宽度打开 `index.html`，验证导航锚点、按钮、表单错误提示、有效提交成功提示、键盘焦点和无横向滚动。

- [ ] **Step 4: 记录交付**

确认 `index.html` 可通过双击离线打开，且目录中包含设计规格与实施计划。
