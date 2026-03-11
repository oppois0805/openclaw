# Facade 實作

## 職責

Facade 是 Component 存取狀態的**唯一介面**，整合 Store 與 Effects。

## 基本結構

```typescript
import { Injectable, inject } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class FeatureFacade {
  private readonly store = inject(FeatureStore);
  private readonly effects = inject(FeatureEffects);

  // 唯讀 signals（供 Component 綁定）
  readonly items = this.store.items;
  readonly isLoading = this.store.isLoading;
  readonly error = this.store.error;

  // 命令方法（觸發 Effects）
  load() {
    this.effects.load();
  }

  submit(data: FormData) {
    this.effects.submit(data);
  }

  reset() {
    this.store.reset();
  }
}
```

## Component 使用方式

```typescript
@Component({
  template: `
    @if (facade.isLoading()) {
      <loading-spinner />
    }
    
    @for (item of facade.items(); track item.id) {
      <item-card [item]="item" />
    }
  `
})
export class FeatureComponent {
  protected readonly facade = inject(FeatureFacade);

  ngOnInit() {
    this.facade.load();
  }
}
```
