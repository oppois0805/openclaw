# 訂閱與記憶體管理規則

## 規則 1：訂閱必須清理

**嚴重程度**：🔴 嚴重
**類別**：Memory Leak

### 說明

未清理的 Observable 訂閱會造成記憶體洩漏，且可能在 Component 銷毀後繼續執行邏輯。

### 錯誤寫法

```typescript
// ❌ 訂閱未清理
export class ExampleComponent {
  constructor() {
    this._service.getData().subscribe((data) => {
      this.data.set(data);
    });

    interval(1000).subscribe(() => {
      console.log("tick"); // 永遠不會停止
    });
  }
}
```

### 正確寫法

```typescript
// ✅ 使用 takeUntilDestroyed（推薦）
export class ExampleComponent {
  private readonly _destroyRef = inject(DestroyRef);

  constructor() {
    this._service
      .getData()
      .pipe(takeUntilDestroyed(this._destroyRef))
      .subscribe((data) => {
        this.data.set(data);
      });
  }
}

// ✅ 使用 takeUntil + Subject
export class ExampleComponent implements OnDestroy {
  private readonly _destroy$ = new Subject<void>();

  constructor() {
    interval(1000)
      .pipe(takeUntil(this._destroy$))
      .subscribe(() => {
        console.log("tick");
      });
  }

  ngOnDestroy(): void {
    this._destroy$.next();
    this._destroy$.complete();
  }
}
```

---

## 規則 2：Middleware 必須在 ngOnDestroy 呼叫 destroy()

**嚴重程度**：🔴 嚴重
**類別**：Memory Leak

### 說明

Middleware 內部可能持有訂閱或其他資源，必須在 Component 銷毀時呼叫 `destroy()` 方法清理。

### 錯誤寫法

```typescript
// ❌ 未清理 Middleware
export class FeatureComponent {
  private readonly _middleware = inject(FeatureMiddleware);

  // 沒有實作 ngOnDestroy
}
```

### 正確寫法

```typescript
// ✅ 在 ngOnDestroy 清理 Middleware
export class FeatureComponent implements OnDestroy {
  private readonly _middleware = inject(FeatureMiddleware);

  ngOnDestroy(): void {
    this._middleware.destroy();
  }
}

// ✅ 或使用 DestroyRef
export class FeatureComponent {
  private readonly _middleware = inject(FeatureMiddleware);

  constructor() {
    inject(DestroyRef).onDestroy(() => {
      this._middleware.destroy();
    });
  }
}
```

---

## 規則 3：避免巢狀訂閱

**嚴重程度**：🟡 警告
**類別**：Subscription

### 說明

巢狀訂閱難以管理且容易造成記憶體洩漏，應使用 Higher-Order Operators。

### 錯誤寫法

```typescript
// ❌ 巢狀訂閱
this._userService.getUser().subscribe((user) => {
  this._orderService.getOrders(user.id).subscribe((orders) => {
    this._itemService.getItems(orders[0].id).subscribe((items) => {
      this.items.set(items);
    });
  });
});
```

### 正確寫法

```typescript
// ✅ 使用 switchMap、mergeMap 等 Higher-Order Operators
this._userService
  .getUser()
  .pipe(
    switchMap((user) => this._orderService.getOrders(user.id)),
    switchMap((orders) => this._itemService.getItems(orders[0].id)),
    takeUntilDestroyed(this._destroyRef),
  )
  .subscribe((items) => {
    this.items.set(items);
  });
```

---

## 規則 4：避免過度訂閱

**嚴重程度**：🟡 警告
**類別**：Subscription

### 說明

多個獨立訂閱同一個 Observable 應使用 `shareReplay()` 或合併為一個訂閱。

### 錯誤寫法

```typescript
// ❌ 重複訂閱相同的 Observable
constructor() {
  // 這會發出兩次 HTTP 請求
  this._service.getData()
    .pipe(takeUntilDestroyed(this._destroyRef))
    .subscribe(data => this.items.set(data));

  this._service.getData()
    .pipe(takeUntilDestroyed(this._destroyRef))
    .subscribe(data => this.count.set(data.length));
}
```

### 正確寫法

```typescript
// ✅ 合併為單一訂閱
constructor() {
  this._service.getData()
    .pipe(takeUntilDestroyed(this._destroyRef))
    .subscribe(data => {
      this.items.set(data);
      this.count.set(data.length);
    });
}

// ✅ 或使用 shareReplay
private readonly data$ = this._service.getData().pipe(
  shareReplay(1)
);

constructor() {
  this.data$
    .pipe(takeUntilDestroyed(this._destroyRef))
    .subscribe(data => this.items.set(data));

  this.data$
    .pipe(takeUntilDestroyed(this._destroyRef))
    .subscribe(data => this.count.set(data.length));
}
```

---

## 規則 5：effect() 清理函式

**嚴重程度**：🟡 警告
**類別**：Memory Leak

### 說明

如果 `effect()` 中建立了需要清理的資源（如定時器、事件監聽器），必須使用 `onCleanup` 回呼進行清理。

### 錯誤寫法

```typescript
// ❌ effect 內建立的資源未清理
constructor() {
  effect(() => {
    const id = this.itemId();
    const interval = setInterval(() => {
      this.refresh(id);
    }, 5000);
    // interval 永遠不會被清理
  });
}
```

### 正確寫法

```typescript
// ✅ 使用 onCleanup 清理
constructor() {
  effect((onCleanup) => {
    const id = this.itemId();
    const intervalId = setInterval(() => {
      this.refresh(id);
    }, 5000);

    onCleanup(() => {
      clearInterval(intervalId);
    });
  });
}
```

---

## 規則 6：避免在 effect 中訂閱 Observable

**嚴重程度**：🟡 警告
**類別**：Subscription

### 說明

在 `effect()` 中訂閱 Observable 容易造成訂閱堆積。應使用 `toSignal()` 或在 effect 外處理。

### 錯誤寫法

```typescript
// ❌ 在 effect 中訂閱
constructor() {
  effect(() => {
    const id = this.itemId();
    this._service.getItem(id).subscribe(item => {
      this.item.set(item);
    });
    // 每次 itemId 變更都會新增一個訂閱
  });
}
```

### 正確寫法

```typescript
// ✅ 使用 toSignal 搭配 computed
readonly itemId = signal<string>('');
readonly item$ = computed(() =>
  this._service.getItem(this.itemId())
);
readonly item = toSignal(
  toObservable(this.item$).pipe(switchMap(obs$ => obs$))
);

// ✅ 或使用 effect + onCleanup
constructor() {
  effect((onCleanup) => {
    const id = this.itemId();
    const subscription = this._service.getItem(id)
      .subscribe(item => this.item.set(item));

    onCleanup(() => subscription.unsubscribe());
  });
}
```

---

## 規則 7：EventEmitter 應使用 output() 替代

**嚴重程度**：🟢 建議
**類別**：Memory

### 說明

`output()` 函式比 `EventEmitter` 更輕量，且不需要手動清理。

### 可改進寫法

```typescript
// 可改進：使用 EventEmitter
export class ChildComponent {
  @Output() selected = new EventEmitter<Item>();

  onSelect(item: Item): void {
    this.selected.emit(item);
  }
}
```

### 建議寫法

```typescript
// ✅ 使用 output()
export class ChildComponent {
  readonly selected = output<Item>();

  onSelect(item: Item): void {
    this.selected.emit(item);
  }
}
```
