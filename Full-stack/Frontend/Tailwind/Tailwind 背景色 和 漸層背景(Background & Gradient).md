---
Date:
---
### 1. 實色背景 (Solid Background)
#### 格式
- `bg-{color}` : 設置元素的背景顏色。每個顏色都可以根據不同的透明度和色調進行調整。

範例
```html
<div class="size-20 bg-cyan-500 m-2"></div> <!-- 青色背景 -->
<div class="size-20 bg-yellow-700/25 m-2"></div> <!-- 帶有25%透明度的黃色背景 -->
```
### 2. 漸層背景 (Gradient Background)
#### 格式
- `bg-gradient-{方向}` : 指定漸層方向，並且搭配 `from-{color}` 和 `to-{color}` 指定漸層顏色。

範例：
```html
<div class="h-16 w-64  m-2 bg-gradient-to-t  to-cyan-100 from-cyan-600"></div> <!-- 從底部到頂部的漸層 -->
<div class="h-16 w-64  m-2 bg-gradient-to-bl  to-red-500 from-cyan-500"></div> <!-- 從右上到左下的漸層 -->
```

這裡的 **`bg-gradient-to-t`** 設置了從底部到頂部的漸層，`from-cyan-600` 設置漸層起點的顏色，`to-cyan-100` 設置漸層終點的顏色。
### 3. 漸層色彩停留點（Gradient Color Stops）
#### 格式
- `via-{color}` : 可以在漸層的中間加入更多的顏色停留點。這樣可以實現更複雜的漸層效果。

範例：
```html
<div class="h-16 w-64 m-2 bg-gradient-to-r from-indigo-500 from-10% via-sky-500 via-30% to-emerald-500 to-90%"></div>
```

這裡使用了 **`from-indigo-500`**（從藍色開始），**`via-sky-500`**（中間停留點為天藍色），並在 **`to-emerald-500`**（最後變為綠色）結束。漸層的變化點也指定了百分比，表示色彩在哪個百分比的位置開始或結束。
### 總結
- **實色背景**：`bg-{color}` 用來設置背景顏色，還可以調整透明度。
- **漸層背景**：`bg-gradient-{方向} from-{color} to-{color}`，用於設置線性漸層背景。
- **漸層停留點**：`via-{color}` 在漸層過程中增加更多顏色，讓過渡更加豐富。