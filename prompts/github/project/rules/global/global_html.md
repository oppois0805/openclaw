
# IWA HTML Template 開發規範

> 本文件規範 Angular 20+ 專案中 HTML Template 的開發標準與最佳實踐。

---

## 🎯 Control Flow 語法

**必須** 使用新的 Control Flow 語法，**禁止** 使用 `*ngIf`、`*ngFor`、`*ngSwitch`：

### @if 條件渲染

```html
<!-- ✅ 推薦：@if 條件渲染 -->
@if (isLoading()) {
<app-spinner />
} @else if (hasError()) {
<app-error [message]="errorMessage()" />
} @else {
<app-content [data]="data()" />
}

<!-- ❌ 禁止：舊語法 -->
<!-- <div *ngIf="isLoading">...</div> -->
```

### @for 迴圈

```html
<!-- ✅ 推薦：@for 迴圈 + track -->
@for (item of items(); track item.id) {
<app-item [item]="item" />
} @empty {
<p>無資料</p>
}

<!-- ❌ 禁止：舊語法 -->
<!-- <div *ngFor="let item of items">...</div> -->
```

### @switch 條件分支

```html
<!-- ✅ 推薦：@switch 條件分支 -->
@switch (status()) { @case ('loading') {
<app-spinner />
} @case ('success') {
<app-success />
} @default {
<app-error />
} }
```

---

## ⚡ @for 迴圈必須使用 track

在 `@for` 迴圈中 **必須** 使用 `track` 以優化渲染效能：

```html
<!-- ✅ 正確：使用 track 追蹤唯一識別符 -->
@for (item of items(); track item.id) {
<app-item [item]="item" />
}

<!-- ✅ 正確：使用陣列索引（當沒有唯一 ID 時） -->
@for (item of items(); track $index) {
<app-item [item]="item" />
}

<!-- ❌ 錯誤：沒有 track -->
<!-- @for (item of items()) { ... } -->
```

### track 選擇原則

| 情境          | 使用方式                      | 說明               |
| ------------- | ----------------------------- | ------------------ |
| 物件有唯一 ID | `track item.id`               | 最佳效能，推薦使用 |
| 物件無唯一 ID | `track $index`                | 次佳選擇           |
| 複合唯一鍵    | `track item.type + item.name` | 多欄位組合         |

---

## 🚫 Template 效能優化

**⚠️ 嚴禁在 HTML Template 中直接呼叫 Function**，這會導致每次變更偵測都重新執行：

```html
<!-- ❌ 禁止：直接呼叫 function（嚴重影響效能） -->
<div>{{ formatDate(date) }}</div>
<div *ngIf="calculateTotal() > 100">...</div>
<app-list [items]="filterItems(searchTerm)" />

<!-- ✅ 推薦：使用 Signal 或 Computed Signal -->
<div>{{ formattedDate() }}</div>
@if (totalAmount() > 100) {
<div>...</div>
}
<app-list [items]="filteredItems()" />
```

### 解決方案

在 Component 中使用 `computed()` 取代 Template 中的 function 呼叫：

```typescript
export class ExampleComponent {
  readonly date = input.required<Date>();
  readonly items = input<Item[]>([]);
  readonly searchTerm = input<string>("");

  // ✅ 使用 computed signal 取代 template function
  readonly formattedDate = computed(() =>
    this.date().toLocaleDateString("zh-TW")
  );

  readonly totalAmount = computed(() =>
    this.items().reduce((sum, item) => sum + item.price, 0)
  );

  readonly filteredItems = computed(() =>
    this.items().filter((item) => item.name.includes(this.searchTerm()))
  );
}
```

---

## ✅ HTML Template 檢查清單

### 必須項目

- [ ] 使用 Control Flow 語法 (`@if`, `@for`, `@switch`)
- [ ] `@for` 迴圈有使用 `track`
- [ ] Template 中沒有直接呼叫 function
- [ ] 使用 Signal getter 讀取狀態，如 `items()` 而非 `items`

### 禁止項目

- [ ] ❌ 不使用 `*ngIf`, `*ngFor`, `*ngSwitch`
- [ ] ❌ 不在 Template 中直接呼叫 function
- [ ] ❌ 不使用 `| async` pipe（改用 Signal）

---

## 🔗 相關參考文件

- **Component 規範**：`global_component.md`
- **SCSS 規範**：`global_scss.md`

