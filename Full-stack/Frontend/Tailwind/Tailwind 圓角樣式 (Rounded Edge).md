---
Date: 2024-09-07
---
### 1. 基礎圓角類別
#### 基礎類別語法：
- **`rounded`**：應用一個標準的圓角（預設大小）。
- **`rounded-sm`**：應用較小的圓角。
- **`rounded-md`**：應用中等大小的圓角。
- **`rounded-lg`**：應用較大的圓角。
- **`rounded-xl`**：應用超大圓角。
- **`rounded-2xl`**：應用更大的圓角。
- **`rounded-3xl`**：應用最大的圓角。
- **`rounded-full`**：將元素的所有邊設置為全圓形（適用於正方形元素來形成圓形）。

```html
<div class="rounded border p-4">這是一個標準圓角容器</div>
<div class="rounded-sm border p-4">這是一個小圓角容器</div>
<div class="rounded-lg border p-4">這是一個大圓角容器</div>
<div class="rounded-full border p-4">這是一個全圓形容器</div>
```
#### 針對特定邊的圓角
- **`s`**：start（開始邊，根據文本書寫方向，通常為左邊）。
- **`e`**：end（結束邊，根據文本書寫方向，通常為右邊）。
- **`t`**：top（上邊）。
- **`r`**：right（右邊）。
- **`l`**：left（左邊）。
- **`b`**：bottom（下邊）。
- **`tl`**：top-left（左上角）。
- **`tr`**：top-right（右上角）。
- **`bl`**：bottom-left（左下角）。
- **`br`**：bottom-right（右下角）。
- **`ss`**：start-side（開始邊的上側）。
- **`se`**：start-end（開始邊的下側）。
- **`ee`**：end-end（結束邊的下側）。
- **`es`**：end-start（結束邊的上側）。

```html
<div class="rounded-t-lg border p-4">這個容器只有上邊有大圓角</div>
<div class="rounded-l-md border p-4">這個容器左邊有中等圓角</div>
<div class="rounded-tl-xl border p-4">這個容器左上角有特大圓角</div>
```
### 3. 自定義圓角大小
除了使用 Tailwind 預設的圓角大小，您也可以根據需要自定義圓角的半徑。

```html
<div class="rounded-[20px] border p-4">這個容器的圓角為 20px</div>
<div class="rounded-[1rem] border p-4">這個容器的圓角為 1rem</div>
```