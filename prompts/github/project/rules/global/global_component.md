# IWA Component 開發規範

> 本文件規範 Angular 20+ 專案中 Component 的開發標準與最佳實踐。

---

## 🎯 Component 基本設定

### 必要的 Component 設定

所有 Component **必須** 包含以下設定：

```typescript
@Component({
  selector: "app-example",
  changeDetection: ChangeDetectionStrategy.OnPush, // 必須
  standalone: true, // 必須
  templateUrl: "./example.component.html",
  styleUrls: ["./example.component.scss"],
})
export class ExampleComponent {}
```

### 變更偵測策略

**所有 Component 必須使用 `ChangeDetectionStrategy.OnPush`** 以優化效能。

---

## 📡 Signal-Based 狀態管理

所有狀態管理 **必須** 使用 Signal API：

### Input / Output

```typescript
export class ExampleComponent {
  // ✅ Signal-based inputs（取代 @Input）
  readonly userId = input.required<string>();
  readonly config = input<Config>({ theme: "light" });

  // ✅ Signal-based outputs（取代 @Output）
  readonly itemSelected = output<Item>();

  // ❌ 禁止：舊式裝飾器
  // @Input() userId: string;
  // @Output() itemSelected = new EventEmitter<Item>();
}
```

### 狀態與衍生狀態

```typescript
export class ExampleComponent {
  // 狀態管理使用 Signal
  readonly title = signal<string>("");
  readonly items = signal<Item[]>([]);
  readonly isLoading = signal<boolean>(false);

  // Computed signals 用於衍生狀態
  readonly itemCount = computed(() => this.items().length);
  readonly hasItems = computed(() => this.items().length > 0);

  // ✅ 使用 computed 取代 template 中的 function
  readonly formattedDate = computed(() =>
    this.date().toLocaleDateString("zh-TW")
  );
}
```

---

## 🔄 訂閱管理與清理

使用 `takeUntilDestroyed()` 或 Subject + `takeUntil()` 模式處理訂閱：

### 方式一：takeUntilDestroyed（推薦）

```typescript
export class FeatureComponent {
  private readonly _destroyRef = inject(DestroyRef);

  constructor() {
    this._service
      .getData()
      .pipe(takeUntilDestroyed(this._destroyRef))
      .subscribe((data) => this.items.set(data));
  }
}
```

### 方式二：傳統 takeUntil 模式

```typescript
export class FeatureComponent implements OnDestroy {
  private readonly _destroy$ = new Subject<void>();

  constructor() {
    this._anotherService
      .getStream()
      .pipe(takeUntil(this._destroy$))
      .subscribe((value) => this.processValue(value));
  }

  ngOnDestroy(): void {
    this._destroy$.next();
    this._destroy$.complete();
  }
}
```

---

## 💉 依賴注入

**推薦** 使用 `inject()` 函式而非 Constructor 注入：

```typescript
// ✅ 推薦：使用 inject()
export class FeatureComponent {
  private readonly _service = inject(FeatureService);
  private readonly _router = inject(Router);
  private readonly _destroyRef = inject(DestroyRef);
}

// ❌ 避免：Constructor 注入
export class FeatureComponent {
  constructor(
    private readonly _service: FeatureService,
    private readonly _router: Router
  ) {}
}
```

---

## 📋 完整 Component 範例

```typescript
import {
  Component,
  ChangeDetectionStrategy,
  inject,
  signal,
  computed,
  input,
  output,
  DestroyRef,
} from "@angular/core";
import { takeUntilDestroyed } from "@angular/core/rxjs-interop";

@Component({
  selector: "app-user-list",
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  templateUrl: "./user-list.component.html",
  styleUrls: ["./user-list.component.scss"],
  imports: [UserCardComponent, SpinnerComponent],
})
export class UserListComponent {
  private readonly _destroyRef = inject(DestroyRef);
  private readonly _userService = inject(UserService);

  // Inputs
  readonly searchTerm = input<string>("");

  // Outputs
  readonly userSelected = output<User>();

  // State
  readonly users = signal<User[]>([]);
  readonly isLoading = signal<boolean>(false);
  readonly error = signal<string | null>(null);

  // Computed
  readonly filteredUsers = computed(() => {
    const term = this.searchTerm().toLowerCase();
    return this.users().filter((user) =>
      user.name.toLowerCase().includes(term)
    );
  });

  readonly userCount = computed(() => this.filteredUsers().length);

  constructor() {
    this.loadUsers();
  }

  private loadUsers(): void {
    this.isLoading.set(true);

    this._userService
      .getUsers()
      .pipe(takeUntilDestroyed(this._destroyRef))
      .subscribe({
        next: (users) => {
          this.users.set(users);
          this.isLoading.set(false);
        },
        error: (err) => {
          this.error.set(err.message);
          this.isLoading.set(false);
        },
      });
  }

  onUserClick(user: User): void {
    this.userSelected.emit(user);
  }
}
```

---

## ✅ Component 檢查清單

### 必須項目

- [ ] 使用 `ChangeDetectionStrategy.OnPush`
- [ ] 使用 `standalone: true`
- [ ] 使用 Signal-based `input()` 和 `output()`
- [ ] 狀態使用 `signal()` 和 `computed()` 管理
- [ ] 使用 `inject()` 函式進行依賴注入
- [ ] 所有訂閱使用 `takeUntilDestroyed()` 或 `takeUntil()` 處理

### 禁止項目

- [ ] ❌ 不使用 `@Input()` 和 `@Output()` 裝飾器
- [ ] ❌ 不在 Component 中寫複雜業務邏輯
- [ ] ❌ 不使用 Constructor 注入（改用 `inject()`）
- [ ] ❌ 不使用 `ChangeDetectionStrategy.Default`

---

## 🔗 相關參考文件

- **HTML Template 規範**：`global_html.md`
- **SCSS 規範**：`global_scss.md`
- **Service 三層架構規範**：`folder-system_api.md`
