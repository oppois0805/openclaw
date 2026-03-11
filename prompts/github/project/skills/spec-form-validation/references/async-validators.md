# 非同步驗證器 (Async Validators)

> 參考: https://angular.dev/guide/forms/form-validation#creating-async-validators

## AsyncValidatorFn 介面

```typescript
interface AsyncValidatorFn {
  (control: AbstractControl): Promise<ValidationErrors | null> | Observable<ValidationErrors | null>;
}
```

## 建立非同步驗證器

```typescript
import { AbstractControl, AsyncValidatorFn, ValidationErrors } from '@angular/forms';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';

export function uniqueEmailValidator(userService: UserService): AsyncValidatorFn {
  return (control: AbstractControl): Observable<ValidationErrors | null> => {
    return userService.checkEmailExists(control.value).pipe(
      map(exists => exists ? { emailExists: true } : null)
    );
  };
}
```

## 套用至 FormControl

非同步驗證器作為 `FormControl` 的第三個參數：

```typescript
this.form = this.fb.group({
  email: [
    '',
    [Validators.required, Validators.email],  // 同步驗證器
    [uniqueEmailValidator(this.userService)]  // 非同步驗證器
  ]
});
```

## 驗證狀態

| 屬性 | 說明 |
|------|------|
| `control.pending` | 非同步驗證進行中 |
| `control.status` | `'PENDING'` / `'VALID'` / `'INVALID'` |

## Template 中顯示狀態

```html
@if (control.pending) {
  <span>驗證中...</span>
}
```

## 效能考量

可考慮使用 `debounceTime` 減少 API 呼叫頻率：

```typescript
return of(control.value).pipe(
  debounceTime(300),
  switchMap(value => service.check(value)),
  map(result => result ? null : { error: true })
);
```
