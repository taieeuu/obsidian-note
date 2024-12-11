---
Date: 2024-09-08
---
### 1. 背景重複（Image Repeat）
背景圖片的重複行為可以控制圖片是否在容器中重複顯示：

- **`bg-no-repeat`**：不重複背景圖片。
- **`bg-repeat-x`**：僅在水平軸上重複圖片。
- **`bg-repeat-y`**：僅在垂直軸上重複圖片。

```html
<div style="background-image: url('../images/10.jpg');"
    class="w-[1512px] h-[1768px] border-4 border-blue-500 m-3 bg-no-repeat"></div>
```

這個範例展示了一個寬度 1512px、高度 1768px 的區塊，其背景圖片不會重複顯示，無論圖片尺寸如何，區塊內只會顯示一次圖片，圖片之外的區域將保持空白。

```html
<div style="background-image: url('../images/10.jpg');"
    class="w-[1512px] h-[1768px] border-4 border-blue-500 m-3 bg-repeat-x"></div>
```

這個範例中，圖片僅在水平方向重複。垂直方向保持不變，適合在水平布局中使用，例如背景條紋。

```html
<div style="background-image: url('../images/10.jpg');"
    class="w-[1512px] h-[1768px] border-4 border-blue-500 m-3 bg-repeat-y"></div>
```

這裡圖片僅在垂直方向重複，適合設計垂直條紋或背景特效。
### 2. 背景位置（Background Position）

根據設計需求將背景圖片放置在容器的不同位置：

- **`bg-top`**：背景圖片位於頂部。
- **`bg-bottom-right`**：背景圖片位於右下角。
- **`bg-center`**：背景圖片位於容器中心。

```html
<div style="background-image: url('../images/10.jpg');"
    class="w-[800px] h-[800px] border-4 border-blue-500 m-3 bg-no-repeat bg-top"></div>
```

這個範例中，表示背景圖片固定於容器的頂部，圖片只顯示一次，不會重複，並且容器的大小為 800px x 800px。

```html
<div style="background-image: url('../images/10.jpg');"
    class="w-[800px] h-[800px] border-4 border-blue-500 m-3 bg-no-repeat bg-bottom-right"></div>
```

這裡的圖片被定位到容器的右下角，同樣圖片只顯示一次且不重複。適合對齊容器右下角的設計。

```html
<div style="background-image: url('../images/10.jpg');"
    class="w-[800px] h-[800px] border-4 border-blue-500 m-3 bg-no-repeat bg-center"></div>
```

這個範例中的背景圖片居中顯示，圖片不會重複，並且保持其原始位置在容器的中心。
### 3. 背景尺寸（Image Sizing）
- **`bg-auto`**：背景圖片以其原始尺寸顯示。
- **`bg-cover`**：背景圖片擴展或縮小以覆蓋整個容器，可能會裁切部分圖片。
- **`bg-contain`**：背景圖片縮放以適應容器，並保持圖片比例。

```html
<div style="background-image: url('../images/10.jpg');"
    class="w-[700px] h-[300px] border-4 border-blue-500 m-3 bg-no-repeat bg-auto"></div>
```

這裡背景圖片將以原始尺寸顯示，圖片不會被拉伸或縮小，只是保持其原始大小，無論容器的大小如何。

```html
<div style="background-image: url('../images/10.jpg');"
    class="w-[700px] h-[500px] border-4 border-blue-500 m-3 bg-no-repeat bg-cover"></div>
```

背景圖片會根據容器大小自動縮放或裁切，以確保完全覆蓋容器。適合全屏背景設計，但圖片的部分內容可能被裁切。

```html
<div style="background-image: url('../images/10.jpg');"
    class="w-[700px] h-[500px] border-4 border-blue-500 m-3 bg-no-repeat bg-contain"></div>
```

在這個範例中，背景圖片將保持比例縮放以適應容器，圖片不會被裁切，但可能會有空白區域。
### 4. 背景固定 (bg-fixed)
- **`bg-fixed`**：背景圖片固定在頁面中，不會隨頁面滾動。
- **`bg-scroll`**（預設）：背景圖片會隨著內容滾動。
- **`bg-local`**：背景圖片會隨著其元素的滾動條滾動。
範例
```html
<div class="w-full h-[500px] bg-fixed bg-center bg-cover" style="background-image: url('background.jpg');">
  這是一個帶有固定背景圖片的區塊
</div>
```

這段代碼創建了一個全寬高 500px 的區塊，背景圖片居中顯示且會固定在視窗中，即使頁面滾動，背景圖片也保持不動。
### 總結
- **背景重複（Image Repeat）**：控制背景圖片是否在水平和垂直方向上重複。
- **背景定位（Image Positioning）**：設置背景圖片在容器中的位置，如頂部、底部、居中等。
- **背景尺寸（Image Sizing）**：調整圖片尺寸以適應容器，或保持原始大小，或覆蓋整個容器。
- **背景固定（Background Attachment）**：使用 `bg-fixed` 使背景圖片在頁面滾動時保持固定，背景圖片不會隨著頁面內容一起滾動。這通常用於視差滾動效果，讓背景圖片和前景內容產生視覺上的深度對比。