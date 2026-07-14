# 顾客评价轮播 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 将三条纵向顾客评价改成紧凑、可自动播放且可手动控制的无障碍轮播。

**Architecture:** 保持单文件 `index.html`，使用轮播视口包裹横向轨道，每张评价占轨道 100% 宽度。原生 JavaScript 维护唯一的 `currentReview` 索引，并统一更新位移、圆点和无障碍状态。

**Tech Stack:** HTML5、CSS3、原生 JavaScript

## Global Constraints

- 不添加第三方依赖，不改变现有评价文案和预约表单逻辑。
- 默认每 5 秒自动切换，循环播放。
- 页面不可见时暂停；悬停或焦点进入时继续播放，用户操作后重新计时。
- 减少动态效果模式下关闭自动播放和滑动动画。
- 支持按钮、圆点、键盘左右键与超过 45px 的触摸滑动。

---

### Task 1: 轮播结构与视觉

**Files:**
- Modify: `index.html`

**Interfaces:**
- Consumes: 现有三条 `.review-card` 评价。
- Produces: `.review-carousel`、`.review-viewport`、`.review-track`、`#review-prev`、`#review-next`、`.review-dot`、`#review-status`。

- [ ] **Step 1: 替换评价区结构**

用 `role="region" aria-label="顾客评价轮播" tabindex="0"` 的容器包裹视口、轨道与控制区。三张卡片保留原内容；第一张设置 `aria-hidden="false"`，其余设置 `aria-hidden="true"`。添加上一条、下一条按钮，三个带 `data-review-index="0|1|2"` 的圆点，以及 `aria-live="polite"` 状态元素。

- [ ] **Step 2: 实现紧凑布局**

设置轮播最大宽度 760px 并居中；视口隐藏溢出；轨道使用 `display:flex` 和 `transform`；评价卡片设置 `flex:0 0 100%`。控制按钮保持至少 44px 触摸区域，圆点当前项使用焦糖橙显示。

- [ ] **Step 3: 增加响应式与减少动态规则**

手机端缩小卡片内边距和控制间距；在现有 `prefers-reduced-motion: reduce` 中关闭 `.review-track` 的过渡。

- [ ] **Step 4: 验证结构**

Run: `rg -n "review-carousel|review-track|review-prev|review-next|review-dot|review-status|aria-hidden" index.html`

Expected: 所有轮播结构、控制和无障碍属性均有匹配。

### Task 2: 轮播状态、自动播放与输入方式

**Files:**
- Modify: `index.html`

**Interfaces:**
- Consumes: Task 1 生成的轮播 DOM。
- Produces: `showReview(index: number, userInitiated?: boolean): void`、`startReviewAutoplay(): void`、`stopReviewAutoplay(): void`。

- [ ] **Step 1: 实现统一状态更新**

声明 `currentReview = 0`，通过 `showReview()` 对索引取模，设置轨道 `translateX(-index * 100%)`，同步每张卡片的 `aria-hidden`、圆点 `aria-current` 和手动切换播报文本。

- [ ] **Step 2: 实现自动播放与暂停**

使用 5000ms 定时器切换下一条；仅在 `document.visibilitychange` 表明页面不可见时停止计时，页面恢复可见后继续。悬停和键盘焦点不暂停；减少动态效果匹配时不启动计时。

- [ ] **Step 3: 实现按钮、圆点和键盘**

左右按钮调用 `showReview(currentReview ± 1, true)`；圆点读取 `data-review-index`；容器监听 `ArrowLeft` 与 `ArrowRight`，阻止默认行为并切换。

- [ ] **Step 4: 实现触摸滑动**

记录 `touchstart` 的 `clientX`，在 `touchend` 中计算位移；绝对值超过 45px 时按方向切换，否则不操作。

- [ ] **Step 5: 执行源码验证**

Run: `rg -n "showReview|startReviewAutoplay|stopReviewAutoplay|5000|ArrowLeft|ArrowRight|touchstart|touchend|visibilitychange|prefers-reduced-motion" index.html`

Expected: 每项行为至少有一个匹配，JavaScript 通过 `new Function()` 语法检查。

### Task 3: 浏览器验收

**Files:**
- Test: `index.html`

**Interfaces:**
- Consumes: 完成后的轮播。
- Produces: 无代码接口；输出可复核的验收结果。

- [ ] **Step 1: 桌面端验证**

确认一次只显示一张完整评价、按钮和圆点可用、5 秒后自动切换、悬停时继续播放且页面无横向溢出。

- [ ] **Step 2: 手机端验证**

在约 390px 宽度确认卡片完整、控件可点、左右滑动可切换且页面高度明显减少。

- [ ] **Step 3: 无障碍与回归验证**

确认键盘左右键、焦点进入后继续播放、当前圆点状态、手动切换播报、减少动态效果规则、控制台无错误，并复测导航和预约表单关键逻辑。

- [ ] **Step 4: 提交并更新 PR**

Run: `git add index.html docs/superpowers/plans/2026-07-14-review-carousel.md && git commit -m "feat: add customer review carousel" && git push`

Expected: 当前页面优化分支和现有 PR 包含轮播实现及验证记录。
