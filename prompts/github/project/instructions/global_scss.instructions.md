
# IWA SCSS 開發規範

> 本文件規範 Angular 20+ 專案中 SCSS 樣式的開發標準與最佳實踐。

---

## 🎨 基本原則

### Component 樣式隔離

每個 Component 應有獨立的 `.scss` 檔案，利用 Angular 的 View Encapsulation 機制：

```
feature/
├── feature.component.ts
├── feature.component.html
└── feature.component.scss  # Component 專屬樣式
```

### 檔案命名

- Component 樣式：`{component-name}.component.scss`
- 共用樣式：`_variables.scss`、`_mixins.scss`、`_functions.scss`

---

## 📐 SCSS 結構規範

### 選擇器命名（BEM 風格）

使用 BEM（Block Element Modifier）命名方式：

```scss
// Block
.user-card {
  // Element
  &__header {
    display: flex;
    align-items: center;
  }

  &__title {
    font-size: 1.25rem;
    font-weight: 600;
  }

  &__content {
    padding: 1rem;
  }

  // Modifier
  &--active {
    border-color: var(--primary-color);
  }

  &--disabled {
    opacity: 0.5;
    pointer-events: none;
  }
}
```

### :host 選擇器

使用 `:host` 設定 Component 根元素樣式：

```scss
:host {
  display: block;
  width: 100%;
}

:host(.is-hidden) {
  display: none;
}

:host-context(.dark-theme) {
  background-color: #333;
  color: #fff;
}
```

---

## 🎯 變數與設計 Token

### 使用 CSS 變數

優先使用專案定義的 CSS 變數：

```scss
.button {
  // ✅ 推薦：使用 CSS 變數
  background-color: var(--primary-color);
  border-radius: var(--border-radius-md);
  padding: var(--spacing-sm) var(--spacing-md);

  // ❌ 避免：寫死數值
  // background-color: #007bff;
  // border-radius: 4px;
  // padding: 8px 16px;
}
```

### 常用變數分類

```scss
// 顏色
--primary-color
--secondary-color
--success-color
--warning-color
--error-color
--text-color
--background-color

// 間距
--spacing-xs: 4px;
--spacing-sm: 8px;
--spacing-md: 16px;
--spacing-lg: 24px;
--spacing-xl: 32px;

// 字體
--font-size-sm
--font-size-md
--font-size-lg
--font-weight-normal
--font-weight-bold

// 圓角
--border-radius-sm
--border-radius-md
--border-radius-lg

// 陰影
--shadow-sm
--shadow-md
--shadow-lg
```

---

## 📱 響應式設計

### Breakpoint Mixin

```scss
// _mixins.scss
@mixin respond-to($breakpoint) {
  @if $breakpoint == "sm" {
    @media (min-width: 576px) {
      @content;
    }
  } @else if $breakpoint == "md" {
    @media (min-width: 768px) {
      @content;
    }
  } @else if $breakpoint == "lg" {
    @media (min-width: 992px) {
      @content;
    }
  } @else if $breakpoint == "xl" {
    @media (min-width: 1200px) {
      @content;
    }
  }
}

// 使用範例
.container {
  padding: var(--spacing-sm);

  @include respond-to("md") {
    padding: var(--spacing-md);
  }

  @include respond-to("lg") {
    padding: var(--spacing-lg);
  }
}
```

---

## 🧩 常用 Mixin

### Flexbox

```scss
@mixin flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

@mixin flex-between {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

@mixin flex-column {
  display: flex;
  flex-direction: column;
}
```

### 文字溢出

```scss
@mixin text-ellipsis {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

@mixin text-clamp($lines: 2) {
  display: -webkit-box;
  -webkit-line-clamp: $lines;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
```

### 使用範例

```scss
.card-title {
  @include text-ellipsis;
  font-size: var(--font-size-lg);
}

.card-description {
  @include text-clamp(3);
  color: var(--text-secondary);
}
```

---

## 🚫 禁止事項

### 避免使用

```scss
// ❌ 禁止：使用 !important
.button {
  color: red !important;
}

// ❌ 禁止：過深的巢狀（超過 3 層）
.container {
  .wrapper {
    .content {
      .item {
        .text {
        } // 太深了！
      }
    }
  }
}

// ❌ 禁止：使用 ID 選擇器
#main-header {
}

// ❌ 禁止：寫死顏色值（應使用變數）
.text {
  color: #333333;
}

// ❌ 禁止：使用 px 單位（間距應使用變數）
.container {
  padding: 16px;
  margin: 24px;
}
```

### 推薦做法

```scss
// ✅ 推薦：使用 CSS 變數
.text {
  color: var(--text-color);
}

// ✅ 推薦：使用間距變數
.container {
  padding: var(--spacing-md);
  margin: var(--spacing-lg);
}

// ✅ 推薦：扁平化結構
.card {
}
.card__header {
}
.card__content {
}
.card__footer {
}
```

---

## ✅ SCSS 檢查清單

### 必須項目

- [ ] 使用 BEM 命名方式
- [ ] 使用 CSS 變數定義顏色、間距、字體等
- [ ] 使用 `:host` 設定 Component 根元素樣式
- [ ] 使用 Mixin 處理響應式設計
- [ ] 巢狀層級不超過 3 層

### 禁止項目

- [ ] ❌ 不使用 `!important`
- [ ] ❌ 不使用 ID 選擇器
- [ ] ❌ 不寫死顏色值（使用變數）
- [ ] ❌ 不使用過深的巢狀結構
- [ ] ❌ 不直接使用 px 數值（使用變數）

---

## 🔗 相關參考文件

- **Component 規範**：`global_component.md`
- **HTML Template 規範**：`global_html.md`

