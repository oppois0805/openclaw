## 🎯 目標

* 以 **Angular Signals** 建立可維護、可測試、可擴展的狀態管理層。
* 採用「**功能內聚、責任分離**」：`store` 純更新、`effects` 純副作用、`facade` 對外唯讀 + 命令。

---

## 📂 目錄結構（僅 State Management）

```plaintext
src/app/
├─ core/
│  └─ state/
│     ├─ primitives/     # 基礎：不可變更新、deepFreeze、typed helpers
│     ├─ stores/         # 全域 store（app/auth/layout）
│     ├─ effects/        # 全域副作用（認證刷新/主題切換等）
│     ├─ facades/        # 全域唯讀介面 + 命令方法
│     ├─ models/         # AppState/AuthState 等全域狀態模型
│     └─ utils/          # toSignal/toObservable、持久化、memo
└─ features/{feature}/
   └─ state/
      ├─ {feature}.state.ts    # 初始狀態與介面定義
      ├─ {feature}.store.ts    # Store：signals 與純 updaters
      ├─ {feature}.effects.ts  # Effects：HTTP/Timer/Router 等副作用
      └─ {feature}.facade.ts   # Facade：唯讀 signals 與命令方法
```

---

## 🧱 Store（純狀態）

* 使用 `signal()` 管理狀態，更新僅用 `set`/`update`（不可變）。
* 只負責**純邏輯**：新增、移除、置換、計數等，不包含任何 I/O 或外部副作用。
* 透過 `computed()` 暴露衍生資料，保持純粹與可快取。

**範例：**

```ts
// features/todos/state/todos.state.ts
export interface TodosState {
  items: string[];
  filter: 'ALL'|'ACTIVE'|'COMPLETED';
}
export const initialTodosState: TodosState = { items: [], filter: 'ALL' };

// features/todos/state/todos.store.ts
@Injectable({ providedIn: 'root' })
export class TodosStore {
  private readonly init = initialTodosState;
  readonly state = signal<TodosState>({ ...this.init });
  readonly items = computed(() => this.state().items);
  readonly filter = computed(() => this.state().filter);

  add(title: string) {
    this.state.update(s => ({ ...s, items: [...s.items, title] }));
  }
  remove(title: string) {
    this.state.update(s => ({ ...s, items: s.items.filter(i => i !== title) }));
  }
  setFilter(f: TodosState['filter']) {
    this.state.update(s => ({ ...s, filter: f }));
  }
  reset() { this.state.set({ ...this.init }); }
}
```

---

## ⚡ Effects（副作用）

* 使用 `effect()` 隔離異步與外部互動（HTTP、Timer、Router、Storage）。
* 需要寫入 signal 時，加入 `{ allowSignalWrites: true }`。
* 在 `effect(onCleanup => ...)` 中清理訂閱/計時器。

**範例：**

```ts
// features/todos/state/todos.effects.ts
@Injectable({ providedIn: 'root' })
export class TodosEffects {
  constructor(private store: TodosStore, private api: TodosApi) {}

  readonly load = effect(async () => {
    const data = await this.api.list();
    this.store.state.update(s => ({ ...s, items: data }));
  }, { allowSignalWrites: true });
}
```

---

## 🎭 Facade（對外介面）

* **只暴露唯讀 signals**：`asReadonly()` 或直接暴露 computed 的唯讀取值。
* **只提供命令方法**：語意清楚的動詞（`addTodo`/`setFilter`/`loadTodos`）。
* 隱藏內部實作（store/effects），元件只需注入 facade。

**範例：**

```ts
// features/todos/state/todos.facade.ts
@Injectable({ providedIn: 'root' })
export class TodosFacade {
  constructor(private store: TodosStore, private fx: TodosEffects) {}

  readonly items = this.store.items;  // 唯讀信號
  readonly filter = this.store.filter; // 唯讀信號
  readonly filtered = computed(() => {
    const { items, filter } = this.store.state();
    return items.filter(i => filter === 'ALL' ? true : filter === 'ACTIVE' ? !i.startsWith('✔') : i.startsWith('✔'));
  });

  addTodo = (t: string) => this.store.add(t);
  setFilter = (f: TodosState['filter']) => this.store.setFilter(f);
  loadTodos = () => this.fx.load();
}
```

---

## 🧩 Primitives & Utils（基礎/工具）

**不可變更新**

```ts
// core/state/primitives/patch-state.ts
export function patchState<S>(s: S, p: Partial<S>): S { return { ...s, ...p }; }
```

**RxJS 互通**

```ts
// core/state/utils/rxjs-interop.ts
import { toSignal, toObservable } from '@angular/core/rxjs-interop';
export const asSignal = <T>(source$: Observable<T>, initial: T) => toSignal(source$, { initialValue: initial });
export const asObservable = <T>(sig: Signal<T>) => toObservable(sig);
```

**持久化（選用）**

```ts
// core/state/utils/persist.ts
export function persistSignal<T>(key: string, sig: WritableSignal<T>, serialize = JSON.stringify, parse = JSON.parse) {
  const saved = localStorage.getItem(key);
  if (saved != null) sig.set(parse(saved));
  effect(() => localStorage.setItem(key, serialize(sig())));
}
```

---

## 🔁 整合流程（從元件視角）

1. 元件 **只注入 Facade**。
2. 讀取唯讀 signals 綁定模板；透過命令方法觸發行為。
3. Facade 內部呼叫 store（純更新）或 effects（副作用）。

**極簡元件片段：**

```ts
@Component({ /* ... */ })
export class TodosPage {
  readonly items = this.facade.items;
  readonly filtered = this.facade.filtered;
  constructor(private facade: TodosFacade) {}
  add(t: string) { this.facade.addTodo(t); }
  ngOnInit() { this.facade.loadTodos(); }
}
```

---

## 🧪 測試建議（State-Only）

* **Store**：呼叫 updater 後斷言狀態與不可變性（`expect(after).not.toBe(before)`）。
* **Effects**：mock API，驗證副作用完成後 store 被正確更新（含錯誤情境）。
* **Facade**：spy store/effects，驗證命令方法是否正確委派與唯讀 signals 是否正確映射。

**Store 測試範例：**

```ts
describe('TodosStore', () => {
  let store: TodosStore;
  beforeEach(() => TestBed.configureTestingModule({ providers: [TodosStore] }));
  beforeEach(() => store = TestBed.inject(TodosStore));

  it('adds immutably', () => {
    const before = store.state();
    store.add('Learn Signals');
    const after = store.state();
    expect(after.items).toContain('Learn Signals');
    expect(after).not.toBe(before);
  });
});
```

---

## 📏 命名與約定（State-Only）

* 檔案：`*.state.ts` / `*.store.ts` / `*.effects.ts` / `*.facade.ts`
* 類別：`XxxState` / `XxxStore` / `XxxEffects` / `XxxFacade`
* 方法：動詞為主（`setX/addX/removeX/toggleX`），computed 用名詞/形容詞（`isBusy/remainingCount`）。

---

## ✅ 狀態層檢查清單

* [ ] 元件不直接操作 signals；**只透過 Facade**。
* [ ] **所有寫入** 僅發生在 **Store/Effects**。
* [ ] **不可變更新**：`set/update` 產生新物件；避免原地突變。
* [ ] `computed()` **純粹** 且可快取；昂貴計算放 computed。
* [ ] `effect()` 寫入需 `{ allowSignalWrites: true }`；使用 `onCleanup` 做清理。
* [ ] 對外僅暴露 **唯讀 signals** 或命令方法。
* [ ] RxJS 整合使用 `toSignal/toObservable`；注意生命週期與退訂。
* [ ] 可選：`persistSignal`/`deepFreeze` 於開發模式強化穩定性。

---

> **交付要求（給 AI）**：
>
> 1. 針對指定 `{feature}` 建立 `state/` 四件套（state/store/effects/facade）。
> 2. Store 僅含純更新；Effects 僅含副作用；Facade 僅暴露唯讀與命令。
> 3. 提供最小可運作範例（含 1–2 個 updater、1 個 effect）。
> 4. 產出程式碼須符合以上命名與不可變更新原則。
