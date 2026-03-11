## 📋 輸出原則

- **入口**：`src/lib`
- **命名規則**：`kebab-case`（檔案/資料夾）、`PascalCase`（Class/Enum）、`camelCase`（變數/函式）。
- **檔案單元**：一個功能一個檔案，旁邊放 `*.spec.ts` 測試檔。
- **匯出**：對內使用 `index.ts`；對外僅允許透過 `public-api.ts`。
- **主元件**：必須建立 `{library-name}.component.{ts,html,scss,spec.ts}`。
- **🚫 避免多餘組件**：優先整合功能到主組件，避免不必要的子組件和包裝器。

---

## 🏗️ Library 標準目錄結構

```
src/lib/
├─ {library-name}.component.ts         # 📌 主元件（所有功能集中於此）
├─ {library-name}.component.html       # 📌 主元件範本
├─ {library-name}.component.scss       # 📌 主元件樣式
├─ {library-name}.component.spec.ts    # 📌 主元件測試
├─ testing/                            # 🧪 測試假資料 (推薦)
│   ├─ {model}.mock.ts                 # 📋 模型假資料
│   ├─ {service}.stub.ts               # ⚙️ 服務 stub/fake
│   ├─ {helper}.mock.ts                # 🛠️ 工具假資料
│   └─ index.ts                        # 🔄 barrel (測試專用匯出)
├─ enums/                              # 🏷️ 列舉定義
├─ storybook/                              # 🔧 storybook 測試開發
├─ directives/{name}.directive.{ts,spec.ts}  # 📏 指令（僅必要時）
├─ pipes/{name}.pipe.{ts,spec.ts}           # 🔧 管道（僅必要時）
├─ services/{name}.service.{ts,spec.ts}     # ⚙️ 服務（僅必要時）
├─ models/{name}.model.ts                   # 📋 模型/介面
├─ utils/{helper}.util.ts                   # 🛠️ 工具函式
├─ tokens/{token}.ts                        # 🎯 注入令牌
├─ index.ts                                 # 🔄 barrel（內部使用）
└─ public-api.ts                            # 🌐 唯一對外 API
```

### 🚫 避免產生的多餘結構
- ❌ **不建立 `components/` 子目錄**：將所有功能整合到主組件
- ❌ **避免包裝器組件**：如 `-detail`、`-wrapper`、`-container` 等無意義層級
- ❌ **減少子組件**：除非真正可重用，否則整合到主組件
- ✅ **優先合併**：將相關功能合併到單一主組件中

---

## ✅ 快速驗收清單（LLM 產出用）

### 📌 必要結構檢查
- [ ] 是否存在 `{library-name}.component.ts|html|scss|spec.ts`？
- [ ] 是否存在 `public-api.ts`，並為唯一對外入口？
- [ ] 是否所有子資料夾有 `index.ts` 作 barrel？
- [ ] 是否有 `ng-package.json` 與 `package.json`？

### 🚫 多餘結構檢查
- [ ] 是否避免了 `components/` 子目錄？
- [ ] 是否避免了包裝器組件（如 `-detail`、`-wrapper`）？
- [ ] 主組件是否整合了所有核心功能？
- [ ] 是否只在真正需要時才建立子組件？

### 🎯 設計原則驗證
- [ ] 組件架構是否簡潔且易於維護？
- [ ] 對外 API 是否清晰且最小化？
- [ ] 是否遵循單一職責原則？
- [ ] 庫的使用是否直觀且不複雜？
