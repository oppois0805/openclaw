---
name: state-management
description: "狀態管理實作技能：當處理 Angular Signal-based 狀態管理、建立 Store/Effects/Facade 架構、處理複雜資料流時使用。架構分層：Component → Facade（對外介面）→ Store（純狀態，signal/computed）↔ Effects（副作用，effect/RxJS interop）。包含 Signal API 使用（signal/computed/effect）、狀態更新規範、RxJS 整合、副作用處理。適用於複雜狀態管理、資料流設計、多元件狀態共享。"
---

# State Management

實作 Angular Signal-based 狀態管理架構。

## 官方文件

- [Signals](https://angular.dev/guide/signals)
- [RxJS Interop](https://angular.dev/guide/signals/rxjs-interop)

## 架構概覽

```
Component
    ↓ 呼叫
Facade（對外介面）
    ↓ 協調
Store（純狀態） ←→ Effects（副作用）
```

## 參考資料

| 主題              | 參考文件                                                               |
| ----------------- | ---------------------------------------------------------------------- |
| Store 實作        | [references/store.md](references/store.md)                             |
| Effects 實作      | [references/effects.md](references/effects.md)                         |
| Facade 實作       | [references/facade.md](references/facade.md)                           |
| Component 串接    | [references/component-integration.md](references/component-integration.md) |
