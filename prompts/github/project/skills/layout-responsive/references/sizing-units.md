# 尺寸與單位使用規則

## 單位選擇決策表

| 用途              | 建議單位      | 說明                       |
| ----------------- | ------------- | -------------------------- |
| **字體大小**      | `rem`         | 相對於根元素，易於全域調整 |
| **間距/Padding**  | `rem` 或 `em` | 與字體相關的間距           |
| **容器寬度**      | `%` 或 `fr`   | 相對於父容器               |
| **最大/最小寬度** | `px` 或 `rem` | 固定限制可用 px            |
| **邊框**          | `px`          | 通常固定 1-2px             |
| **行高**          | 無單位數字    | `line-height: 1.5`         |
| **媒體查詢**      | `px`          | 斷點使用固定值             |

---

## 寬度規則

### ✅ 正確用法

```scss
// 填滿父容器
.full-width {
  width: 100%;
}

// 有最大限制的填滿
.container {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto; // 置中
}

// 比例分配
.half {
  width: 50%;
}

// 彈性最小寬度
.card {
  flex: 1;
  min-width: 280px;
}
```

### ❌ 禁止用法

```scss
// ❌ 固定寬度
.container {
  width: 1200px;
}

// ❌ 無限制的固定寬度
.card {
  width: 350px;
}
```

### 例外情況

以下情況**可以**使用固定寬度：

| 情況           | 範例                         | 說明             |
| -------------- | ---------------------------- | ---------------- |
| 圖示尺寸       | `width: 24px; height: 24px;` | 圖示通常固定大小 |
| 頭像           | `width: 40px; height: 40px;` | 保持圓形比例     |
| 最小/最大限制  | `min-width: 200px;`          | 作為限制使用     |
| 側邊欄固定寬度 | `flex: 0 0 280px;`           | 搭配 flex 使用   |

---

## 高度規則

### 🎯 核心原則：讓內容決定高度

```scss
// ✅ 正確：不設定高度
.content-box {
  padding: 1rem;
  // 高度由內容決定
}

// ✅ 正確：最小高度
.hero-section {
  min-height: 400px;
  // 內容多時自動擴展
}

// ✅ 正確：視窗高度
.full-page {
  min-height: 100vh;
}

// ✅ 正確：flex 填滿
.main-content {
  flex: 1;
}
```

### ❌ 禁止用法

```scss
// ❌ 固定高度（內容會溢出）
.card {
  height: 300px;
}

// ❌ 使用 height 100%（可能無效）
.container {
  height: 100%; // 父元素沒高度時無效
}
```

### Flex 容器中的高度

```scss
// 父容器
.page {
  display: flex;
  flex-direction: column;
  min-height: 100vh; // 最小視窗高度
}

// 子元素
.header {
  flex: 0 0 auto;
} // 依內容
.main {
  flex: 1;
} // 填滿剩餘
.footer {
  flex: 0 0 auto;
} // 依內容
```

---

## 間距規則（Spacing）

### 使用 CSS 變數定義間距系統

```scss
:root {
  // 間距比例尺
  --spacing-xs: 0.25rem; // 4px
  --spacing-sm: 0.5rem; // 8px
  --spacing-md: 1rem; // 16px
  --spacing-lg: 1.5rem; // 24px
  --spacing-xl: 2rem; // 32px
  --spacing-2xl: 3rem; // 48px
}
```

### ✅ 正確用法

```scss
// 使用 CSS 變數
.card {
  padding: var(--spacing-md);
  margin-bottom: var(--spacing-lg);
}

// 使用 gap
.list {
  display: flex;
  gap: var(--spacing-md);
}

// 使用 rem
.section {
  padding: 2rem 1.5rem;
}
```

### ❌ 禁止用法

```scss
// ❌ 任意固定像素值
.card {
  padding: 17px;
  margin: 23px;
}

// ❌ 使用 margin 模擬 gap
.list-item {
  margin-right: 16px;
}
.list-item:last-child {
  margin-right: 0;
}
```

---

## 字體大小規則

### 使用 rem 單位

```scss
:root {
  font-size: 16px; // 根元素基準
}

// 字體比例尺
:root {
  --font-xs: 0.75rem; // 12px
  --font-sm: 0.875rem; // 14px
  --font-md: 1rem; // 16px
  --font-lg: 1.125rem; // 18px
  --font-xl: 1.25rem; // 20px
  --font-2xl: 1.5rem; // 24px
  --font-3xl: 2rem; // 32px
}

.body-text {
  font-size: var(--font-md);
}

.heading {
  font-size: var(--font-2xl);
}
```

### 行高使用無單位數字

```scss
// ✅ 正確
.text {
  line-height: 1.5;
}

// ❌ 錯誤
.text {
  line-height: 24px;
}
```

---

## 邊框與陰影

### 邊框可使用 px

```scss
// ✅ 正確：邊框使用 px
.card {
  border: 1px solid var(--color-border);
  border-radius: 0.5rem; // 圓角用 rem
}
```

### 陰影使用 px

```scss
// ✅ 正確：陰影使用 px
.card {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}
```

---

## 計算與混合單位

### 使用 calc() 混合單位

```scss
// ✅ 正確：計算混合單位
.sidebar {
  width: calc(100% - 2rem);
}

.content {
  height: calc(100vh - 64px); // 減去固定 header
}

// Grid 搭配 minmax
.grid {
  grid-template-columns: repeat(auto-fit, minmax(min(100%, 300px), 1fr));
}
```

---

## 速查表

| 屬性            | 推薦                         | 避免      |
| --------------- | ---------------------------- | --------- |
| `width`         | `%`, `fr`, `100%`            | 固定 `px` |
| `max-width`     | `px`, `rem`                  | -         |
| `min-width`     | `px`, `rem`                  | -         |
| `height`        | `auto`, `100%`, `min-height` | 固定 `px` |
| `min-height`    | `px`, `rem`, `vh`            | -         |
| `padding`       | `rem`, CSS 變數              | 任意 `px` |
| `margin`        | `rem`, `auto`                | 任意 `px` |
| `gap`           | `rem`, CSS 變數              | -         |
| `font-size`     | `rem`, CSS 變數              | `px`      |
| `line-height`   | 無單位數字                   | `px`      |
| `border`        | `px`                         | -         |
| `border-radius` | `rem`, `px`                  | -         |
