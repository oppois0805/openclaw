# Flexbox 與 Grid 佈局規則

## 佈局選擇決策樹

```
是否為一維排列（單行或單列）？
├── 是 → 使用 Flexbox
│   ├── 水平排列 → flex-direction: row
│   └── 垂直排列 → flex-direction: column
└── 否（二維排列）→ 使用 Grid
    ├── 固定列數 → grid-template-columns: repeat(N, 1fr)
    └── 自適應列數 → grid-template-columns: repeat(auto-fit, minmax(Xpx, 1fr))
```

---

## Flexbox 規則

### 基本容器設定

```scss
// ✅ 標準 Flex 容器
.flex-container {
  display: flex;
  flex-direction: row; // 或 column
  gap: 1rem; // 使用 gap 取代 margin
  align-items: stretch; // 子元素等高
}
```

### 子元素尺寸控制

| 需求         | 寫法                         | 說明                 |
| ------------ | ---------------------------- | -------------------- |
| 填滿剩餘空間 | `flex: 1`                    | 自動擴展填滿         |
| 固定比例     | `flex: 0 0 25%`              | 不壓縮不擴展，佔 25% |
| 依內容大小   | `flex: 0 0 auto`             | 依內容決定寬度       |
| 最小寬度保護 | `flex: 1; min-width: 200px;` | 填滿但不小於 200px   |

### 常用模式

#### 側邊欄 + 主內容

```scss
.layout {
  display: flex;
  gap: 1rem;
  height: 100%;
}

.sidebar {
  flex: 0 0 280px; // 固定寬度側邊欄
  min-width: 200px;
}

.main-content {
  flex: 1; // 填滿剩餘空間
  min-width: 0; // 防止內容撐開
}
```

#### 頁首 + 內容 + 頁尾

```scss
.page {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.header {
  flex: 0 0 auto; // 依內容高度
}

.content {
  flex: 1; // 填滿中間空間
}

.footer {
  flex: 0 0 auto; // 依內容高度
}
```

#### 等寬項目列表

```scss
.card-list {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}

.card {
  flex: 1 1 calc(33.333% - 1rem); // 三欄
  min-width: 250px; // 最小寬度，自動換行
}
```

---

## Grid 規則

### 基本容器設定

```scss
// ✅ 標準 Grid 容器
.grid-container {
  display: grid;
  gap: 1rem;
}
```

### 欄位定義方式

| 需求        | 寫法                                                          | 說明               |
| ----------- | ------------------------------------------------------------- | ------------------ |
| 固定等寬欄  | `grid-template-columns: repeat(3, 1fr)`                       | 三等分             |
| 比例分配    | `grid-template-columns: 1fr 2fr 1fr`                          | 1:2:1 比例         |
| 自適應欄數  | `grid-template-columns: repeat(auto-fit, minmax(250px, 1fr))` | 自動調整欄數       |
| 固定 + 彈性 | `grid-template-columns: 280px 1fr`                            | 左側固定，右側填滿 |

### 常用模式

#### 響應式卡片網格

```scss
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.5rem;
}

.card {
  // 不需要設定寬度，Grid 自動處理
}
```

#### 儀表板佈局

```scss
.dashboard {
  display: grid;
  grid-template-columns: repeat(12, 1fr); // 12 欄系統
  grid-template-rows: auto 1fr auto;
  gap: 1rem;
  min-height: 100vh;
}

.header {
  grid-column: 1 / -1; // 滿版
}

.sidebar {
  grid-column: 1 / 4; // 佔 3 欄
}

.main {
  grid-column: 4 / -1; // 佔剩餘 9 欄
}

.footer {
  grid-column: 1 / -1; // 滿版
}
```

#### 表單佈局

```scss
.form-grid {
  display: grid;
  grid-template-columns: auto 1fr; // Label + Input
  gap: 1rem 1.5rem;
  align-items: center;
}

.form-label {
  text-align: right;
}

.form-input {
  width: 100%;
}

// 全寬欄位
.form-full {
  grid-column: 1 / -1;
}
```

---

## 禁止寫法

### ❌ 使用 float 佈局

```scss
// ❌ 過時寫法
.container::after {
  content: "";
  clear: both;
}
.left {
  float: left;
  width: 30%;
}
.right {
  float: right;
  width: 70%;
}

// ✅ 改用 Flexbox
.container {
  display: flex;
}
.left {
  flex: 0 0 30%;
}
.right {
  flex: 1;
}
```

### ❌ 使用負邊距對齊

```scss
// ❌ 負邊距
.item {
  margin-right: -1rem;
}

// ✅ 使用 gap
.container {
  display: flex;
  gap: 1rem;
}
```

### ❌ 使用固定定位的間距

```scss
// ❌ 固定間距
.second-column {
  margin-left: 300px;
}

// ✅ 使用 flex/grid
.container {
  display: flex;
}
.first-column {
  flex: 0 0 300px;
}
.second-column {
  flex: 1;
}
```

---

## 響應式調整

### 使用 CSS 變數管理斷點

```scss
:root {
  --sidebar-width: 280px;
  --content-gap: 1.5rem;
}

@media (max-width: 768px) {
  :root {
    --sidebar-width: 100%;
    --content-gap: 1rem;
  }
}

.layout {
  display: flex;
  gap: var(--content-gap);
}

.sidebar {
  flex: 0 0 var(--sidebar-width);
}
```

### Grid 自動響應

```scss
// 自動響應，不需要 media query
.responsive-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1rem;
}
```
