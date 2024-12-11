---
Date: 2024-09-08
---
### 1. 相對定位 (Relative Positioning)
**`relative`** 是 **CSS** 中的一種定位方式，它允許元素相對於自身的原始位置進行微調。與絕對定位不同，使用 `relative` 定位的元素依然保持在正常文檔流中，它會佔據原來的位置，但可以使用 `top`、`right`、`bottom` 和 `left` 來調整它的實際顯示位置。
#### 相對定位屬性
- **`relative`**：設置一個元素的定位方式，使其可以相對於其原始位置進行移動。
- **`top`、`bottom`、`left`、`right`**：使用這些屬性來微調元素的位置。

範例
```html
<div class="relative bg-blue-200 p-4">
  這是一個相對定位的元素
  <div class="relative top-2 left-4 bg-red-500 p-2">
    這個元素相對於自己的位置向下移動了 2 單位，向右移動了 4 單位。
  </div>
</div>
```

這裡外層容器是相對定位，內層元素使用 `relative` 定位後，向下移動了 `2` 單位，向右移動了 `4` 單位，但它仍然佔據其原始位置的空間。
### 2. 絕對定位 (Absolute Positioning)
**`absolute`** 定位將元素從正常的文檔流中移除，並相對於其最近的相對定位（`relative`）或絕對定位的父容器進行定位。如果沒有父容器有相對或絕對定位，則相對於 `body` 元素進行定位。
#### 常用屬性：
- **`absolute`**：使元素進入絕對定位模式，根據父容器進行精確定位。
- **`top`、`bottom`、`left`、`right`**：設置元素的具體位置。

範例
```html
<div class="relative bg-red-200 h-48 w-48">
  <div class="absolute top-0 left-0 bg-red-500 p-2">
    絕對定位的左上角元素
  </div>
  <div class="absolute bottom-0 right-0 bg-blue-500 p-2">
    絕對定位的右下角元素
  </div>
</div>
```

這裡的兩個絕對定位的元素分別定位在父容器的左上角和右下角，父容器使用了 `relative` 來作為絕對定位的參考點。
### 3. Inset 屬性
**Inset** 是 **Tailwind CSS** 中一個便捷的屬性，能夠控制元素與父容器的上下左右邊距（即 `top`、`right`、`bottom` 和 `left`），可以簡化定位操作。
#### 常用屬性
- **`inset-*`**：同時設置上下左右的距離。
- **`inset-x-*`**：設置水平方向（左右）的距離。
- **`inset-y-*`**：設置垂直方向（上下）的距離。
- **`-inset-*`**：使用負值來讓元素超出容器邊界。

範例
```html
<div class="relative bg-green-200 size-28 m-4">
  <div class="absolute inset-1 bg-green-500 p-2 text-white">
    Inset 1 單位
  </div>
</div>
```

這個元素在容器內部的四個邊上都設置了 1 單位的距離，使其保持均等的內邊距。

範例
```html
<div class="relative bg-yellow-200 size-28 m-4">
  <div class="absolute inset-x-2 bg-yellow-500 p-2 text-white">
    水平居中對齊
  </div>
</div>
```
### 總結
- **相對定位（Relative Positioning）** 是用於微調元素相對於其自身的原始位置。
- **絕對定位（Absolute Positioning）** 允許元素脫離正常文檔流，並根據父容器進行精確定位。
- **Inset** 屬性使得 Tailwind CSS 中的定位操作更為簡便，尤其是在需要同時設置多個方向的距離時。