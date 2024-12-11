---
Date: 2024-09-08
---
### 1. Object Fit
`object-fit` 屬性用來控制如何調整替換元素（例如圖片、影片）的大小，以適應它的容器。這些值決定了圖片在容器中的顯示方式。

- **`object-contain`**：圖片將保持原始比例縮放以完全適應容器，可能會留有空白區域，圖片不會被裁切。
- **`object-cover`**：圖片會縮放以覆蓋整個容器，可能會裁切圖片的部分內容。
- **`object-fill`**：圖片會被拉伸以完全適應容器，比例可能會失真。
- **`object-none`**：圖片保持原始大小，且不會縮放，可能會超出容器。
- **`object-scale-down`**：圖片會根據需要縮小或保持原始大小，以適應容器。

範例：
```html
<img src="../images/7.jpg" alt="" class="h-72 w-full object-contain">
```

這裡圖片的高度設置為 `h-72`（通常約 18rem），寬度設置為 `w-full`（即寬度占據整個容器）。`object-contain` 表示圖片會縮放以適應容器，並且保持原始比例，因此可能會在容器中留下空白。
### 2. Object Position
`object-position` 屬性控制圖片在容器中的顯示位置，當圖片大小不足以完全填充容器時可以使用此屬性。

- **`object-right`**：圖片定位在容器的右側。
- **`object-left`**：圖片定位在容器的左側。
- **`object-center`**：圖片居中顯示（默認）。

範例
```html
<img src="../images/7.jpg" alt="" class="h-72 w-full object-contain object-right">
```

這裡圖片設置為 `object-contain`，即縮放以適應容器，並保持比例，不會裁切。同時，`object-right` 會將圖片內容定位到容器的右側，這意味著圖片會保持在右邊，而可能在左側留下空白。
### 總結
- **`object-fit`**：控制替換元素（例如圖片）的大小調整方式，以適應容器。可以選擇 `contain` 來保持比例，或使用 `cover` 來覆蓋容器。
- **`object-position`**：控制元素在容器中的位置，例如將圖片固定在容器的右側或左側。

