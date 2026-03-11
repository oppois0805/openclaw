# Effects 實作

> 參考: https://angular.dev/guide/signals/rxjs-interop

## 職責

Effects 負責處理**副作用**，包含 HTTP 請求、Timer、Router 等。

## 基本結構

```typescript
import { Injectable, inject, effect } from '@angular/core';
import { toSignal } from '@angular/core/rxjs-interop';

@Injectable({ providedIn: 'root' })
export class FeatureEffects {
  private readonly store = inject(FeatureStore);
  private readonly apiService = inject(FeatureApiService);

  // 載入資料
  load() {
    this.store.setLoading(true);
    this.store.setError(null);

    this.apiService.getItems().subscribe({
      next: (items) => {
        this.store.setItems(items);
        this.store.setLoading(false);
      },
      error: (err) => {
        this.store.setError(err.message);
        this.store.setLoading(false);
      }
    });
  }

  // 提交資料
  submit(data: FormData) {
    this.store.setLoading(true);

    this.apiService.create(data).subscribe({
      next: () => {
        this.store.setLoading(false);
        // 重新載入或其他處理
      },
      error: (err) => {
        this.store.setError(err.message);
        this.store.setLoading(false);
      }
    });
  }
}
```

## 使用 effect() 監聽變化

```typescript
constructor() {
  // 當 filter 變化時自動重新載入
  effect(() => {
    const filter = this.store.filter();
    this.load(filter);
  });
}
```
