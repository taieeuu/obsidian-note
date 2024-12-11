---
Date: 2024-09-08
---
**`columns`** 類別在 **Tailwind CSS** 中用來快速創建響應式的多列佈局。這個類別基於 **CSS 多列佈局** 模組，允許將內容自動分配到多個欄位中，類似於報紙或雜誌的佈局風格。透過簡單的 `columns` 類別，可以輕鬆實現靈活的列佈局，無需使用傳統的 **CSS grid** 或 **flexbox** 系統。
### 主要特點
1. **響應式多列佈局**：
    - `columns` 類別能夠創建根據螢幕大小調整的多列佈局。你可以使用 `columns-2`、`columns-3` 等類別來控制容器內顯示的列數。
    - 隨著螢幕尺寸變化，列數會根據可用的寬度進行自動調整。

2. **自動內容分配**：
    - 當應用 `columns` 類別時，內容會自動分配到指定的列中。瀏覽器會根據可用的空間自動將內容分配到每一列中，創建均勻的佈局效果。
      
3. **靈活的列寬控制**：
    - Tailwind 提供了像 **`columns-xs`**、**`columns-sm`**、**`columns-lg`** 等類別，用於控制每列的寬度。可以根據需要選擇較窄或較寬的列，這讓多列佈局更具彈性。
      
4. **列間距（Gap）**：
    - 你可以使用 `gap-*` 類別來調整列與列之間的間距，提供更好的佈局控制。例如，`gap-4` 會設置 1rem 的列間距。
      
5. **內容斷列控制**：
    - **`break-inside`** 屬性用來控制內容在列中是否斷開。使用 `break-inside-avoid` 可以防止內容被拆分到不同的列，確保區塊完整顯示在同一列中。
### 類別概覽
- **`columns-2`**：將內容分為兩列。
- **`columns-3`**：將內容分為三列。
- **`columns-xs`**、**`columns-lg`**：設置固定列寬，依據螢幕大小進行調整。
- **`break-inside-avoid`**：避免內容在不同列中拆分。
- **`gap-*`**：調整列之間的間距。

範例
```html
<div class="columns-3 gap-4">
  <div class="bg-red-200 p-4">內容區塊 1</div>
  <div class="bg-blue-200 p-4">內容區塊 2</div>
  <div class="bg-green-200 p-4">內容區塊 3</div>
</div>
```

這段程式碼將內容自動分為 3 列，並設置了 1rem 的列間距（`gap-4`）。

**使用 `break-inside-avoid` 防止內容斷裂**：
```html
<div class="columns-3 gap-4">
  <div class="bg-yellow-200 p-4 break-inside-avoid">這是完整顯示的內容區塊</div>
  <div class="bg-purple-200 p-4">其他內容區塊</div>
</div>
```

`break-inside-avoid` 確保內容不會被拆分到不同列，整個區塊會保持在同一列中顯示。
### 應用場景
- 創建報紙風格的佈局。
- 將大段內容分成多列以提高可讀性。
- 類似圖庫或文章列表的動態內容分配。
### 自訂設定
你可以通過在 `tailwind.config.js` 文件中自訂 `columns` 行為，根據專案需求調整列寬和列間距。