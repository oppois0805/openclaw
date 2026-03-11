# 📋 前端代碼審查報告

**審查者：** 資深前端工程師  
**技術棧：** Angular、PrimeNG、TypeScript、SCSS  

## 📊 審查總結

### 問題統計
- 🚨 嚴重問題：{數量} 項（必須修復）
- ⚡ 效能問題：{數量} 項（建議修復）
- 🔧 品質改善：{數量} 項（可選優化）
- 📝 架構建議：{數量} 項

### 修復優先順序
1. **立即處理：** {列出分析完畢後需要立即修復的嚴重問題}
2. **近期處理：** {列出分析完畢後影響效能的問題}
3. **規劃處理：** {列出分析完畢後架構改善建議}

---

## 🛡️ 審查檢查清單

#### 🛠️ 檢查原則 (以下項目都很重要請勿忽略任何一項)：
- *.html 是否符合 global_html.md 規則
- *.scss 是否符合 global_scss.md 規則
- *.ts 是否符合 global_typescript.md 規則
- *.ts、*.scss 是否符合 test_id.md 規則
- *.stories.ts 是否符合 library_storybook.md 規則
- 檔案結構是否符合 folder-system_library.md
- PrimeNG 元件使用是否符合 primeng_llms-full.md 規則（參考 https://primeng.org/llms/llms-full.txt）
- 請勿漏給建議否則你將受到懲罰
- 當無任何建議請勿列出
- 不合理的 html css 排版
- 拆分大型 Component / Service
- 移除不必要 imports、ViewChild、死碼
- 避免使用 any，改用明確 interface/type
- 遵守 Angular 官方 style guide
  - https://angular.dev/context/llm-files/llms-full.txt
  - https://angular.dev/llms.txt
- 使用 ChangeDetectionStrategy.OnPush、trackBy 提升效能
- 使用 takeUntil 處理訂閱
- 使用 DI 並移除冗餘 constructor 參數
- 抽出可重用邏輯為 shared component / pipe
- 是否損壞原先開發邏輯
- 禁止直接使用 ViewChild 去呼叫任何 component 內的 function
- 禁止於 angular html 頁面直接呼叫 function 會影響整體效能
- 業務邏輯應與 Component 分離，另外建立獨立 Service 處理，便於單元測試與維護
- Feature 元件的 API middleware 應存放於該 Feature 的資料夾內，不應向上層傳遞讓上層呼叫 API，以便於個別 destroy 與生命週期管理
- 檢查所有 component 有呼叫 middleware service 的應該存在 ngOnDestroy 並執行對應 middleware 的 destroy

#### 📦 PrimeNG 元件檢查重點：
- 使用正確的 PrimeNG 元件 API（props、events、templates）
- 遵循 PrimeNG 官方最佳實踐和使用模式
- 確認元件版本相容性（參考遷移指南）
- 正確使用 Pass Through 和 Design Tokens 進行樣式客製化
- 避免使用已棄用的元件或 API
- 表單元件需正確綁定 FormControl/ngModel
- 資料展示元件需使用 trackBy 提升效能
- 覆蓋元件（Dialog、Toast 等）需正確管理生命週期
- 選單元件需正確設定 MenuItem 結構
---

### ✅ 已檢查項目
- [ ] 程式碼品質（可讀性、命名規範、註解）
- [ ] 效能優化（Bundle大小、渲染效率、記憶體）
- [ ] 安全防護（XSS、CSRF、敏感資訊）
- [ ] 使用者體驗（響應式、載入提示、無障礙）
- [ ] 架構設計（組件職責、狀態管理、錯誤處理）
- [ ] 測試覆蓋（單元測試、整合測試）
- [ ] 瀏覽器相容（API支援、Polyfill）
- [ ] 開發體驗（TypeScript、代碼風格）
- [ ] PrimeNG 使用（元件 API、最佳實踐、版本相容）

---
## 🎨 PrimeNG 元件問題

### {問題標題}
- **檔案位置：** `{檔案路徑}:{行數}`
- **問題類型：** {API 誤用|已棄用元件|效能問題|樣式錯誤|版本不相容}
- **影響程度：** 🟡 中
- **說明：** {簡要說明 PrimeNG 使用問題}

**原始程式碼：**
```typescript
// 第 {行數} 行
{原始程式碼}
```

**建議修改：**
```typescript
// 根據 PrimeNG 官方文件修改
{修改後程式碼}
```

**參考文件：** `https://primeng.org/{component}.md`

**修復原因：** {說明為什麼需要修復，參考 PrimeNG 官方文件}

---
## 🚨 嚴重問題（必須修復）

### {問題標題}
- **檔案位置：** `{檔案路徑}:{行數}`
- **問題類型：** {安全漏洞|核心功能錯誤|相容性問題}
- **嚴重程度：** 🔴 高
- **說明：** {簡要說明問題}

**原始程式碼：**
```typescript
// 第 {行數} 行
{原始程式碼}
```

**建議修改：**
```typescript
// 修改建議
{修改後程式碼}
```

**修復原因：** {說明為什麼需要修復}

---

## ⚡ 效能問題（建議修復）

### {問題標題}
- **檔案位置：** `{檔案路徑}:{行數}`
- **問題類型：** {Bundle大小|渲染效率|記憶體洩漏|網路請求}
- **影響程度：** 🟡 中
- **說明：** {簡要說明效能影響}

**原始程式碼：**
```typescript
// 第 {行數} 行
{原始程式碼}
```

**建議優化：**
```typescript
// 優化建議
{優化後程式碼}
```

**效能提升：** {說明預期的效能改善}

---

## 🔧 品質改善（可選優化）

### {問題標題}
- **檔案位置：** `{檔案路徑}:{行數}`
- **改善類型：** {可讀性|命名規範|代碼重構|註解完善}
- **優先程度：** 🟢 低
- **說明：** {簡要說明改善點}

**原始程式碼：**
```typescript
// 第 {行數} 行
{原始程式碼}
```

**建議改善：**
```typescript
// 改善建議
{改善後程式碼}
```

**改善效益：** {說明改善後的好處}

---

## 📝 架構建議

### {建議標題}
- **影響範圍：** {組件層級|模組層級|應用層級}
- **建議類型：** {組件職責|狀態管理|錯誤處理|測試策略}
- **實施複雜度：** {簡單|中等|複雜}
- **說明：** {架構改善說明}

**目前架構：**
```typescript
// 目前實作方式
{目前程式碼結構}
```

**建議架構：**
```typescript
// 建議架構調整
{建議的架構設計}
```

**架構效益：** {說明長期維護和擴展的好處}

---