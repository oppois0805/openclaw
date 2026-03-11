````markdown
# Component 與 State 串接

## 核心原則

1. **Component 只能注入 Facade**，禁止直接注入 Store 或 Effects
2. **狀態綁定使用 computed**，確保響應式更新
3. **事件處理呼叫 Facade 方法**，不直接操作狀態

## 串接流程

```
Step 1: 注入 Facade
    ↓
Step 2: 建立狀態綁定（computed from Facade）
    ↓
Step 3: 實作事件處理（呼叫 Facade 方法）
    ↓
Step 4: 初始化（ngOnInit / effect）
    ↓
Step 5: 清理（ngOnDestroy）
```

## 基本結構

```typescript
import { Component, inject, computed, effect, OnDestroy } from '@angular/core';

@Component({
  template: `
    <!-- 狀態綁定 -->
    @if (isLoading()) {
      <loading-spinner />
    }

    <!-- 錯誤顯示 -->
    @if (showError()) {
      <error-message>{{ errorMessage() }}</error-message>
    }

    <!-- 表單綁定 -->
    <input
      [value]="formData().name"
      (input)="onNameInput($event)"
      (blur)="onNameBlur()"
    />

    <!-- 按鈕狀態 -->
    <button
      [disabled]="!isFormValid() || isSubmitting()"
      (click)="onSaveClick()"
    >
      Save
    </button>
  `
})
export class FeatureComponent implements OnDestroy {
  // ==================== Facade 注入 ====================
  private readonly facade = inject(FeatureFacade);

  // ==================== 狀態綁定（computed from Facade）====================

  /** 表單資料 */
  readonly formData = computed(() => this.facade.formData());

  /** 錯誤狀態 */
  readonly errors = this.facade.errors;

  /** 觸碰狀態 */
  readonly touched = this.facade.touched;

  /** 載入中 */
  readonly isLoading = this.facade.isLoading;

  /** 提交中 */
  readonly isSubmitting = this.facade.submitting;

  /** 表單有效 */
  readonly isFormValid = this.facade.isFormValid;

  // ==================== 衍生狀態 ====================

  /** 是否顯示錯誤 */
  readonly showError = computed(() => {
    const errors = this.errors();
    const touched = this.touched();
    return touched['name'] && !!errors.name;
  });

  /** 錯誤訊息 */
  readonly errorMessage = computed(() => this.errors().name ?? '');

  // ==================== 初始化 ====================

  constructor() {
    // 使用 effect 監聽外部輸入變化
    effect(() => {
      const data = this.inputData();
      if (data) {
        this.facade.initEditMode(data);
      } else {
        this.facade.initCreateMode();
      }
    });
  }

  ngOnDestroy(): void {
    this.facade.reset();
  }

  // ==================== 事件處理 ====================

  onNameInput(event: Event): void {
    const target = event.target as HTMLInputElement;
    this.facade.updateFormData({ name: target.value });
  }

  onNameBlur(): void {
    this.facade.setTouched({ name: true });
  }

  onSaveClick(): void {
    // 觸碰所有欄位
    this.facade.setTouched({ name: true });

    // 檢查有效性
    if (!this.isFormValid()) {
      return;
    }

    // 提交
    this.facade.submit();
  }
}
```

## 狀態綁定類型

| 類型       | 寫法                                | 說明               |
| ---------- | ----------------------------------- | ------------------ |
| 直接綁定   | `this.facade.items`                 | Signal 直接引用    |
| 計算綁定   | `computed(() => this.facade.xx())` | 需要轉換或組合     |
| 衍生狀態   | `computed(() => ...)`               | 結合多個狀態的邏輯 |

## 事件處理模式

```typescript
// ✅ 正確：透過 Facade 更新
onInput(event: Event): void {
  const value = (event.target as HTMLInputElement).value;
  this.facade.updateFormData({ field: value });
}

// ❌ 錯誤：直接操作 Store
onInput(event: Event): void {
  this.store.updateFormData({ field: value }); // 禁止！
}
```

## 驗證整合

```typescript
// 同步驗證：在 Component 執行，結果透過 Facade 設定
private validateField(value: string): void {
  const result = validator(value);
  this.facade.setErrors({ fieldName: result ? result.message : null });
}

// 非同步驗證：透過 Facade 觸發 Effects
onFieldBlur(): void {
  this.facade.setTouched({ fieldName: true });
  this.facade.validateFieldUnique(this.formData().fieldName);
}
```

## 常見錯誤

### ❌ 直接注入 Store

```typescript
// 錯誤
private readonly store = inject(FeatureStore);
```

### ❌ 直接注入 Effects

```typescript
// 錯誤
private readonly effects = inject(FeatureEffects);
```

### ❌ 在 Component 內處理副作用

```typescript
// 錯誤
onSave(): void {
  this.http.post('/api', data).subscribe(); // 應放在 Effects
}
```

### ✅ 正確做法

```typescript
// 正確：只注入 Facade
private readonly facade = inject(FeatureFacade);

// 正確：透過 Facade 觸發
onSave(): void {
  this.facade.submit();
}
```

````
