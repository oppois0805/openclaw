🎯 Angular Storybook 測試開發完整指南

## 📋 組件說明文件生成

### **Storybook 說明結構**

```typescript
parameters: {
    docs: {
        description: {
            component: `

## 組件概述
{簡潔描述組件用途與核心功能}

### Import
\`\`\`typescript
import { {ComponentName} } from '@iwa/{package-name}';
\`\`\`

## 核心介面
- 主要輸入屬性中需要的各種介面
{使用 code 格式方便開發人員 copy}

- 主要輸入屬性
  - {屬性名稱}: {屬性介面} - {屬性說明}
  
- 模板參數
  - {參數名稱}: {參數說明}

- 輸出事件
  - {屬性名稱}: {屬性介面} - {屬性說明}

## 列舉定義
{主要輸入屬性需要的 Enum 定義與說明}

## 基本用法
{使用 code 格式方便開發人員 copy}
            `
        }
    }
}
```

### **專注要點**

* ✅ 組件用途一句話說明
* ✅ 外部傳入介面完整列出
* ✅ 所有對外 Enum 定義
* ✅ 實際可執行範例
* ✅ 測試範例保持簡潔乾淨

---

## 📌 AI-Generated Test Labeling

### **要求**

* 所有由 **AI 自動產生的 Story 或測試案例**，必須在 **Story 標題 (name 屬性)** 中清楚標示 `AI - ` 前綴，以利辨識來源。

### **範例**

```typescript
export const AiRenderTest: Story = {
    name: 'AI - Render Test',
    args: { ... },
    render: args => ({ ... }),
    play: async ({ canvasElement, step }) => { ... }
};

export const AiInteractiveTest: Story = {
    name: 'AI - Interactive Test',
    args: { ... },
    render: args => ({ ... }),
    play: async ({ canvasElement, step }) => { ... }
};

export const AiNoDataTest: Story = {
    name: 'AI - No Data Test',
    args: { ... },
    render: args => ({ ... }),
    play: async ({ canvasElement, step }) => { ... }
};

export const AiEdgeCaseTest: Story = {
    name: 'AI - Edge Case Test',
    args: { ... },
    render: args => ({ ... }),
    play: async ({ canvasElement, step }) => { ... }
};
```

### **品質保護理由**

1. **來源清楚**：避免誤認為人工編寫測試。
2. **回溯容易**：出現問題時，可快速確認是否 AI 生成。
3. **團隊透明**：讓所有開發者清楚測試來源。

---

## 🎨 Storybook Stories 開發順序

### **第一階段：Render Test - 渲染測試與完整展示**

```typescript
export const AiRenderTest: Story = {
    name: 'AI - Render Test',
    args: { ...DefaultArgs },
    render: args => ({ props: args, template: `<{component-selector}></{component-selector}>` }),
    play: async ({ canvasElement, step }) => { ... }
};
```

### **第二階段：Interactive Test - 專門互動測試**

```typescript
export const AiInteractiveTest: Story = {
    name: 'AI - Interactive Test',
    args: { ...TestArgs },
    render: args => ({ props: args, template: `<{component-selector}></{component-selector}>` }),
    play: async ({ canvasElement, step }) => { ... }
};
```

### **第三階段：No Data Test - 無資料狀態測試**

```typescript
export const AiNoDataTest: Story = {
    name: 'AI - No Data Test',
    args: { ...NoDataArgs },
    render: args => ({ props: args, template: `<{component-selector}></{component-selector}>` }),
    play: async ({ canvasElement, step }) => { ... }
};
```

### **第四階段：Edge Case Test - 邊界條件測試**

```typescript
export const AiEdgeCaseTest: Story = {
    name: 'AI - Edge Case Test',
    args: { ...EdgeCaseArgs },
    render: args => ({ props: args, template: `<{component-selector}></{component-selector}>` }),
    play: async ({ canvasElement, step }) => { // 測試極端參數、邊界情況 });
    }
};
```

### **第五階段：Test ID Verification - 測試 ID 驗證測試**

```typescript
export const TestIdVerificationTest: Story = {
    name: 'AI - Test ID Verification',
    args: {
        ...DefaultArgs,
        prefixKeyForTestId: 'test-prefix',
        // 設置具有代表性的測試資料
    },
    play: async ({ canvasElement, step }) => { // 測試 Test ID 是否存在 });
    }
};
```

**Test ID Verification 測試要求**

* **用途**：驗證組件的測試 ID 實作符合規範
* **目的**：驗證組件的測試 ID 實作符合規範
* **驗證項目**：
  - 主要組件測試 ID 格式正確
  - 子元件測試 ID 格式正確（如有）
  - UniqueKey 前綴功能正常
  - 測試 ID 可被測試工具正確識別

---

## 📋 Story 結構範本

### **基本 Meta 設定**

```typescript
const meta: Meta<{ComponentName}> = {
    title: '{依據分類}/{componentName}',
    component: {ComponentName},
    tags: ['autodocs'],
    parameters: {
        docs: {
            description: { component: '組件完整說明文件' }
        }
    }
};
```

### **測試參數設定**

```typescript
const DefaultArgs = { prefixKeyForTestId: '' };
const InteractiveArgs = { };
const NoDataArgs = { prefixKeyForTestId: 'no-data-test', data: [], items: [] };
const EdgeCaseArgs = { prefixKeyForTestId: 'edge-case', extremeValue: 99999, longText: '...超長文字...' };
const TestIdArgs = { prefixKeyForTestId: 'test-prefix' }; // 測試 ID 驗證用
```

---

## 🎯 測試開發流程

1. Render Test → 基礎渲染
2. Interactive Test → 互動測試
3. No Data Test → 無資料穩定性
4. Edge Case Test → 極端與邊界條件測試
5. Test ID Verification → 測試 ID 驗證

---

## ✅ 品質檢查清單

### **Edge Case Test 檢查**

* [ ] 異常輸入正確處理
* [ ] 極端數值不導致崩潰
* [ ] 超長字串正確顯示或截斷
* [ ] 錯誤狀態提示正確顯示
* [ ] 元件能保持穩定運作

### **Test ID Verification 檢查**

* [ ] 主要組件測試 ID 格式正確
* [ ] 子元件測試 ID 格式正確（如有）
* [ ] UniqueKey 前綴功能正常運作
* [ ] 測試 ID 可被測試工具正確識別
* [ ] 多實例情況下 ID 不重複
* [ ] 符合 test-id.mdc 規範要求

---

## 🎭 實際應用概念

* **🧪 Render Test** → 基礎保障
* **🔧 Interactive Test** → 深入互動
* **📭 No Data Test** → 無資料健壯性
* **⚠️ Edge Case Test** → 極端條件補強
* **🔍 Test ID Verification** → 測試 ID 規範驗證
