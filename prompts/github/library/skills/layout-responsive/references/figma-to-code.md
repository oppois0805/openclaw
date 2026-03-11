# Figma 設計轉換規則

## 轉換原則

從 Figma 進行切版時，**不要直接複製像素值**，而是根據設計意圖轉換為自適應的 CSS 寫法。

---

## Figma Auto Layout → CSS

### 方向對應

| Figma 設定 | CSS 寫法                 |
| ---------- | ------------------------ |
| Horizontal | `flex-direction: row`    |
| Vertical   | `flex-direction: column` |

### 間距轉換

| Figma Gap | CSS Gap        |
| --------- | -------------- |
| 8px       | `gap: 0.5rem`  |
| 12px      | `gap: 0.75rem` |
| 16px      | `gap: 1rem`    |
| 24px      | `gap: 1.5rem`  |
| 32px      | `gap: 2rem`    |

### Padding 轉換

| Figma Padding | CSS Padding           |
| ------------- | --------------------- |
| 8px           | `padding: 0.5rem`     |
| 16px          | `padding: 1rem`       |
| 24px          | `padding: 1.5rem`     |
| 不同邊        | 使用 CSS 變數統一管理 |

---

## 尺寸約束轉換

### Figma Constraints → CSS

| Figma 設定         | 說明       | CSS 轉換                               |
| ------------------ | ---------- | -------------------------------------- |
| **Fixed** (W/H)    | 固定尺寸   | **避免**，改用 `min-width`/`max-width` |
| **Hug contents**   | 依內容大小 | 不設定尺寸（預設行為）                 |
| **Fill container** | 填滿容器   | `width: 100%` 或 `flex: 1`             |

### 範例轉換

#### Figma: Fixed Width 320px

```scss
// ❌ 直接複製
.card {
  width: 320px;
}

// ✅ 正確轉換
.card {
  width: 100%;
  max-width: 320px;
}

// ✅ 或使用 flex
.card {
  flex: 0 0 320px;
  min-width: 280px; // 可選的最小寬度
}
```

#### Figma: Fill Container

```scss
// ✅ 使用百分比
.panel {
  width: 100%;
}

// ✅ 使用 flex
.panel {
  flex: 1;
}

// ✅ 使用 grid
.panel {
  /* grid 子項目自動填滿 */
}
```

#### Figma: Hug Contents

```scss
// ✅ 不設定尺寸，讓內容決定
.badge {
  padding: 0.25rem 0.5rem;
  // 不設定 width/height
}
```

---

## 間距與對齊轉換

### Figma Alignment → CSS

| Figma 設定    | CSS 寫法                         |
| ------------- | -------------------------------- |
| Left          | `justify-content: flex-start`    |
| Center        | `justify-content: center`        |
| Right         | `justify-content: flex-end`      |
| Space between | `justify-content: space-between` |
| Top           | `align-items: flex-start`        |
| Middle        | `align-items: center`            |
| Bottom        | `align-items: flex-end`          |

### 範例

```scss
// Figma: Center aligned, Space between
.toolbar {
  display: flex;
  align-items: center; // 垂直置中
  justify-content: space-between; // 左右分散
  gap: 1rem;
}
```

---

## 比例計算

### 計算元素佔比

當 Figma 中元素有明確的比例關係時：

```
元素寬度百分比 = (元素寬度 / 父容器寬度) × 100%
```

#### 範例

- 父容器寬度：1200px
- 側邊欄寬度：300px
- 主內容寬度：900px

```scss
.layout {
  display: flex;
}

.sidebar {
  flex: 0 0 25%; // 300/1200 = 25%
  min-width: 240px;
}

.main-content {
  flex: 1; // 填滿剩餘
}
```

### Grid 比例轉換

- 三欄等寬：`grid-template-columns: repeat(3, 1fr)`
- 1:2:1 比例：`grid-template-columns: 1fr 2fr 1fr`
- 固定 + 彈性：`grid-template-columns: 280px 1fr`

---

## 字體轉換

### Figma 字體 → CSS 變數

| Figma 樣式       | CSS 寫法                     |
| ---------------- | ---------------------------- |
| Heading 1 (32px) | `font-size: var(--font-3xl)` |
| Heading 2 (24px) | `font-size: var(--font-2xl)` |
| Body (16px)      | `font-size: var(--font-md)`  |
| Caption (12px)   | `font-size: var(--font-xs)`  |

### 行高轉換

```
無單位行高 = Figma 行高 / 字體大小

範例：24px 行高 / 16px 字體 = 1.5
```

```scss
.body-text {
  font-size: var(--font-md);
  line-height: 1.5;
}
```

---

## 顏色轉換

### 使用 CSS 變數

```scss
// 定義顏色變數
:root {
  --color-primary: #3b82f6;
  --color-secondary: #64748b;
  --color-background: #ffffff;
  --color-text: #1e293b;
  --color-border: #e2e8f0;
}

// 使用變數
.button {
  background-color: var(--color-primary);
  color: var(--color-background);
}
```

---

## 響應式考量

### Figma 多尺寸設計

當 Figma 有多個 Frame 表示不同裝置尺寸時：

1. **識別佈局變化點**：找出不同尺寸間的佈局差異
2. **定義斷點**：根據設計變化設定 media query
3. **優先行動裝置**：從小尺寸開始設計（mobile-first）

```scss
// Mobile-first 寫法
.grid {
  display: grid;
  grid-template-columns: 1fr; // 手機：單欄
  gap: 1rem;
}

@media (min-width: 768px) {
  .grid {
    grid-template-columns: repeat(2, 1fr); // 平板：雙欄
  }
}

@media (min-width: 1024px) {
  .grid {
    grid-template-columns: repeat(3, 1fr); // 桌面：三欄
  }
}
```

---

## 轉換檢查清單

進行 Figma 轉換後，確認以下項目：

- [ ] 所有固定寬度已轉換為 `max-width` 或百分比
- [ ] 所有固定高度已移除或轉換為 `min-height`
- [ ] 間距使用 `rem` 或 CSS 變數
- [ ] 使用 Flexbox/Grid 進行佈局
- [ ] 顏色使用 CSS 變數
- [ ] 字體使用 CSS 變數和 `rem`

---

## 常見錯誤

### ❌ 直接複製所有像素值

```scss
// ❌ 錯誤：直接從 Figma 複製
.card {
  width: 340px;
  height: 280px;
  padding: 24px;
  margin: 16px;
  font-size: 14px;
}
```

### ✅ 正確轉換

```scss
// ✅ 正確：轉換為自適應值
.card {
  width: 100%;
  max-width: 340px;
  min-height: 280px; // 或移除高度限制
  padding: 1.5rem;
  margin: 1rem;
  font-size: var(--font-sm);
}
```
