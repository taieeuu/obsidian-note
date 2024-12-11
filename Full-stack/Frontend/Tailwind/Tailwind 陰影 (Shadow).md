---
Date: 2024-09-07
---
### 陰影效果
#### 陰影類別：
- **`shadow-sm`**：小陰影。
- **`shadow-md`**：中等陰影。
- **`shadow-lg`**：大陰影。
- **`shadow-xl`**：非常大的陰影。
- **`shadow-2xl`**：極大的陰影。
- **`shadow-inner`**：內陰影（應用於元素內部的陰影）。
- **`shadow-none`**：無陰影。

範例
```html
<div class="m-5 p-3 border rounded-lg shadow-md">這是一個中等陰影效果的元素</div>
<div class="m-5 p-3 border rounded-lg shadow-lg">這是一個大陰影效果的元素</div>
```
### 陰影顏色（Shadow Colors）
#### 陰影顏色類別
- **`shadow-<color>-<shade>/<opacity>`**：設置陰影的顏色、深淺和透明度。例如，`shadow-red-500/50` 表示紅色陰影，透明度為 50%。

範例
```html
<div class="m-5 p-3 border rounded-lg shadow-lg shadow-red-500/50">這是一個帶有紅色陰影的元素</div>
<div class="m-5 p-3 border rounded-lg shadow-lg shadow-black/20">這是一個帶有黑色 20% 透明陰影的元素</div>
```