---
name: figma-check
description: "Figma URL 驗證技能：當需要從 Figma 讀取設計進行切版時，在開始前先驗證 URL 有效性。檢查：1⃣ URL 格式是否為 https://www.figma.com/file/... 或 https://www.figma.com/design/...、2⃣ 使用 Figma Tools 讀取檔案資訊驗證存取權限。驗證失敗會立即停止並回報「無法讀取 Figma 檔案，請確認連結權限或 URL 是否正確」。適用於切版流程開始前、Figma 設計稿讀取前、避免無效 URL 浪費時間。"
---

# Figma Check

驗證 Figma URL 有效性，確保設計資源可讀取。

## 檢查流程

1. **URL 格式** - 必須是 `https://www.figma.com/file/...` 或 `https://www.figma.com/design/...`
2. **存取權限** - 使用 Figma Tools 讀取檔案資訊
3. **錯誤處理** - 無效或無權限時立即停止

## 輸出

- ✅ 驗證通過 → 繼續執行切版
- ❌ 驗證失敗 → 回報「無法讀取 Figma 檔案，請確認連結權限或 URL 是否正確」並終止
