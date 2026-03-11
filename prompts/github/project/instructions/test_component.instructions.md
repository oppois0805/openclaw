# 🎯 Angular Jasmine 測試生成精華指南

> 本指南幫助 AI 快速生成完整、正確的 Angular Component 單元測試

---

## 📑 快速導覽

- [✨ 核心原則](#-核心原則) — 必讀的 4 大原則
- [📋 必測項目](#-必測項目) — 6 類必須測試的項目
- [🔧 關鍵模式](#-關鍵模式) — 5 種常用測試模式
- [💡 完整範例](#-完整範例) — UserCardComponent 完整示例
- [✅ 檢查清單](#-快速檢查清單) — 強制檢查 & 基本檢查

---

## ✨ 核心原則

| 原則 | 說明 |
|------|------|
| ✅ **測試使用者行為** | 專注於 Input、Output、DOM 變化、使用者互動 |
| ❌ **不測試內部** | 避免 private 方法、內部邏輯、CSS 細節 |
| 🌐 **全中文描述** | describe/it 中文命名，使用 `data-testid` 定位 |
| ⚠️ **完整服務分析** | 必須分析所有注入的 Services/Facades/Providers |

### ⚠️ 服務分析必做項

```
1️⃣  檢查 Component 的 constructor 參數 → 每個都需要 Mock
2️⃣  檢查 Component 的 providers 陣列 → 都要包含在 TestBed
3️⃣  為所有服務建立對應的 Mock → 名稱方法需相符
4️⃣  確保 TestBed.providers 完全對應 → 數量不能少
```

---

## 📋 必測項目

> 開始寫測試前，必須先完成：**分析 Component 的所有依賴服務並建立 Mock**

| # | 項目 | 測試內容 | 驗證方式 |
|---|------|---------|---------|
| 1️⃣ | **🏗️ 元件建立** | 元件能否正常建立 | `expect(component).toBeTruthy()` |
| 2️⃣ | **📌 服務注入** | 所有服務是否正確注入 | `expect(component.service).toBe(serviceSpy)` |
| 3️⃣ | **📥 @Input** | 輸入改變是否反映到 DOM | 設定值 → `detectChanges()` → 驗證元素 |
| 4️⃣ | **📤 @Output** | 事件是否正確觸發 | 觸發事件 → 驗證 `toHaveBeenCalledWith()` |
| 5️⃣ | **👁️ 條件渲染** | `*ngIf/*ngFor` 是否正確顯示 | 改變條件 → 驗證元素存在性 |
| 6️⃣ | **👆 使用者互動** | 點擊/輸入是否產生預期結果 | 模擬事件 → 驗證結果 |

---

## 🔧 關鍵模式

> 5 種最常用的測試模式。複製粘貼即可使用

### 📌 模式 1️⃣：基本結構（含服務 Mock）— 最重要！

⚠️ **這是所有測試的基礎。必須確保所有服務都被 Mock**

**第 1 步：分析 Component 的依賴**
```typescript
// 範例 Component 的 constructor:
const exampleConstructor = `
constructor(
  private authService: AuthService,
  private router: Router,
  public facade: LoginFacade
) {}
`
```

**第 2-4 步：撰寫 beforeEach**
```typescript

### 2. 測試 @Input
```typescript
it('當 message 改變時應更新顯示文字', () => {
  component.message = '測試';
  fixture.detectChanges();
  
  const el = fixture.debugElement.query(By.css('[data-testid="msg"]'));
  expect(el.nativeElement.textContent).toContain('測試');
});
```

### 3. 測試 @Output
```typescript
it('點擊按鈕應發出 close 事件', () => {
  spyOn(component.close, 'emit');
  
  fixture.debugElement.query(By.css('[data-testid="btn"]'))
    .nativeElement.click();
  
  expect(component.close.emit).toHaveBeenCalled();
});
```

### 4. 測試條件渲染
```typescript
it('當 visible 為 false 時不應顯示元素', () => {
  component.visible = false;
  fixture.detectChanges();
  
  const el = fixture.debugElement.query(By.css('[data-testid="box"]'));
  expect(el).toBeNull();
});
```

### 5. 測試列表渲染
```typescript
it('應根據 items 數量渲染正確數量的項目', () => {
  component.items = [{ id: 1 }, { id: 2 }, { id: 3 }];
  fixture.detectChanges();
  
  const items = fixture.debugElement.queryAll(
    By.css('[data-testid^="list-item"]')
  );
  expect(items.length).toBe(3);
});
```

---

## 📝 標準輸出結構

```typescript
describe('🧩 [ComponentName] 元件', () => {
  // 基本設定 (beforeEach)
  
  it('🏗️ 應該成功建立元件', () => { ... });
  
  describe('📥 @Input 測試', () => {
    it('當 [input] 改變時應 [行為]', () => { ... });
  });
  
  describe('📤 @Output 測試', () => {
    it('當 [觸發] 時應發出 [event]', () => { ... });
  });
  
  describe('👆 使用者互動測試', () => {
    it('點擊 [元素] 應 [結果]', () => { ... });
  });
  
  describe('👁️ 條件渲染測試', () => {
    it('當 [條件] 時應 [顯示/隱藏]', () => { ... });
  });
});
```

---

## 🚫 不要測試的項目

- ❌ Private 方法
- ❌ CSS 樣式細節（如顏色、邊距）
- ❌ 動畫時間
- ❌ 第三方套件內部邏輯
- ❌ 複雜的內部狀態轉換（除非影響 UI）

---

## ✅ 快速檢查清單

### 🔴 強制檢查（必須完成）
- [ ] **已完整分析 Component 的 constructor 參數**
- [ ] **已完整分析 Component 的 providers 陣列**
- [ ] **為所有服務建立對應的 Mock Spy**
- [ ] **TestBed.providers 包含所有服務 Mock**
- [ ] **已驗證所有服務注入正確**

### 🟢 基本檢查
- [ ] 全中文描述
- [ ] 使用 `data-testid`
- [ ] 覆蓋所有 public `@Input/@Output`
- [ ] 測試關鍵條件渲染
- [ ] 測試使用者主要互動
- [ ] 每個 `it()` 只測一件事
- [ ] 測試碼簡潔（單一測試 ≤ 10 行）

---

## 💡 完整範例

### 輸入 Component

```typescript
@Component({
  selector: 'app-user-card',
  standalone: true,
  template: `
    <div [attr.data-testid]="'user-card_container_content'" *ngIf="visible">
      <h2 [attr.data-testid]="'user-card_name_content'">{{ userName }}</h2>
      <button 
        [attr.data-testid]="'user-card_delete_button'"
        [disabled]="disabled"
        (click)="onDelete.emit(userId)">
        刪除
      </button>
    </div>
  `
})
export class UserCardComponent {
  @Input() userName = '';
  @Input() userId = 0;
  @Input() visible = true;
  @Input() disabled = false;
  @Output() onDelete = new EventEmitter<number>();
}
```

### 輸出測試檔案

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { By } from '@angular/platform-browser';
import { UserCardComponent } from './user-card.component';

describe('🧩 UserCardComponent 元件', () => {
  let component: UserCardComponent;
  let fixture: ComponentFixture<UserCardComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [UserCardComponent]
    }).compileComponents();

    fixture = TestBed.createComponent(UserCardComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('🏗️ 應該成功建立元件', () => {
    expect(component).toBeTruthy();
  });

  describe('📥 @Input 測試', () => {
    it('當 userName 改變時應更新顯示的使用者名稱', () => {
      component.userName = '張三';
      fixture.detectChanges();

      const nameElement = fixture.debugElement.query(
        By.css('[data-testid="user-card_name_content"]')
      );
      expect(nameElement.nativeElement.textContent).toBe('張三');
    });

    it('當 visible 為 false 時不應顯示卡片', () => {
      component.visible = false;
      fixture.detectChanges();

      const card = fixture.debugElement.query(
        By.css('[data-testid="user-card_container_content"]')
      );
      expect(card).toBeNull();
    });

    it('當 disabled 為 true 時刪除按鈕應被停用', () => {
      component.disabled = true;
      fixture.detectChanges();

      const button = fixture.debugElement.query(
        By.css('[data-testid="user-card_delete_button"]')
      );
      expect(button.nativeElement.disabled).toBeTrue();
    });
  });

  describe('📤 @Output 測試', () => {
    it('點擊刪除按鈕應發出 onDelete 事件並傳遞 userId', () => {
      spyOn(component.onDelete, 'emit');
      component.userId = 123;
      fixture.detectChanges();

      const button = fixture.debugElement.query(
        By.css('[data-testid="user-card_delete_button"]')
      );
      button.nativeElement.click();

      expect(component.onDelete.emit).toHaveBeenCalledWith(123);
    });
  });
});
```

---

**準備好提供你的 Component，我將立即生成完整測試！** 🚀