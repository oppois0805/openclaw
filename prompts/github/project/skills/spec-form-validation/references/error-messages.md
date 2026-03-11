# 錯誤訊息處理

> 參考: https://angular.dev/guide/forms/form-validation#displaying-form-validation-errors

## 顯示驗證錯誤

```html
@if (name.errors?.['required']) {
  <span>Name is required.</span>
}
@if (name.errors?.['minlength']) {
  <span>Name must be at least {{ name.errors['minlength'].requiredLength }} characters.</span>
}
```

## ValidationErrors 結構

不同驗證器回傳的錯誤物件結構：

| 驗證器 | 錯誤物件 |
|--------|----------|
| `required` | `{ required: true }` |
| `minLength(n)` | `{ minlength: { requiredLength: n, actualLength: x } }` |
| `maxLength(n)` | `{ maxlength: { requiredLength: n, actualLength: x } }` |
| `pattern(regex)` | `{ pattern: { requiredPattern: string, actualValue: x } }` |
| `min(n)` | `{ min: { min: n, actual: x } }` |
| `max(n)` | `{ max: { max: n, actual: x } }` |
| `email` | `{ email: true }` |

## 自訂錯誤物件

自訂驗證器可回傳任意結構的錯誤物件：

```typescript
return { customError: { expectedValue: 'foo', actualValue: control.value } };
```

## 錯誤訊息元件範例

可建立可重用的錯誤訊息元件：

```typescript
@Component({
  selector: 'app-field-error',
  template: `
    @if (control()?.invalid && control()?.touched) {
      <small class="error">{{ errorMessage() }}</small>
    }
  `
})
export class FieldErrorComponent {
  control = input.required<AbstractControl>();
  
  errorMessage = computed(() => {
    const errors = this.control()?.errors;
    if (!errors) return '';
    // 依據 errors 物件組合訊息
    return this.getMessageForError(errors);
  });
}
```
