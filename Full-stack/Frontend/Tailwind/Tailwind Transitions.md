---
Date: 2024-09-08
---
**Transitions** 是 Tailwind CSS 用來控制元素屬性變化時的過渡效果工具。它能夠為元素的狀態變化（如顏色、大小、位置等）提供平滑的過渡動畫，提升用戶體驗，讓頁面更具互動性和動態感。
##### 特點：
1. **平滑過渡**：讓元素從一個狀態變化到另一個狀態時更加自然流暢。
2. **靈活配置**：可以自定義過渡的持續時間、延遲時間及應用的屬性。
3. **多樣化應用**：適用於顏色、大小、透明度、邊框、變換等不同屬性。
### 1. 啟用過渡 (Transitions Basics)
#### `transition` 類別
- **`transition`**：啟用預設的過渡效果，過渡持續時間和應用的屬性會使用 Tailwind 的預設值。
- **`transition-all`**：讓所有可過渡的屬性都進行過渡。
- **`transition-colors`**：僅讓顏色相關屬性（如 `background-color`、`border-color` 等）進行過渡。
- **`transition-opacity`**：讓透明度屬性進行過渡。

```html
<div class="transition bg-blue-500 hover:bg-red-500 p-6">
  Hover 來改變顏色
</div>
```

當滑鼠懸停時，背景顏色會從藍色過渡到紅色。

---
### 2. 自定義過渡持續時間 (Duration)
使用 `duration-*` 類別來控制過渡持續時間（以毫秒計算）：
    - **`duration-75`**: 過渡持續 75ms。
    - **`duration-150`**: 過渡持續 150ms。
    - **`duration-500`**: 過渡持續 500ms。
      
```html
<div class="transition duration-500 bg-blue-500 hover:bg-red-500 p-6">
  顏色過渡持續 500ms
</div>
```

背景顏色的過渡持續時間為 500ms。

---
### 3. 自定義過渡延遲 (Delay)
使用 `delay-*` 類別來控制過渡延遲時間（以毫秒計算）：

- **`delay-75`**: 過渡延遲 75ms。
- **`delay-150`**: 過渡延遲 150ms。
- **`delay-300`**: 過渡延遲 300ms。

```html
<div class="transition delay-150 bg-blue-500 hover:bg-red-500 p-6">
  顏色過渡延遲 150ms
</div>
```

當滑鼠懸停時，背景顏色的過渡會延遲 150ms 才開始。

---
### 4. 過渡屬性 (Property)
- 使用 `transition-*` 類別來指定過渡應用的具體屬性：
    - **`transition-all`**：應用所有屬性。
    - **`transition-colors`**：應用顏色屬性（背景、文字顏色等）。
    - **`transition-opacity`**：應用透明度屬性。
    - **`transition-transform`**：應用變換屬性（例如旋轉、縮放、平移等）。

```html
<div class="transition-opacity duration-300 opacity-50 hover:opacity-100 p-6 bg-blue-500">
  透明度過渡
</div>
```

透明度的過渡會持續 300ms，當滑鼠懸停時，透明度從 50% 增加到 100%。

---
### 5. 過渡時間曲線 (Timing Function)
- 使用 `ease-*` 類別來定義過渡效果的速度曲線：
    - **`ease-linear`**：線性過渡，速度恆定。
    - **`ease-in`**：過渡開始時慢，結束時快。
    - **`ease-out`**：過渡開始時快，結束時慢。
    - **`ease-in-out`**：過渡開始和結束時都慢，中間快。

```html
<div class="transition ease-in-out duration-500 transform hover:scale-125 p-6 bg-blue-500">
  緩入緩出的縮放過渡
</div>
```

這個元素在滑鼠懸停時會平滑縮放，過渡開始和結束時較慢，中間較快。

---
### 6. 多重過渡效果
可以將多個過渡效果組合使用，來同時應用在不同屬性上，例如同時過渡顏色和大小。

```html
<div class="transition-all duration-500 ease-in-out transform hover:scale-110 hover:bg-red-500 p-6 bg-blue-500">
  顏色與縮放過渡
</div>
```

當滑鼠懸停時，這個元素的顏色會從藍色變為紅色，並且同時放大 10%。
