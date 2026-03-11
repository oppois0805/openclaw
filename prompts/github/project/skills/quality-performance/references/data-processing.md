# 資料處理效能規則

## 規則 1：避免在迴圈中進行重複計算

**嚴重程度**：🔴 嚴重
**類別**：Data Processing

### 說明

在迴圈中重複進行相同的計算會嚴重影響效能，應將計算結果快取。

### 錯誤寫法

```typescript
// ❌ 每次迭代都呼叫 getConfig()
for (const item of items) {
  const config = this.getConfig(); // 假設每次都一樣
  this.process(item, config);
}

// ❌ 在 map/filter 中重複計算
const result = items.map((item) => ({
  ...item,
  rate: this.calculateRate(), // 每個項目都重新計算
  tax: this.getTaxRate() * item.price,
}));
```

### 正確寫法

```typescript
// ✅ 預先計算並快取
const config = this.getConfig();
for (const item of items) {
  this.process(item, config);
}

// ✅ 預先取得常數值
const rate = this.calculateRate();
const taxRate = this.getTaxRate();
const result = items.map((item) => ({
  ...item,
  rate,
  tax: taxRate * item.price,
}));
```

---

## 規則 2：大量資料處理使用適當的資料結構

**嚴重程度**：🟡 警告
**類別**：Data Processing

### 說明

查找操作頻繁時，應使用 `Map` 或 `Set` 而非陣列的 `find()` 或 `includes()`。

### 錯誤寫法

```typescript
// ❌ 使用 find() 進行查找 - O(n)
const items: Item[] = [...];

function getItemById(id: string): Item | undefined {
  return items.find(item => item.id === id);
}

// 在迴圈中呼叫 - O(n²)
for (const order of orders) {
  const item = getItemById(order.itemId);
  // ...
}
```

### 正確寫法

```typescript
// ✅ 使用 Map 進行查找 - O(1)
const itemMap = new Map<string, Item>(items.map((item) => [item.id, item]));

function getItemById(id: string): Item | undefined {
  return itemMap.get(id);
}

// 在迴圈中呼叫 - O(n)
for (const order of orders) {
  const item = getItemById(order.itemId);
  // ...
}
```

### Set 使用範例

```typescript
// ❌ 使用 includes() - O(n)
const selectedIds: string[] = [...];
const isSelected = (id: string) => selectedIds.includes(id);

// ✅ 使用 Set - O(1)
const selectedIdSet = new Set(selectedIds);
const isSelected = (id: string) => selectedIdSet.has(id);
```

---

## 規則 3：避免不必要的陣列複製

**嚴重程度**：🟡 警告
**類別**：Data Processing

### 說明

頻繁使用展開運算子或 `slice()` 複製大型陣列會造成記憶體壓力。

### 錯誤寫法

```typescript
// ❌ 每次操作都複製整個陣列
let items = [...originalItems];
items = [...items, newItem];
items = items.filter((i) => i.active);
items = [...items].sort((a, b) => a.name.localeCompare(b.name));
```

### 正確寫法

```typescript
// ✅ 鏈式操作，減少中間複製
const items = originalItems
  .filter(i => i.active)
  .concat(newItem)
  .sort((a, b) => a.name.localeCompare(b.name));

// ✅ 使用 Signal 並一次性更新
readonly items = signal<Item[]>([]);

addAndSort(newItem: Item): void {
  this.items.update(current =>
    [...current, newItem].sort((a, b) => a.name.localeCompare(b.name))
  );
}
```

---

## 規則 4：computed 中避免副作用

**嚴重程度**：🔴 嚴重
**類別**：Data Processing

### 說明

`computed()` 應該是純函式，不應有副作用。副作用應放在 `effect()` 中。

### 錯誤寫法

```typescript
// ❌ computed 中有副作用
readonly processedItems = computed(() => {
  const items = this.items();
  console.log('Processing items:', items.length); // 副作用
  this.logService.log('computed executed'); // 副作用
  localStorage.setItem('lastCount', items.length.toString()); // 副作用
  return items.map(i => ({ ...i, processed: true }));
});
```

### 正確寫法

```typescript
// ✅ computed 保持純淨
readonly processedItems = computed(() =>
  this.items().map(i => ({ ...i, processed: true }))
);

// ✅ 副作用放在 effect
constructor() {
  effect(() => {
    const items = this.items();
    console.log('Items updated:', items.length);
    this.logService.log('items changed');
    localStorage.setItem('lastCount', items.length.toString());
  });
}
```

---

## 規則 5：避免深層物件複製

**嚴重程度**：🟡 警告
**類別**：Data Processing

### 說明

深層物件複製（如 `JSON.parse(JSON.stringify())` 或 `structuredClone()`）效能較差，應只在必要時使用。

### 錯誤寫法

```typescript
// ❌ 頻繁深層複製
updateItem(id: string, changes: Partial<Item>): void {
  this.items.update(current =>
    current.map(item =>
      item.id === id
        ? JSON.parse(JSON.stringify({ ...item, ...changes })) // 不必要的深層複製
        : item
    )
  );
}
```

### 正確寫法

```typescript
// ✅ 僅複製必要的層級
updateItem(id: string, changes: Partial<Item>): void {
  this.items.update(current =>
    current.map(item =>
      item.id === id
        ? { ...item, ...changes } // 淺層複製足夠
        : item
    )
  );
}

// ✅ 如確實需要深層複製，使用 structuredClone（比 JSON 方式快）
const deepCopy = structuredClone(original);
```

---

## 規則 6：分頁或分批處理大量資料

**嚴重程度**：🔴 嚴重
**類別**：Data Processing

### 說明

一次處理/渲染大量資料會阻塞 UI。應使用分頁、虛擬滾動或分批處理。

### 錯誤寫法

```typescript
// ❌ 一次載入全部資料
async loadAllData(): Promise<void> {
  const allItems = await this._service.getAllItems(); // 可能有 10000 筆
  this.items.set(allItems);
}
```

### 正確寫法

```typescript
// ✅ 分頁載入
readonly pageSize = 50;
readonly currentPage = signal(1);
readonly items = signal<Item[]>([]);
readonly hasMore = signal(true);

async loadPage(): Promise<void> {
  const page = this.currentPage();
  const result = await this._service.getItems(page, this.pageSize);

  this.items.update(current => [...current, ...result.items]);
  this.hasMore.set(result.hasMore);
  this.currentPage.update(p => p + 1);
}

// ✅ 使用 Virtual Scroll（參見 template-performance.md）
```

---

## 規則 7：非同步操作使用適當的並發控制

**嚴重程度**：🟡 警告
**類別**：Data Processing

### 說明

大量並發請求可能造成瀏覽器或伺服器過載，應使用適當的並發控制。

### 錯誤寫法

```typescript
// ❌ 同時發送 1000 個請求
async processAll(ids: string[]): Promise<void> {
  await Promise.all(
    ids.map(id => this._service.process(id))
  );
}
```

### 正確寫法

```typescript
// ✅ 使用 RxJS 的 mergeMap 控制並發數
import { from } from 'rxjs';
import { mergeMap, toArray } from 'rxjs/operators';

processAll(ids: string[]): Observable<Result[]> {
  return from(ids).pipe(
    mergeMap(id => this._service.process(id), 5), // 最多 5 個並發
    toArray()
  );
}

// ✅ 或使用批次處理
async processAllBatched(ids: string[]): Promise<void> {
  const batchSize = 10;
  for (let i = 0; i < ids.length; i += batchSize) {
    const batch = ids.slice(i, i + batchSize);
    await Promise.all(batch.map(id => this._service.process(id)));
  }
}
```

---

## 規則 8：debounce 高頻事件

**嚴重程度**：🟢 建議
**類別**：Data Processing

### 說明

搜尋輸入、視窗縮放等高頻事件應使用 `debounceTime()` 避免過度處理。

### 可改進寫法

```typescript
// 可改進：每次按鍵都觸發搜尋
onSearch(term: string): void {
  this._service.search(term).subscribe(results => {
    this.results.set(results);
  });
}
```

### 建議寫法

```typescript
// ✅ 使用 debounce
private readonly searchTerm$ = new Subject<string>();

constructor() {
  this.searchTerm$
    .pipe(
      debounceTime(300),
      distinctUntilChanged(),
      switchMap(term => this._service.search(term)),
      takeUntilDestroyed(this._destroyRef)
    )
    .subscribe(results => {
      this.results.set(results);
    });
}

onSearch(term: string): void {
  this.searchTerm$.next(term);
}
```
