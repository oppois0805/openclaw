# Store 實作

> 參考: https://angular.dev/guide/signals

## 職責

Store 負責**純狀態管理**，不包含任何副作用（HTTP、Timer 等）。

## 基本結構

```typescript
import { Injectable, signal, computed } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class FeatureStore {
  // 私有 state signal
  private readonly _state = signal<FeatureState>(initialState);

  // 公開唯讀 computed
  readonly items = computed(() => this._state().items);
  readonly isLoading = computed(() => this._state().isLoading);
  readonly error = computed(() => this._state().error);

  // Updater 方法（純狀態更新）
  setItems(items: Item[]) {
    this._state.update(state => ({ ...state, items }));
  }

  setLoading(isLoading: boolean) {
    this._state.update(state => ({ ...state, isLoading }));
  }

  setError(error: string | null) {
    this._state.update(state => ({ ...state, error }));
  }

  reset() {
    this._state.set(initialState);
  }
}
```

## State Interface

```typescript
// feature.state.ts
export interface FeatureState {
  items: Item[];
  isLoading: boolean;
  error: string | null;
}

export const initialState: FeatureState = {
  items: [],
  isLoading: false,
  error: null
};
```
