## 🎯 HTML 元素識別規範（data-testid）

### **強制要求**
所有功能開發都**必須**為 HTML 元素添加 `data-testid` 屬性作為唯一標準。

### **⚠️ 核心原則**
1. **純排版 div 不需要標識** - 只有內容元素、互動元素、功能性容器才需要
2. **🔑 必須支援唯一識別 Key** - 避免同一組件多實例時的 ID 重複問題
3. **流水號永遠在最後** - 格式：`{prefixKeyForTestId}_{描述}_{類型}_{流水號}`
4. **統一使用 data-testid** - 避免與第三方套件 ID 衝突
5. **使用列舉組合** - 禁止硬編碼字串
6. **純顯示元素統一使用 content 類型** - 簡化命名邏輯，不依賴 HTML 標籤類型
7. **⚡ 禁止在 HTML 範本中呼叫函式** - 避免變更偵測時的效能問題，直接在範本中組合字串

---

## 📏 基本格式

```html
<div [attr.data-testid]="{prefixKeyForTestId}_{描述}_{類型}_{流水號}"></div>
```

### **格式組成**
| 組成部分 | 說明 | 必需性 | 範例 |
|---|---|---|---|
| **🔑 prefixKeyForTestId** | 組件唯一識別 key | 多實例時必需 | `sidebar`, `modal`, `main` |
| **描述** | 元件描述，多字用 `-` 串接，**禁止串類型** | 必需 | `baud-rate`, `user-list` |
| **類型** | 元件類型 | 必需 | `button`, `content`, `icon` |
| **流水號** | 從 1 開始的序號 | 視情況 | `1`, `2`, `3` |


### **⚠️ 描述命名規則**
- ✅ **正確**：`save_button` - 描述只包含功能說明
- ❌ **錯誤**：`save-button_button` - 描述中不可包含類型名稱
- ✅ **正確**：`user-list_content` - 清晰的描述與類型分離
- ❌ **錯誤**：`user-list-content_content` - 避免描述與類型重複）


### **🔑 唯一識別 Key 的重要性**

#### **問題場景**
當同一個組件在同一畫面中使用多次時，會產生重複的 `data-testid`：

```html
<!-- ❌ 問題：兩個組件產生相同的 data-testid -->
<user-list></user-list>     <!-- 產生：user-list_container_content -->
<user-list></user-list>     <!-- 產生：user-list_container_content (重複！) -->
```

#### **解決方案**
每個組件都必須支援 `prefixKeyForTestId` input：

```html
<!-- ✅ 解決：使用 prefixKeyForTestId 區分不同實例 -->
<user-list [prefixKeyForTestId]="active"></user-list>    <!-- 產生：active_user-list_container_content -->
<user-list [prefixKeyForTestId]="inactive"></user-list>  <!-- 產生：inactive_user-list_container_content -->
```

> **🚨 關鍵規則：流水號永遠在最後**  
> ✅ 正確：`sidebar_item_edit_icon_1`  
> ❌ 錯誤：`sidebar_item_1_edit_icon`

---

## 🎯 什麼元素需要 TestID？

| 元素類型 | 是否需要 | 範例 |
|---|---|---|
| **內容元素** | ✅ 需要 | `<div>文字內容</div>` |
| **互動元素** | ✅ 需要 | `<button>`, `<input>`, `<iwa-icon>` |
| **功能性容器** | ✅ 需要 | 表格容器、對話框 |
| **純排版容器** | ❌ 不需要 | 純 CSS 佈局的 `<div>` |
| **第三方組件** | ✅ 需要 | `<p-tabs>`, `<p-table>` |

## 🏷️ 元素類型選擇規則

### **純顯示元素 → 使用 `content` 類型**
適用於只負責顯示內容、無互動功能的元素：

| HTML 標籤 | 使用 content 的情況 | 範例 |
|-----------|-------------------|------|
| `<div>`, `<section>`, `<article>` | 顯示文字、範本內容 | 頁首區域、內容區域 |
| `<nav>`, `<header>`, `<footer>` | 純顯示導航列、頁首、頁尾 | 不含互動的版面區塊 |
| `<p-tabpanel>`, `<p-tabpanels>` | 顯示標籤內容面板 | 標籤頁內容區域 |
| `<span>`, `<p>`, `<h1-h6>` | 文字顯示 | 標題、段落、標籤文字 |

### **功能性元素 → 使用特定類型**
根據實際功能選擇對應類型：

| 元素功能 | 使用類型 | 範例 |
|----------|---------|------|
| 點擊互動 | `button` | `<button>`, `<i>` (點擊事件) |
| 資料輸入 | `input` | `<input>`, `<textarea>`, `<select>` |
| 標籤切換 | `tab` | `<p-tab>`, `<p-tabs>` |
| 拖拽調整 | `handle` | 調整大小的控制項 |
| 容器包裝 | `container` | 具有特殊功能的容器 |

---

## 🔧 列舉定義規則

### **功能專用 ID**
```typescript

// 請確保有產生檔案!! {功能名稱}/enums/{功能名稱}-ids.enum.ts
// 只產生功能必要的 CardTabIds 
export enum CardTabIDs {
    CardTab = 'card-tab',
    Header = 'header',
    TabList = 'tab-list'
    ...
}
```

### **共用類型**
```typescript
// 請確保有產生檔案!! {功能名稱}/enums/element-type.enum.ts
// 只產生功能必要的 ElementType 
export enum ElementType {
    Button = 'button',
    Content = 'content',
    Icon = 'icon',
    Input = 'input'
    ... 
}
```

### **🔑 組件 prefixKeyForTestId 實作規範**

#### **⚠️ 實作方式判斷流程（重要）**

依照以下順序判斷使用哪種實作方式：

1. **首先判斷：組件是否會被動態生成？**
   - 動態生成場景：`ViewContainerRef.createComponent()`、`ComponentFactoryResolver`、動態載入模組
   - 如果 **是動態生成** → 跳到步驟 2（不能使用 Signal）
   - 如果 **否**（靜態聲明在模板中）→ 使用 **方案 A：Signal computed**（最推薦）

2. **判斷：組件是否使用 OnPush 變更偵測策略？**
   - 檢查 `@Component({ changeDetection: ChangeDetectionStrategy.OnPush })`
   - 如果 **是 OnPush** → 使用 **方案 B：屬性 + ngOnChanges**
   - 如果 **否**（預設策略）→ 使用 **方案 C：getter**

---

#### **方案 A：Signal computed（最推薦 - 靜態組件適用）**

**✅ 適用場景：**
- 組件是靜態聲明在模板中（非動態生成）
- 希望獲得最佳性能
- 使用 Angular 16+ 的現代專案

**❌ 不適用場景：**
- 組件會被動態生成（`ViewContainerRef.createComponent()` 等）
- 需要相容舊版 Angular

```typescript
export class MyComponent {
    // 必須添加 prefixKeyForTestId input
    prefixKeyForTestId = input<string>('');
    
    // ✅ Signal computed：自動追蹤依賴，只在依賴改變時重新計算
    testIdPrefix = computed(() => 
        this.prefixKeyForTestId() ? `${this.prefixKeyForTestId()}_` : ''
    );
    
    // 暴露列舉供範本使用
    readonly MyIDs = MyIDs;
    readonly ElementType = ElementType;
}
```

**HTML 使用：**
```html
<div [attr.data-testid]="testIdPrefix() + MyIDs.Container + '_' + ElementType.Content">
```

---

#### **方案 B：屬性 + ngOnChanges（動態生成 + OnPush 適用）**

**✅ 適用場景：**
- 組件會被動態生成
- 使用 `ChangeDetectionStrategy.OnPush`
- 需要確保變更偵測正確觸發

**❌ 不適用場景：**
- 靜態組件且可使用 Signal 時（應優先使用方案 A）

```typescript
import { ChangeDetectionStrategy, SimpleChanges, OnChanges } from '@angular/core';

@Component({
    changeDetection: ChangeDetectionStrategy.OnPush
})
export class MyComponent implements OnChanges {
    // 必須添加 prefixKeyForTestId input
    @Input() prefixKeyForTestId: string = '';
    
    // ✅ 使用屬性儲存計算結果
    testIdPrefix: string = '';
    
    // ✅ 在 ngOnChanges 中更新屬性
    ngOnChanges(changes: SimpleChanges): void {
        if (changes['prefixKeyForTestId']) {
            this.testIdPrefix = this.prefixKeyForTestId 
                ? `${this.prefixKeyForTestId}_` 
                : '';
        }
    }
    
    // 暴露列舉供範本使用
    readonly MyIDs = MyIDs;
    readonly ElementType = ElementType;
}
```

**HTML 使用：**
```html
<div [attr.data-testid]="testIdPrefix + MyIDs.Container + '_' + ElementType.Content">
```

---

#### **方案 C：getter（動態生成 + 預設策略適用）**

**✅ 適用場景：**
- 組件會被動態生成
- 使用預設變更偵測策略（非 OnPush）
- 程式碼簡潔度優先

**⚠️ 注意事項：**
- 不建議與 OnPush 策略一起使用
- 靜態組件應優先使用方案 A

```typescript
export class MyComponent {
    // 必須添加 prefixKeyForTestId input
    @Input() prefixKeyForTestId: string = '';
    
    // ✅ getter：在屬性被訪問時計算
    get testIdPrefix(): string {
        return this.prefixKeyForTestId ? `${this.prefixKeyForTestId}_` : '';
    }
    
    // 暴露列舉供範本使用
    readonly MyIDs = MyIDs;
    readonly ElementType = ElementType;
}
```

**HTML 使用：**
```html
<div [attr.data-testid]="testIdPrefix + MyIDs.Container + '_' + ElementType.Content">
```

---

#### **🔍 方案比較表**

| 特性 | 方案 A<br>Signal computed | 方案 B<br>屬性 + ngOnChanges | 方案 C<br>getter |
|------|---------------------------|------------------------------|------------------|
| **動態生成支援** | ❌ 不支援 | ✅ 支援 | ✅ 支援 |
| **OnPush 支援** | ✅✅✅ 完美 | ✅✅✅ 完美 | ⚠️ 可能有問題 |
| **性能** | ⭐⭐⭐⭐⭐ 最佳 | ⭐⭐⭐⭐ 良好 | ⭐⭐⭐ 中等 |
| **程式碼簡潔度** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Angular 版本** | 16+ | 所有版本 | 所有版本 |
| **推薦優先級** | 🥇 最優先<br>(靜態組件) | 🥈 次優先<br>(動態+OnPush) | 🥉 最後選擇<br>(動態+預設) |

### **🔧 自動補充規則**
> **使用到的 ID 必須在列舉中定義**  
> 當使用 `CardTabIDs.NewId` 時，必須在列舉檔案中存在 `NewId` 定義

---

## 💡 實際範例

### **🔑 唯一識別 Key 實作（重要）**

#### **組件 TypeScript 設定**

**依據判斷流程選擇適當方案：**

```typescript
// 方案 A：Signal computed（靜態組件 - 最推薦）
export class MyComponent {
    prefixKeyForTestId = input<string>('');
    testIdPrefix = computed(() => this.prefixKeyForTestId() ? `${this.prefixKeyForTestId()}_` : '');
    
    readonly MyIDs = MyIDs;
    readonly ElementType = ElementType;
}

// 方案 B：屬性 + ngOnChanges（動態生成 + OnPush）
@Component({
    changeDetection: ChangeDetectionStrategy.OnPush
})
export class MyComponent implements OnChanges {
    @Input() prefixKeyForTestId: string = '';
    testIdPrefix: string = '';
    
    ngOnChanges(changes: SimpleChanges): void {
        if (changes['prefixKeyForTestId']) {
            this.testIdPrefix = this.prefixKeyForTestId ? `${this.prefixKeyForTestId}_` : '';
        }
    }
    
    readonly MyIDs = MyIDs;
    readonly ElementType = ElementType;
}

// 方案 C：getter（動態生成 + 預設策略）
export class MyComponent {
    @Input() prefixKeyForTestId: string = '';
    
    get testIdPrefix(): string {
        return this.prefixKeyForTestId ? `${this.prefixKeyForTestId}_` : '';
    }
    
    readonly MyIDs = MyIDs;
    readonly ElementType = ElementType;
}
```

#### **HTML 範本實作**
```html
<!-- ✅ 方案 A (Signal)：使用 testIdPrefix() -->
<div [attr.data-testid]="testIdPrefix() + MyIDs.Container + '_' + ElementType.Content">

<!-- ✅ 方案 B (屬性)：使用 testIdPrefix -->
<div [attr.data-testid]="testIdPrefix + MyIDs.Container + '_' + ElementType.Content">

<!-- ✅ 方案 C (getter)：使用 testIdPrefix -->
<div [attr.data-testid]="testIdPrefix + MyIDs.Container + '_' + ElementType.Content">

<!-- ❌ 錯誤：呼叫函式會造成效能問題 -->
<div [attr.data-testid]="getTestId(MyIDs.Container, ElementType.Content)">

<!-- ❌ 錯誤：在範本中使用三元運算子，每次變更偵測都會執行 -->
<div [attr.data-testid]="(prefixKeyForTestId() ? prefixKeyForTestId() + '_' : '') + MyIDs.Container + '_' + ElementType.Content">
```

#### **使用範例與產生結果**
```html
<!-- 不同實例使用不同 prefixKeyForTestId -->
<user-list [prefixKeyForTestId]="'sidebar'">    <!-- 產生：sidebar_user-list_container_content -->
<user-list [prefixKeyForTestId]="'modal'">      <!-- 產生：modal_user-list_container_content -->
<user-list [prefixKeyForTestId]="'main'">       <!-- 產生：main_user-list_container_content -->
<user-list>                                      <!-- 產生：user-list_container_content -->

<!-- 複雜應用場景 -->
<card-tab [prefixKeyForTestId]="'left-panel'">  <!-- left-panel_card-tab_container_content -->
<card-tab [prefixKeyForTestId]="'right-panel'"> <!-- right-panel_card-tab_container_content -->
```

### **純顯示元素範例**
```html
<!-- 純顯示區域 - 統一使用 content -->
<header [attr.data-testid]="testIdPrefix() + CardTabIDs.Header + '_' + ElementType.Content">
    <ng-container *ngTemplateOutlet="headerTemplate"></ng-container>
</header>

<section [attr.data-testid]="testIdPrefix() + CardTabIDs.Body + '_' + ElementType.Content">
    {{ displayText }}
</section>

<!-- PrimeNG 顯示面板 -->
<p-tabpanel [attr.data-testid]="testIdPrefix() + CardTabIDs.TabPanel + '_' + ElementType.Content + '_' + (i + 1)">
    <ng-template [dynamicTab]="tab.renderComponent"></ng-template>
</p-tabpanel>

<!-- Loading 組件 -->
<app-loading 
    [prefixKeyForTestId]="prefixKeyForTestId()"
    [attr.data-testid]="testIdPrefix() + 'loading' + '_' + ElementType.Content">
</app-loading>
```

### **功能性元素範例**
```html
<!-- 互動功能 - 使用特定類型 -->
<button [attr.data-testid]="testIdPrefix() + CardTabIDs.Save + '_' + ElementType.Button">儲存</button>
<input [attr.data-testid]="testIdPrefix() + CardTabIDs.Username + '_' + ElementType.Input">
<p-tab [attr.data-testid]="testIdPrefix() + CardTabIDs.TabItem + '_' + ElementType.Tab + '_' + (i + 1)">
<div [attr.data-testid]="testIdPrefix() + CardTabIDs.ResizeHandle + '_' + ElementType.Handle" (mousedown)="resize()">
```

### **列表項目（含流水號）**
```html
@for (item of items; track item.id; let i = $index) {
    <div [attr.data-testid]="'item_label_' + ElementType.Content + '_' + item.id">
        {{ item.label }}
    </div>
    <iwa-icon [attr.data-testid]="'item_edit_' + ElementType.Icon + '_' + (i + 1)">
    </iwa-icon>
}
```

### **第三方組件**
```html
<!-- PrimeNG 組件統一使用 data-testid -->
<p-tabs [attr.data-testid]="CardTabIDs.TabList + '_' + ElementType.Tab">
<p-tab [attr.data-testid]="CardTabIDs.TabItem + '_' + ElementType.Tab + '_' + (i + 1)">
```

### **通用測試優勢**
5. **E2E 測試**：`cy.get('[data-testid="modal_username_input"]')`
6. **單元測試**：`fixture.debugElement.query(By.css('[data-testid="sidebar_username_input"]'))`
7. **完全避免衝突**：與所有第三方套件無衝突
8. **穩定測試**：第三方組件變化不影響測試
9. **高效能**：直接字串組合避免變更偵測效能問題
10. **簡化維護**：純顯示元素統一使用 `content`，減少命名複雜度
11. **專注功能**：類型分類基於功能而非 HTML 標籤，更清晰的測試目標

## ✅ 檢查清單

### **🔑 唯一識別 Key 檢查（最高優先級）**
- [ ] **組件有 `prefixKeyForTestId` input 屬性**
- [ ] **已依據判斷流程選擇正確的 testIdPrefix 實作方式**
  - [ ] 靜態組件 → 使用 Signal computed
  - [ ] 動態生成 + OnPush → 使用屬性 + ngOnChanges
  - [ ] 動態生成 + 預設策略 → 使用 getter
- [ ] **HTML 範本正確使用 testIdPrefix**（Signal 用 `testIdPrefix()`，其他用 `testIdPrefix`）
- [ ] **可在同一畫面多次使用而不重複 ID**
- [ ] **父層能透過 prefixKeyForTestId 區分不同實例**

### **基本檢查**
- [ ] 所有互動元素都有 `[attr.data-testid]`
- [ ] 純排版 div 沒有多餘的 `data-testid`
- [ ] 使用列舉組合，非硬編碼字串
- [ ] 使用到的 ID 都在列舉中定義

### **格式檢查**
- [ ] TestID 符合 `{prefixKeyForTestId}_{描述}_{類型}_{流水號}` 格式
- [ ] 流水號永遠在最後面
- [ ] 描述使用 `-` 串接多字
- [ ] 統一使用 `[attr.data-testid]` 語法

### **類型選擇檢查**
- [ ] 純顯示元素（header, section, footer, panel 等）使用 `content` 類型
- [ ] 互動元素使用對應功能類型（button, input, tab, handle 等）
- [ ] 避免根據 HTML 標籤命名，專注於功能性分類

### **效能檢查**
- [ ] ⚡ 已依據組件特性選擇最佳 testIdPrefix 實作方式
  - [ ] 靜態組件優先使用 Signal computed
  - [ ] 動態生成組件依 OnPush 策略選擇屬性或 getter
- [ ] TypeScript 中正確定義 testIdPrefix（computed / 屬性 / getter）
- [ ] HTML 範本正確使用 testIdPrefix（避免三元運算子）
- [ ] 避免在 HTML 範本中呼叫函式或複雜邏輯運算

### **第三方組件**
- [ ] PrimeNG 組件使用 `[attr.data-testid]`
- [ ] 避免設置會被覆蓋的 `[id]` 屬性

---

## 🎯 測試好處

### **🔑 唯一識別的關鍵優勢**
1. **避免 ID 衝突**：多實例組件不會產生重複的 `data-testid`
2. **精確測試定位**：可準確測試特定實例，如 `cy.get('[data-testid="sidebar_user-list_container_content"]')`
3. **測試隔離性**：不同實例的測試互不干擾
4. **可維護性**：重構組件位置不影響測試穩定性

### **通用測試優勢**
5. **E2E 測試**：`cy.get('[data-testid="modal_username_input"]')`
6. **單元測試**：`fixture.debugElement.query(By.css('[data-testid="sidebar_username_input"]'))`
7. **完全避免衝突**：與所有第三方套件無衝突
8. **穩定測試**：第三方組件變化不影響測試
9. **高效能**：使用 `testIdPrefix` 計算屬性，避免每次變更偵測都執行三元運算
10. **簡化維護**：純顯示元素統一使用 `content`，減少命名複雜度
11. **專注功能**：類型分類基於功能而非 HTML 標籤，更清晰的測試目標

### **實際測試場景範例**
```typescript
// 可以精確測試不同實例
cy.get('[data-testid="sidebar_user-list_add_button"]').click();
cy.get('[data-testid="modal_user-list_add_button"]').should('be.disabled');
cy.get('[data-testid="main_user-list_container_content"]').should('contain', 'No users');
```