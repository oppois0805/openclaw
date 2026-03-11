# 跨欄位驗證器 (Cross-field Validators)

> 參考: https://angular.dev/guide/forms/form-validation#adding-cross-validation-to-reactive-forms

## 概念

跨欄位驗證器套用於 `FormGroup` 層級，可存取多個 `FormControl` 進行比對驗證。

## 建立跨欄位驗證器

```typescript
import { AbstractControl, ValidationErrors, ValidatorFn } from '@angular/forms';

export const identityRevealedValidator: ValidatorFn = (
  control: AbstractControl
): ValidationErrors | null => {
  const name = control.get('name');
  const alterEgo = control.get('alterEgo');

  return name && alterEgo && name.value === alterEgo.value
    ? { identityRevealed: true }
    : null;
};
```

## 套用至 FormGroup

```typescript
this.form = this.fb.group({
  name: [''],
  alterEgo: ['']
}, {
  validators: identityRevealedValidator
});
```

## 常見應用場景

| 場景 | 說明 |
|------|------|
| 密碼確認 | 比對兩個欄位值是否相同 |
| 日期範圍 | 驗證開始日期小於結束日期 |
| 條件必填 | 當欄位 A 有值時，欄位 B 必填 |

## 顯示 FormGroup 錯誤

```html
@if (form.errors?.['identityRevealed']) {
  <div class="error">Name cannot match alter ego.</div>
}
```
