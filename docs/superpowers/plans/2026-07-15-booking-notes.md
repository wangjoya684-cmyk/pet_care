# 预约备注字段 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 在预约表单中增加选填的 200 字备注框、实时字数统计和提交后重置行为。

**Architecture:** 保持单文件 `index.html`。HTML 提供语义化文本框和计数元素，CSS 复用现有表单视觉，原生 JavaScript 只负责更新计数并在有效提交后恢复初始状态。

**Tech Stack:** HTML5、CSS3、原生 JavaScript

## Global Constraints

- 备注为选填，最多 200 个字符。
- 当前表单仍为前端演示，不发送或保存备注内容。
- 桌面和手机端占据整行，不造成横向溢出。
- 不改变现有姓名、手机号、宠物类型、服务和日期校验。

---

### Task 1: 备注字段结构与样式

**Files:**
- Modify: `index.html`

**Interfaces:**
- Consumes: 现有 `.form-grid` 和 `.field.full` 表单布局。
- Produces: `HTMLTextAreaElement#notes` 与 `HTMLElement#notes-count`。

- [ ] **Step 1: 添加语义化字段**

在日期字段后添加整行 `.field.full`，包含 `<label for="notes">备注（选填）</label>`、`<textarea id="notes" name="notes" maxlength="200" aria-describedby="notes-count">` 和初始文本为 `0 / 200` 的计数元素。

- [ ] **Step 2: 添加控件样式**

让 `textarea` 复用输入控件的背景、边框、圆角和焦点样式；设置最小高度 90px、内边距 14px 15px、`resize: vertical`。计数右对齐，使用次要文字颜色和 11px 字号。

- [ ] **Step 3: 检查结构**

Run: `rg -n "textarea|notes-count|maxlength=\"200\"|aria-describedby" index.html`

Expected: 备注框、字符限制和无障碍关联均有匹配。

### Task 2: 字数更新与表单重置

**Files:**
- Modify: `index.html`

**Interfaces:**
- Consumes: `#notes`、`#notes-count` 和现有表单提交监听器。
- Produces: `updateNotesCount(): void`。

- [ ] **Step 1: 实现字数统计**

缓存备注框和计数元素，定义 `updateNotesCount()` 将计数文本设置为 `${notesInput.value.length} / 200`，并在 `input` 事件中调用。

- [ ] **Step 2: 接入成功提交**

在现有 `form.reset()` 后调用 `updateNotesCount()`，保证有效提交后内容和计数同步恢复。

- [ ] **Step 3: 执行源码验证**

Run: `rg -n "updateNotesCount|notesInput.value.length|form.reset" index.html`

Expected: 输入更新和成功重置逻辑均有匹配，脚本通过 `new Function()` 语法检查。

### Task 3: 验收与提交

**Files:**
- Test: `index.html`

**Interfaces:**
- Consumes: 完成后的预约表单。
- Produces: 可复核的验证结果。

- [ ] **Step 1: 验证输入限制**

确认初始计数为 `0 / 200`，输入中文、英文和空格时实时更新，达到 200 字后无法继续输入。

- [ ] **Step 2: 验证提交与回归**

确认空备注和非空备注均可提交；成功后备注与计数清空；原手机号和日期校验保持有效。

- [ ] **Step 3: 验证响应式**

确认桌面与约 390px 手机宽度下备注框为整行，纵向调整不产生横向滚动。

- [ ] **Step 4: 提交改动**

Run: `git add index.html docs/superpowers/plans/2026-07-15-booking-notes.md && git commit -m "feat: add booking notes field"`

Expected: 当前分支新增一个只包含备注字段实现和计划的提交。
