# 變更偵測優化規則

## 規則 1：必須使用 OnPush 策略

**嚴重程度**：🔴 嚴重
**類別**：Change Detection

### 說明

所有 Component **必須** 使用 `ChangeDetectionStrategy.OnPush`。預設的變更偵測策略會在每次事件循環中檢查所有 Component，造成嚴重效能損耗。

### 錯誤寫法

```typescript
// ❌ 未設定 changeDetection，使用預設策略
@Component({
  selector: "app-example",
  standalone: true,
  templateUrl: "./example.component.html",
})
export class ExampleComponent {}

// ❌ 明確使用 Default 策略
@Component({
  selector: "app-example",
  changeDetection: ChangeDetectionStrategy.Default,
  standalone: true,
  templateUrl: "./example.component.html",
})
export class ExampleComponent {}
```

### 正確寫法

```typescript
// ✅ 使用 OnPush 策略
@Component({
  selector: "app-example",
  changeDetection: ChangeDetectionStrategy.OnPush,
  standalone: true,
  templateUrl: "./example.component.html",
})
export class ExampleComponent {}
```

---

## 規則 2：使用 Signal 進行狀態管理

**嚴重程度**：🟡 警告
**類別**：Change Detection

### 說明

搭配 OnPush 策略，應使用 `signal()`、`computed()`、`input()`、`output()` 進行狀態管理，確保 Angular 能正確追蹤狀態變更。

### 錯誤寫法

```typescript
// ❌ 使用一般屬性
export class ExampleComponent {
  title: string = "";
  items: Item[] = [];

  updateTitle(newTitle: string): void {
    this.title = newTitle; // OnPush 下可能不會觸發更新
  }
}
```

### 正確寫法

```typescript
// ✅ 使用 Signal
export class ExampleComponent {
  readonly title = signal<string>("");
  readonly items = signal<Item[]>([]);

  updateTitle(newTitle: string): void {
    this.title.set(newTitle); // 自動觸發變更偵測
  }
}
```

---

## 規則 3：避免在 OnPush Component 中手動觸發變更偵測

**嚴重程度**：🟡 警告
**類別**：Change Detection

### 說明

如果需要頻繁呼叫 `ChangeDetectorRef.detectChanges()` 或 `markForCheck()`，代表狀態管理設計有問題。應重構為 Signal-based 設計。

### 錯誤寫法

```typescript
// ❌ 頻繁手動觸發變更偵測
export class ExampleComponent {
  private readonly _cdr = inject(ChangeDetectorRef);
  data: string = "";

  loadData(): void {
    this._service.getData().subscribe((result) => {
      this.data = result;
      this._cdr.detectChanges(); // 反模式
    });
  }
}
```

### 正確寫法

```typescript
// ✅ 使用 Signal 自動追蹤
export class ExampleComponent {
  readonly data = signal<string>("");

  loadData(): void {
    this._service.getData().subscribe((result) => {
      this.data.set(result); // 自動觸發更新
    });
  }
}
```

---

## 規則 4：避免使用 Getter 產生新的物件參照

**嚴重程度**：🔴 嚴重
**類別**：Change Detection

### 說明

在 OnPush 策略下，如果 getter 每次都回傳新的物件參照，會導致子元件不必要的重新渲染。

### 錯誤寫法

```typescript
// ❌ Getter 每次產生新物件
export class ExampleComponent {
  private readonly _items = signal<Item[]>([]);

  // 每次呼叫都產生新陣列
  get filteredItems(): Item[] {
    return this._items().filter((item) => item.active);
  }
}
```

### 正確寫法

```typescript
// ✅ 使用 computed 確保參照穩定
export class ExampleComponent {
  private readonly _items = signal<Item[]>([]);

  // computed 只在相依值變更時重新計算
  readonly filteredItems = computed(() =>
    this._items().filter((item) => item.active),
  );
}
```

---

## 規則 5：Input/Output 必須使用 Signal-based API

**嚴重程度**：🟡 警告
**類別**：Change Detection

### 說明

使用 `input()` 和 `output()` 函式取代 `@Input()` 和 `@Output()` 裝飾器，可更好地與變更偵測系統整合。

### 錯誤寫法

```typescript
// ❌ 使用裝飾器
export class ChildComponent {
  @Input() userId!: string;
  @Output() selected = new EventEmitter<Item>();
}
```

### 正確寫法

```typescript
// ✅ 使用 Signal-based API
export class ChildComponent {
  readonly userId = input.required<string>();
  readonly selected = output<Item>();
}
```
