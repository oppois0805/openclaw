# IWA Web Library 元件文件

本文件提供了 IWA Web Library 中可用元件的詳細說明。旨在引導 LLM 和開發人員如何有效地使用這些元件。

## 目錄

- [IWA Web Library 元件文件](#iwa-web-library-元件文件)
  - [目錄](#目錄)
  - [Card Components](#card-components)
    - [iwa-card](#iwa-card)
    - [iwa-card-header](#iwa-card-header)
    - [iwa-card-header-footer](#iwa-card-header-footer)
    - [iwa-card-tab](#iwa-card-tab)
  - [General Components](#general-components)
    - [iwa-dialog-menu](#iwa-dialog-menu)
    - [iwa-icon](#iwa-icon)
    - [iwa-main-menu](#iwa-main-menu)
    - [iwa-navigation-bar](#iwa-navigation-bar)
    - [iwa-stepper](#iwa-stepper)
  - [Layout Components](#layout-components)
    - [iwa-layout](#iwa-layout)
    - [iwa-layout-header](#iwa-layout-header)
    - [iwa-layout-header-menu](#iwa-layout-header-menu)
  - [Mixin Components](#mixin-components)
    - [iwa-list](#iwa-list)
    - [iwa-list-items](#iwa-list-items)
  - [資料模型與介面 (Data Models \& Interfaces)](#資料模型與介面-data-models--interfaces)
    - [共用介面 (Shared Interfaces)](#共用介面-shared-interfaces)
    - [Card Tab Interfaces](#card-tab-interfaces)
    - [Dialog Menu Interfaces](#dialog-menu-interfaces)
    - [Icon Interfaces](#icon-interfaces)
    - [Main Menu Interfaces](#main-menu-interfaces)
    - [Navigation Bar Interfaces](#navigation-bar-interfaces)
    - [Stepper Interfaces](#stepper-interfaces)
    - [List Interfaces](#list-interfaces)
    - [List Items Interfaces](#list-items-interfaces)

---

## Card Components

### iwa-card

**Selector:** `iwa-card` **描述:** 基本卡片元件。

**輸入屬性 (Inputs):**

- `backgroundColor` (string): 卡片背景顏色。預設值: `'#fff'`。
- `prefixKeyForTestId` (string): 測試 ID 的前綴字串。

**內容子元件 (Content Children):**

- `content`: 卡片內容的範本 (Template)。

**使用範例:**

```html
<iwa-card [backgroundColor]="'#f5f5f5'">
    <ng-template #content>
        <!-- 內容放在這裡 -->
    </ng-template>
</iwa-card>
```

### iwa-card-header

**Selector:** `iwa-card-header` **描述:** 帶有標題區塊的卡片元件。

**輸入屬性 (Inputs):**

- `backgroundColor` (string): 卡片背景顏色。預設值: `'#fff'`。
- `prefixKeyForTestId` (string): 測試 ID 的前綴字串。

**內容子元件 (Content Children):**

- `header`: 標題內容的範本。
- `content`: 主要內容的範本。

**使用範例:**

```html
<iwa-card-header>
    <ng-template #header>
        <!-- 標題內容 -->
    </ng-template>
    <ng-template #content>
        <!-- 主要內容 -->
    </ng-template>
</iwa-card-header>
```

### iwa-card-header-footer

**Selector:** `iwa-card-header-footer` **描述:** 包含標題、內容和頁尾區塊的卡片元件。

**輸入屬性 (Inputs):**

- `backgroundColor` (string): 卡片背景顏色。預設值: `'#fff'`。
- `prefixKeyForTestId` (string): 測試 ID 的前綴字串。

**內容子元件 (Content Children):**

- `header`: 標題內容的範本。
- `content`: 主要內容的範本。
- `footer`: 頁尾內容的範本。

**使用範例:**

```html
<iwa-card-header-footer>
    <ng-template #header>標題</ng-template>
    <ng-template #content>內容</ng-template>
    <ng-template #footer>頁尾</ng-template>
</iwa-card-header-footer>
```

### iwa-card-tab

**Selector:** `iwa-card-tab` **描述:** 彈性卡片元件，包含主要內容區域和可切換的標籤詳細資訊區段。支援自動高度和可調整大小的詳細資訊區段。

**輸入屬性 (Inputs):**

- `settings` ([TabSettingsConfig](#card-tab-interfaces)): 標籤設定資料。預設值: `{ data: [] }`。
- `tabEllipsis` (boolean): 是否啟用標籤文字省略顯示。預設值: `false`。
- `tabWidth` (string): 標籤寬度。
- `tabMaxWidth` (string): 標籤最大寬度。
- `arrowTooltip` (string): 箭頭的工具提示文字。
- `height` (string | null): 容器的明確高度。若為 null，則根據視窗自動計算。預設值: `null`。
- `rowData` (any): 傳遞給動態元件的資料。預設值: `null`。
- `prefixKeyForTestId` (string): 測試 ID 的前綴字串。

**輸出事件 (Outputs):**

- `tabChangeEvent` (EventEmitter<TabChangeEvent>): 當標籤切換時觸發。
- `tabOpenEvent` (EventEmitter<TabOpenEvent>): 當標籤區域從收合狀態展開時觸發（僅首次開啟時觸發）。

**內容子元件 (Content Children):**

- `header`: 標題的範本。
- `contentBody`: 主要內容主體的範本。

**使用範例:**

```html
<iwa-card-tab [settings]="tabSettings" [height]="'600px'" (tabChangeEvent)="onTabChange($event)">
    <ng-template #header>標題內容</ng-template>
    <ng-template #contentBody>主要內容</ng-template>
</iwa-card-tab>
```

---

## General Components

### iwa-dialog-menu

**Selector:** `iwa-dialog-menu` **描述:** 對話框選單元件。

**輸入屬性 (Inputs):**

- `backgroundColor` (string): 背景顏色。預設值: `'#fff'`。
- `dialogMenuData` ([DialogMenuInterface](#dialog-menu-interfaces)): 對話框選單的設定資料。
- `display` (boolean): 是否顯示對話框。預設值: `false`。
- `prefixKeyForTestId` (string): 測試 ID 的前綴字串。

**內容子元件 (Content Children):**

- `aside`: 側邊欄區塊的範本。
- `content`: 內容區塊的範本。
- `footer`: 頁尾區塊的範本。

**使用範例:**

```html
<iwa-dialog-menu [display]="isVisible" [dialogMenuData]="menuData">
    <ng-template #aside>側邊欄</ng-template>
    <ng-template #content>內容</ng-template>
    <ng-template #footer>頁尾</ng-template>
</iwa-dialog-menu>
```

### iwa-icon

**Selector:** `iwa-icon` **描述:** 支援子圖示、徽章 (badge) 和工具提示 (tooltip) 的圖示元件。

**輸入屬性 (Inputs):**

- `key` (string): **必填**。圖示的鍵值 (Key)。
- `content` (string): **必填**。圖示內容 (例如 SVG path 或字元)。
- `defaultColor` (string): 預設圖示顏色。預設值: `'#000000'`。
- `hasHover` (boolean): 是否啟用懸停效果。預設值: `false`。
- `hoverColor` (string): 懸停時的顏色。預設值: `'#0067ff'`。
- `isDisabled` (boolean): 圖示是否為禁用狀態。預設值: `false`。
- `subIcons` ([SubIcon[]](#icon-interfaces)): 子圖示陣列。預設值: `[]`。
- `subBadge` ([SubBadge[]](#icon-interfaces)): 子徽章陣列。預設值: `[]`。
- `title` (string): 圖示標題。預設值: `''`。
- `tooltip` (string): 工具提示文字。預設值: `''`。
- `tooltipPosition` (string): 工具提示位置。預設值: `TooltipPosition.Bottom`。
- `prefixKeyForTestId` (string): 測試 ID 的前綴字串。

**使用範例:**

```html
<iwa-icon [key]="'home'" [content]="'home_icon_svg'" [hasHover]="true"></iwa-icon>
```

### iwa-main-menu

**Selector:** `iwa-main-menu` **描述:** 具備可折疊項目的主選單元件。

**輸入屬性 (Inputs):**

- `backgroundColor` (string): 背景顏色。預設值: `'#fff'`。
- `titleBackgroundColor` (string): 標題背景顏色。預設值: `'#fff'`。
- `menuData` ([MenuDataInterface](#main-menu-interfaces)): 選單資料結構。預設值: `{ data: [] }`。
- `menuItem` (MenuDataItem | null): 預設選中的項目。預設值: `null`。
- `isCollapse` (boolean): 選單是否折疊 (僅顯示圖示)。預設值: `false`。
- `prefixKeyForTestId` (string): 測試 ID 的前綴字串。
- `collapseAll` (any): 設定此屬性以折疊所有項目。

**輸出事件 (Outputs):**

- `selectedItem` (EventEmitter<string>): 當項目被選取時觸發。

**內容子元件 (Content Children):**

- `title`: 選單標題的範本。

**使用範例:**

```html
<iwa-main-menu [menuData]="menuData" (selectedItem)="onSelect($event)">
    <ng-template #title>我的應用程式</ng-template>
</iwa-main-menu>
```

### iwa-navigation-bar

**Selector:** `iwa-navigation-bar` **描述:** 導覽列元件。

**輸入屬性 (Inputs):**

- `backgroundColor` (string): 背景顏色。預設值: `'#ffffff'`。
- `siteName` (string): 網站名稱。
- `version` (string): 版本字串。
- `menuData` (MenuDataInterface): 選單資料。預設值: `{ data: [] }`。
- `prefixKeyForTestId` (string): 測試 ID 的前綴字串。

**輸出事件 (Outputs):**

- `toggleMenu` (EventEmitter<boolean>): 當點擊選單切換按鈕時觸發。
- `selectedItem` (EventEmitter<string>): 當項目被選取時觸發。

**內容子元件 (Content Children):**

- `center`: 中間內容的範本。

**使用範例:**

```html
<iwa-navigation-bar [siteName]="'我的網站'" [menuData]="navData"></iwa-navigation-bar>
```

### iwa-stepper

**Selector:** `iwa-stepper` **描述:** 步驟導引元件，用於引導使用者完成一系列的步驟流程。支援水平、水平靠右和垂直三種排列方向。

**輸入屬性 (Inputs):**

- `steps` (string[]): 步驟文字陣列。預設值: `[]`。
- `currentStep` (number): 目前的步驟索引（1-based）。預設值: `1`。
- `status` ([StepperStatusEnum](#stepper-interfaces)): Stepper 狀態，控制步驟完成邏輯。預設值: `StepperStatusEnum.Create`。
- `orientation` ([StepperOrientationEnum](#stepper-interfaces)): 排列方向。預設值: `StepperOrientationEnum.Horizontal`。
- `width` (string): Stepper 寬度。預設值: `'auto'`。
- `height` (string): Stepper 高度。預設值: `'auto'`。
- `prefixKeyForTestId` (string): 測試 ID 的前綴字串。

**輸出事件 (Outputs):**

- `stepChanges` (EventEmitter<number>): 當步驟切換時觸發，發送目標步驟索引（1-based）。

**使用範例:**

```html
<iwa-stepper
    [steps]="['步驟一', '步驟二', '步驟三']"
    [currentStep]="1"
    [status]="'create'"
    [orientation]="'horizontal'"
    (stepChanges)="onStepChange($event)">
</iwa-stepper>
```

---

## Layout Components

### iwa-layout

**Selector:** `iwa-layout` **描述:** 基本佈局元件。

**輸入屬性 (Inputs):**

- `height` (string): 佈局高度。預設值: `'100vh'`。
- `borderRadius` (string): 邊框圓角。預設值: `'0rem'`。
- `backgroundColor` (string): 背景顏色。預設值: `'none'`。
- `prefixKeyForTestId` (string): 測試 ID 的前綴字串。

**內容子元件 (Content Children):**

- `layout`: 佈局內容的範本。

**使用範例:**

```html
<iwa-layout>
    <ng-template #layout>
        <!-- 佈局內容 -->
    </ng-template>
</iwa-layout>
```

### iwa-layout-header

**Selector:** `iwa-layout-header` **描述:** 包含標題（頂部）和內容（底部）區塊的佈局元件。

**輸入屬性 (Inputs):**

- `height` (string): 佈局高度。預設值: `'100vh'`。
- `bottomBorderRadius` (string): 底部區塊的邊框圓角。預設值: `'0rem'`。
- `topBackgroundColor` (string): 頂部區塊的背景顏色。預設值: `'none'`。
- `bottomBackgroundColor` (string): 底部區塊的背景顏色。預設值: `'none'`。
- `prefixKeyForTestId` (string): 測試 ID 的前綴字串。

**內容子元件 (Content Children):**

- `top`: 頂部區塊的範本。
- `bottom`: 底部區塊的範本。

**使用範例:**

```html
<iwa-layout-header>
    <ng-template #top>標題</ng-template>
    <ng-template #bottom>內容</ng-template>
</iwa-layout-header>
```

### iwa-layout-header-menu

**Selector:** `iwa-layout-header-menu` **描述:** 包含標題、左側選單和右側內容區域的佈局元件。

**輸入屬性 (Inputs):**

- `height` (string): 佈局高度。預設值: `'100vh'`。
- `rightBorderRadius` (string): 右側區塊的邊框圓角。預設值: `'0rem'`。
- `topBackgroundColor` (string): 頂部區塊的背景顏色。預設值: `'none'`。
- `leftBackgroundColor` (string): 左側區塊的背景顏色。預設值: `'none'`。
- `rightBackgroundColor` (string): 右側區塊的背景顏色。預設值: `'none'`。
- `leftIsCollapse` (boolean | null): 左側選單是否折疊。預設值: `null`。
- `prefixKeyForTestId` (string): 測試 ID 的前綴字串。

**內容子元件 (Content Children):**

- `top`: 頂部區塊的範本。
- `left`: 左側區塊的範本。
- `right`: 右側區塊的範本。

**使用範例:**

```html
<iwa-layout-header-menu>
    <ng-template #top>標題</ng-template>
    <ng-template #left>選單</ng-template>
    <ng-template #right>內容</ng-template>
</iwa-layout-header-menu>
```

---

## Mixin Components

### iwa-list

**Selector:** `iwa-list` **描述:** 進階列表/表格元件，具備排序、篩選、虛擬捲動等功能。

**輸入屬性 (Inputs):**

- `loading` (boolean): 是否顯示載入中的骨架屏 (skeleton)。預設值: `true`。
- `stripedRows` (boolean): 是否顯示斑馬紋行。預設值: `true`。
- `rowHover` (boolean): 滑鼠懸停時是否高亮顯示行。預設值: `true`。
- `scrollHeight` (string): 表格容器的捲動高度。預設值: `'400px'`。
- `loadedRows` (number): 延遲載入 (lazy loading) 時每批載入的行數。預設值: `50`。
- `isVirtualScroll` (boolean): 是否啟用虛擬捲動。預設值: `false`。
- `lazy` (boolean): 是否啟用懶加載模式。預設值: `false`。
- `virtualScrollItemSize` (number): 虛擬捲動時每個項目的高度（像素）。預設值: `37.2`。
- `isDataUpdated` (boolean): 資料是否已更新 (顯示重新整理提示)。預設值: `false`。
- `scrollToIndex` (number): 載入時捲動到的索引位置。預設值: `-1`。
- `selectedIndex` (number): 初始選中的行索引。預設值: `-1`。
- `scrollToSelected` (boolean): 是否滾動到已選取的行。預設值: `false`。
- `scrollToTopTrigger` (number): 滾動觸發器。當此值改變時，會滾動到已選取的行。建議使用遞增方式觸發。預設值: `0`。
- `selectEmptyMessage` (string): 篩選結果為空時顯示的訊息。預設值: `''`。
- `emptyMessage` (string): 表格為空時顯示的訊息。預設值: `''`。
- `prefixKeyForTestId` (string): 測試 ID 的前綴字串。
- `tableHeader` ([ListHeaderInterface](#list-interfaces)): 表格標題的設定。
- `tableBody` ([ListBodyInterface](#list-interfaces)): 表格內容的資料。
- `rawTableBody` ([ListBodyInterface](#list-interfaces)): 表格內容的原始資料。

**輸出事件 (Outputs):**

- `sendCellChange` (EventEmitter<CellChangeEvent>): 當儲存格數值改變時觸發。
- `sendClickIcon` (EventEmitter<ClickIconEvent>): 當圖示被點擊時觸發。
- `sendFilteredColumns` (EventEmitter<FilteredColumn>): 當欄位被篩選時觸發。
- `sendLazyLoad` (EventEmitter<TableLazyLoadEvent>): 觸發延遲載入時發送。
- `sendRefreshTable` (EventEmitter<RefreshTableEvent>): 請求重新整理表格時觸發。
- `sendRowSelection` (EventEmitter<RowSelectEvent>): 當行被選取時觸發。
- `sendSortColumn` (EventEmitter<SortEvent>): 當欄位排序時觸發。

**使用範例:**

```html
<iwa-list [tableHeader]="headers" [tableBody]="data" [loading]="isLoading" (sendRowSelection)="onRowSelect($event)">
</iwa-list>
```

### iwa-list-items

**Selector:** `iwa-list-items` **描述:** 用於顯示項目列表的元件。

**輸入屬性 (Inputs):**

- `backgroundColor` (string): 背景顏色。預設值: `'#fff'`。
- `width` (string): 元件寬度。預設值: `'100%'`。
- `height` (string): 元件高度。預設值: `'200px'`。
- `prefixKeyForTestId` (string): 測試 ID 的前綴字串。
- `listItemsData` (ListItemsInterface): 列表項目的資料。

**輸出事件 (Outputs):**

- `sendSelectedIcon` (EventEmitter<IconEvent>): 當圖示被選取時觸發。
- `sendSelectedRow` (EventEmitter<ListItemsItem>): 當行被選取時觸發。

**使用範例:**

```html
<iwa-list-items [listItemsData]="items" (sendSelectedRow)="onSelect($event)"> </iwa-list-items>
```

---

## 資料模型與介面 (Data Models & Interfaces)

以下列出各元件使用的詳細介面定義，供開發參考。

### 共用介面 (Shared Interfaces)

```typescript
export interface DataInterface<T> {
    id?: number | string;
    data: T;
}

export interface TableLazyLoadEvent {
    first?: number;
    last?: number;
    rows?: number;
    sortField?: string;
    sortOrder?: number;
    filters?: { [s: string]: any };
    globalFilter?: any;
    multiSortMeta?: any[];
    [key: string]: any;
}

// PrimeNG MenuItem (參考簡化版)
export interface MenuItem {
    label?: string;
    icon?: string;
    command?: (event?: any) => void;
    url?: string;
    routerLink?: any;
    items?: MenuItem[];
    expanded?: boolean;
    disabled?: boolean;
    visible?: boolean;
    target?: string;
    id?: string;
    [key: string]: any;
}
```

### Card Tab Interfaces

```typescript
export interface TabSettingsConfig {
    /** 標籤頁設定陣列 */
    data: TabSetting[];
}

export interface TabSetting {
    /** 標籤頁唯一識別碼 */
    id: number;
    /** 標籤頁顯示標題 */
    title: string;
    /** 是否禁用此標籤頁 */
    disabled: boolean;
    /** 關聯的表格行 ID */
    tableRowId: number;
    /** 動態載入的組件類型 */
    renderComponent: Type<any>;
    /** 組件初始化時的回調函數 - 可以訪問 context 來取得當前資料 */
    onComponentInitFunction?: (instance: any, context?: TabInitContext) => void;
}

export interface TabInitContext {
    /** 當前傳入的資料 */
    rowData: any;
    /** 標籤設定本身 */
    tabSetting: TabSetting;
    /** 標籤索引 */
    tabIndex: number;
    /** 當前選中的標籤索引 */
    activeTabIndex: number;
}

export interface TabChangeEvent {
    /** 標籤索引 */
    index: number;
    /** 標籤 ID */
    id: number;
}

export interface TabOpenEvent {
    /** 當前標籤索引 */
    index: number;
    /** 當前標籤 ID */
    id: number;
}
```

### Dialog Menu Interfaces

```typescript
export interface DialogMenuInterface extends DataInterface<DialogMenuItem> {}

export interface DialogMenuItem {
    header?: string;
    width?: string;
    height?: string;
}
```

### Icon Interfaces

```typescript
export interface IconInterface extends DataInterface<IconItem> {}

export interface IconItem {
    id?: string | number;
    key: string;
    content: string;
    defaultColor?: string;
    hasHover?: boolean;
    hoverColor?: string;
    isDisabled?: boolean;
    subIcons?: SubIcon[];
    subBadge?: SubBadge[];
    title?: string;
    tooltip?: string;
    tooltipPosition?: string;
}

export interface SubIcon {
    position: string;
    className: string | null;
}

export interface SubBadge {
    position: string;
    value: number | string | null;
}

export enum Position {
    TopLeft = 'top-left',
    TopRight = 'top-right',
    BottomLeft = 'bottom-left',
    BottomRight = 'bottom-right'
}

export enum TooltipPosition {
    Top = 'top',
    Bottom = 'bottom',
    Left = 'left',
    Right = 'right'
}
```

### Main Menu Interfaces

```typescript
export interface MenuDataInterface extends DataInterface<MenuDataItem[]> {}

export interface MenuDataItem extends MenuItem {
    permissionTypeCode?: string;
    elementId?: string;
    value?: string;
    active?: boolean;
}
```

### Navigation Bar Interfaces

```typescript
export interface MenuDataInterface extends DataInterface<MenuDataItem[]> {
    // MenuDataItem 文字排列 (左中右)
    textAlign?: TextAlign;
}

export interface MenuDataItem extends MenuItem {
    permissionTypeCode?: string;
    isImage?: boolean;
    imageUrl?: string;
    isParent?: boolean;
    isTitle?: boolean;
    active?: boolean;
    elementId?: string;
}

export enum TextAlign {
    Left = 'left',
    Center = 'center',
    Right = 'right'
}
```

### Stepper Interfaces

```typescript
export enum StepperStatusEnum {
    /** 建立模式：只有 currentStep 之前的步驟會標記為已完成 */
    Create = 'create',
    /** 編輯模式：除了 currentStep 以外的所有步驟都會標記為已完成 */
    Edit = 'edit'
}

export enum StepperOrientationEnum {
    /** 水平排列（連接線在步驟之間） */
    Horizontal = 'horizontal',
    /** 水平靠右排列（連接線在步驟下方） */
    HorizontalRight = 'horizontal-right',
    /** 垂直排列 */
    Vertical = 'vertical'
}
```

### List Interfaces

```typescript
export interface ListHeaderInterface extends DataInterface<ListHeader[]> {}
export interface ListBodyInterface extends DataInterface<ListBody[]> {}

export interface ListHeader {
    id?: string | number;
    field: string;
    header?: string;
    width: string;
    minWidth?: string;
    dynamicWidth?: string;
    rate?: number;
    isSortable?: boolean;
    isEditable?: boolean;
    required?: boolean;
    type?: FilterTypeEnum;
    columnType?: ColumnTypeEnum;
    class?: string;
    options?: SelectOptions[];
    selectedOption?: SelectedOption | null;
    filterPlaceholder?: string;
    searchValue?: string;
    placeholder?: string | null;
    icons?: IconInterface[];
    component?: ComponentType;
    tooltipPosition?: TooltipPosition;
}

export interface ListBody {
    id: number | string;
    [key: string]: ColumnType | number | string;
}

export type ColumnType =
    | CheckboxType
    | ComponentType
    | SelectType
    | IconType
    | ToggleSwitchType
    | InputType
    | ProgressBarType
    | StatusLabelType
    | StatusType
    | TextType;

export interface CheckboxType {
    type: ColumnTypeEnum.Checkbox;
    checked: boolean | null;
    disabled?: boolean;
    invalid?: boolean;
    class?: string;
}

export interface ComponentType {
    id: number;
    inputs?: Record<string, any>;
    renderComponent: Type<any>;
    onComponentInitFunction?: (instance: any) => void;
}

export interface SelectType {
    type: ColumnTypeEnum.Select;
    content: string | null;
    class?: string;
    invalid?: boolean;
    disabled?: boolean;
    options: string[];
    placeholder?: string;
    filterPlaceholder?: string;
    tooltip?: string;
    tooltipPosition?: string;
}

export interface IconType {
    type: ColumnTypeEnum.Icon;
    icons: IconInterface[];
}

export interface ToggleSwitchType {
    type: ColumnTypeEnum.ToggleSwitch;
    content: boolean | null;
    disabled?: boolean;
    enableTitle?: string;
    disableTitle?: string;
}

export interface InputType {
    type: ColumnTypeEnum.Input;
    content: string | null;
    class?: string;
    disabled?: boolean;
    invalid?: boolean;
    placeholder?: string;
    tooltip?: string;
    tooltipPosition?: string;
}

export interface ProgressBarType {
    type: ColumnTypeEnum.ProgressBar;
    content: number | null;
    colorCode?: string;
}

export interface StatusType {
    type: ColumnTypeEnum.Status;
    colorCode: string;
}

export interface StatusLabelType {
    type: ColumnTypeEnum.StatusLabel;
    content: string;
    colorCode: string;
}

export interface TextType {
    type: ColumnTypeEnum.Text;
    content: string | number;
    required?: boolean;
    isEditable?: boolean;
    isInvalid?: boolean;
    icons?: IconInterface[];
    tooltip?: string;
    tooltipPosition?: string;
}

export interface SelectOptions {
    name: string;
    code: string;
}

export interface SelectedOption {
    name: string;
    code: string | number;
}

export interface CellChangeEvent {
    event?: Event | CheckboxChangeEvent | ToggleSwitchChangeEvent | SelectChangeEvent;
    value?: string;
    rowData: ListBody;
    field: string;
}

export interface RowSelectEvent {
    rowSelectEvent: TableRowSelectEvent;
    isSelected: boolean;
}

export interface SortEvent {
    sortField: string;
    sortOrder: number;
}

export interface ClickIconEvent {
    field: string;
    key: string;
    rowData?: ListBody;
}

export interface RefreshTableEvent {
    isRefresh: boolean;
}

export interface FilteredColumn {
    [key: string]: {
        type: string;
        searchValue?: string;
        selectedOption?: SelectedOption | null;
    };
}

export enum SortEnum {
    None = 0,
    Ascending = 1,
    Descending = -1
}

export enum FilterTypeEnum {
    Select = 'select',
    Search = 'search',
    Checkbox = 'checkbox'
}

export enum ColumnTypeEnum {
    Select = 'select',
    Search = 'search',
    Checkbox = 'checkbox',
    Component = 'component',
    Icon = 'icon',
    ToggleSwitch = 'toggleswitch',
    Input = 'input',
    ProgressBar = 'progressBar',
    StatusLabel = 'statusLabel',
    Status = 'status',
    Text = 'text'
}

// PrimeNG Events (Simplified)
export interface TableRowSelectEvent {
    originalEvent: Event;
    data: any;
    type: string;
    index: number;
}

export interface CheckboxChangeEvent {
    checked: boolean;
    originalEvent: Event;
}

export interface ToggleSwitchChangeEvent {
    originalEvent: Event;
    checked: boolean;
}

export interface SelectChangeEvent {
    originalEvent: Event;
    value: any;
}
```

### List Items Interfaces

```typescript
export interface ListItemsInterface extends DataInterface<ListItemsItem[]> {}

export interface ListItemsItem {
    id: number;
    label: string;
    isDisabled?: boolean;
    isSelected?: boolean;
    count?: number | null;
    icons?: IconInterface[];
}

export interface IconEvent {
    id: number;
    key: string;
}
```