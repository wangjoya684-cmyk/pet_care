# 预约表单文案优化 Implementation Plan

> **For agentic workers:** 按以下步骤内联执行并逐项验证；本仓库不由代理执行提交或推送。

**Goal:** 优化预约区域的引导与字段文案，并删除该区域的“出浴纪念照”权益。

**Architecture:** 保持现有单文件页面结构，仅替换 `index.html` 预约区域内的静态 HTML 文案和删除一个权益节点。不调整 CSS 或 JavaScript。

**Tech Stack:** HTML5、内嵌 CSS、原生 JavaScript

## Global Constraints

- 仅修改 `index.html` 的预约区域。
- 页面其他区域的“出浴美照”和“出浴照”表述保持不变。
- 不改变表单字段、校验、提交逻辑、布局和样式。
- 不由代理提交或推送代码。

---

### Task 1: 更新预约区域文案

**Files:**
- Modify: `index.html:519-539`

**Interfaces:**
- Consumes: 现有 `#booking` 预约区域和 `#booking-form` 表单。
- Produces: 文案更清晰、不含“出浴纪念照”权益的预约区域。

- [ ] **Step 1: 记录修改前的匹配结果**

  Run: `rg -n "留下简单信息|填写预约信息|主人称呼|毛孩子类型|想预约的服务|出浴纪念照" index.html`

  Expected: 上述六组旧文案均在预约区域中命中。

- [ ] **Step 2: 替换引导、提示和字段文案**

  将预约区域更新为以下确定文案：

  ```html
  <p>留下基本信息，我们会在营业时间内尽快联系你，确认适合的到店时间和服务方案。</p>
  <h3>填写预约需求</h3>
  <p class="form-intro">请填写以下信息，带 * 为必填项。提交后，店员会与你联系确认。</p>
  <label for="name">您的称呼 *</label>
  <label for="petType">宠物类型 *</label>
  <label for="service">预约服务 *</label>
  ```

  同时删除以下整个节点：

  ```html
  <div class="booking-perk"><span>✓</span> 免费提供一张出浴纪念照</div>
  ```

- [ ] **Step 3: 验证新旧文案与修改范围**

  Run: `rg -n "留下基本信息|填写预约需求|请填写以下信息|您的称呼|宠物类型|<label for=\"service\">预约服务" index.html`

  Expected: 六组新文案均命中。

  Run: `rg -n "留下简单信息|填写预约信息|主人称呼|毛孩子类型|想预约的服务|出浴纪念照" index.html`

  Expected: 无匹配。

  Run: `git diff --check`

  Expected: 无输出，退出码为 0。

  Run: `git diff -- index.html`

  Expected: 只包含上述预约区域文案替换和权益节点删除，不含 CSS、JavaScript 或页面其他区域改动。
