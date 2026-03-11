# Template 效能規則

## 規則 1：禁止在 Template 中呼叫函式

**嚴重程度**：🔴 嚴重
**類別**：Template Performance

### 說明

在 Template 中直接呼叫函式會在每次變更偵測週期都執行，嚴重影響效能。應使用 `computed()` 替代。

### 錯誤寫法

```html
<!-- ❌ 直接呼叫函式 -->
<div>{{ formatDate(createdAt) }}</div>
<div>{{ calculateTotal(items) }}</div>

@if (isValid(form)) {
<button>Submit</button>
} @for (item of getFilteredItems(); track item.id) {
<app-item [data]="item" />
}
```

```typescript
// ❌ 在 Component 中定義這些函式
export class ExampleComponent {
  formatDate(date: Date): string {
    return date.toLocaleDateString("zh-TW");
  }

  calculateTotal(items: Item[]): number {
    return items.reduce((sum, item) => sum + item.price, 0);
  }
}
```

### 正確寫法

```typescript
// ✅ 使用 computed
export class ExampleComponent {
  readonly createdAt = signal<Date>(new Date());
  readonly items = signal<Item[]>([]);
  readonly form = signal<FormData>({});

  readonly formattedDate = computed(() =>
    this.createdAt().toLocaleDateString("zh-TW"),
  );

  readonly total = computed(() =>
    this.items().reduce((sum, item) => sum + item.price, 0),
  );

  readonly isFormValid = computed(() => this.validateForm(this.form()));

  readonly filteredItems = computed(() =>
    this.items().filter((item) => item.active),
  );

  private validateForm(form: FormData): boolean {
    // 驗證邏輯
  }
}
```

```html
<!-- ✅ 使用 computed 結果 -->
<div>{{ formattedDate() }}</div>
<div>{{ total() }}</div>

@if (isFormValid()) {
<button>Submit</button>
} @for (item of filteredItems(); track item.id) {
<app-item [data]="item" />
}
```

---

## 規則 2：@for 必須使用 track

**嚴重程度**：🔴 嚴重
**類別**：Template Performance

### 說明

`@for` 迴圈**必須**指定 `track` 函式，否則每次資料變更都會重新建立所有 DOM 元素。

### 錯誤寫法

```html
<!-- ❌ 未使用 track -->
@for (item of items(); ) {
<app-item [data]="item" />
}
```

### 正確寫法

```html
<!-- ✅ 使用 track 追蹤唯一識別碼 -->
@for (item of items(); track item.id) {
<app-item [data]="item" />
}

<!-- ✅ 如果沒有 id，使用 $index（但不推薦，除非資料不會重排） -->
@for (item of items(); track $index) {
<app-item [data]="item" />
}
```

### 最佳實踐

```typescript
// 確保資料物件有唯一識別碼
interface Item {
  id: string; // 必須有唯一識別碼
  name: string;
  // ...
}
```

---

## 規則 3：避免複雜的 track 函式

**嚴重程度**：🟡 警告
**類別**：Template Performance

### 說明

`track` 函式應該簡單且快速，避免在其中進行複雜計算。

### 錯誤寫法

```html
<!-- ❌ 複雜的 track 函式 -->
@for (item of items(); track generateComplexHash(item)) {
<app-item [data]="item" />
}
```

### 正確寫法

```html
<!-- ✅ 簡單的 track -->
@for (item of items(); track item.id) {
<app-item [data]="item" />
}

<!-- ✅ 組合鍵也可接受 -->
@for (item of items(); track item.type + '-' + item.id) {
<app-item [data]="item" />
}
```

---

## 規則 4：大型列表使用 Virtual Scroll

**嚴重程度**：🔴 嚴重
**類別**：Template Performance

### 說明

當列表項目超過 50 個時，應考慮使用 CDK Virtual Scroll 來優化渲染效能。

### 錯誤寫法

```html
<!-- ❌ 直接渲染大量項目 -->
@for (item of items(); track item.id) {
<!-- 假設有 1000 個項目 -->
<app-large-item [data]="item" />
}
```

### 正確寫法

```html
<!-- ✅ 使用 Virtual Scroll -->
<cdk-virtual-scroll-viewport itemSize="50" class="viewport">
  <app-large-item
    *cdkVirtualFor="let item of items(); trackBy: trackById"
    [data]="item"
  />
</cdk-virtual-scroll-viewport>
```

```typescript
export class ExampleComponent {
  readonly items = signal<Item[]>([]);

  trackById(index: number, item: Item): string {
    return item.id;
  }
}
```

---

## 規則 5：避免在 @if 中使用複雜條件

**嚴重程度**：🟡 警告
**類別**：Template Performance

### 說明

`@if` 中的複雜條件應抽取為 `computed()`。

### 錯誤寫法

```html
<!-- ❌ 複雜條件直接寫在 template -->
@if (user() && user().roles && user().roles.includes('admin') && !isLoading()) {
<admin-panel />
}
```

### 正確寫法

```typescript
// ✅ 抽取為 computed
readonly isAdminPanelVisible = computed(() => {
  const user = this.user();
  return user?.roles?.includes('admin') && !this.isLoading();
});
```

```html
<!-- ✅ 使用 computed -->
@if (isAdminPanelVisible()) {
<admin-panel />
}
```

---

## 規則 6：避免過深的 Template 巢狀

**嚴重程度**：🟢 建議
**類別**：Template Performance

### 說明

過深的 Template 巢狀會增加變更偵測的複雜度。建議將複雜區塊抽取為獨立的子元件。

### 錯誤寫法

```html
<!-- ❌ 過深巢狀 -->
@for (category of categories(); track category.id) {
<div class="category">
  @for (subcategory of category.subcategories; track subcategory.id) {
  <div class="subcategory">
    @for (item of subcategory.items; track item.id) {
    <div class="item">
      @if (item.hasDetails) { @for (detail of item.details; track detail.id) {
      <!-- ... -->
      } }
    </div>
    }
  </div>
  }
</div>
}
```

### 正確寫法

```html
<!-- ✅ 抽取為子元件 -->
@for (category of categories(); track category.id) {
<app-category [data]="category" />
}
```

```typescript
// category.component.ts
@Component({
  selector: "app-category",
  changeDetection: ChangeDetectionStrategy.OnPush,
  // ...
})
export class CategoryComponent {
  readonly data = input.required<Category>();
}
```

---

## 規則 7：純 Pipe 優先於函式呼叫

**嚴重程度**：🟢 建議
**類別**：Template Performance

### 說明

對於格式化等操作，Pure Pipe 會快取結果，比 computed() 更適合重複使用的轉換。

### 可接受寫法

```html
<!-- 使用 Pure Pipe 進行格式化 -->
<span>{{ createdAt() | date:'yyyy/MM/dd' }}</span>
<span>{{ amount() | currency:'TWD' }}</span>
```

### 自訂 Pipe 範例

```typescript
@Pipe({
  name: "fileSize",
  standalone: true,
  pure: true, // 預設為 true，確保是 Pure Pipe
})
export class FileSizePipe implements PipeTransform {
  transform(bytes: number): string {
    if (bytes < 1024) return `${bytes} B`;
    if (bytes < 1024 * 1024) return `${(bytes / 1024).toFixed(1)} KB`;
    return `${(bytes / 1024 / 1024).toFixed(1)} MB`;
  }
}
```
