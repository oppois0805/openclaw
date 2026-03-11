# 同步驗證器 (Sync Validators)

> 參考: https://angular.dev/guide/forms/form-validation#adding-custom-validators-to-reactive-forms

## ValidatorFn 介面

```typescript
interface ValidatorFn {
  (control: AbstractControl): ValidationErrors | null;
}
```

## 建立自訂驗證器

```typescript
import { AbstractControl, ValidationErrors, ValidatorFn } from '@angular/forms';

export function forbiddenNameValidator(nameRe: RegExp): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const forbidden = nameRe.test(control.value);
    return forbidden ? { forbiddenName: { value: control.value } } : null;
  };
}
```

## 套用至 FormControl

```typescript
this.form = this.fb.group({
  name: ['', [Validators.required, forbiddenNameValidator(/bob/i)]]
});
```

## 內建驗證器

Angular 提供以下內建驗證器：

| 驗證器 | 說明 |
|--------|------|
| `Validators.required` | 必填 |
| `Validators.requiredTrue` | 必須為 true |
| `Validators.min(n)` | 最小值 |
| `Validators.max(n)` | 最大值 |
| `Validators.minLength(n)` | 最小長度 |
| `Validators.maxLength(n)` | 最大長度 |
| `Validators.pattern(regex)` | 正規表達式 |
| `Validators.email` | Email 格式 |

> 完整 API: https://angular.dev/api/forms/Validators
