---
Date: 2024-09-08
---
### 主要特點
1. **針對背景的效果**：應用於元素的背景，而不會影響其內容。
2. **多種過濾效果**：包括背景模糊、背景亮度、背景對比度、背景灰度等。
3. **實現透明玻璃效果**：常見於模糊背景效果，配合透明度可實現類似毛玻璃的視覺效果。
### 1. 模糊效果 (Backdrop Blur)
- 使用 `backdrop-blur-*` 類別來模糊背景：
    - **`backdrop-blur-sm`**: 小幅背景模糊。
    - **`backdrop-blur`**: 預設的背景模糊。
    - **`backdrop-blur-md`**: 中等背景模糊。
    - **`backdrop-blur-lg`**: 大幅背景模糊。
    - **`backdrop-blur-xl`**: 非常大的背景模糊。
    - **`backdrop-blur-2xl`**: 超大的背景模糊。
      
```html
<div class="backdrop-blur-lg bg-opacity-50 p-10">
  背景模糊效果
</div>
```

使用 `backdrop-blur-lg` 來模糊背景，同時配合 `bg-opacity-50` 讓背景半透明。

---
### 2. 亮度調整 (Backdrop Brightness)
- 使用 `backdrop-brightness-*` 類別來調整背景亮度：
    - **`backdrop-brightness-50`**: 調暗背景至 50%。
    - **`backdrop-brightness-100`**: 預設背景亮度。
    - **`backdrop-brightness-150`**: 增加背景亮度至 150%。
    - **`backdrop-brightness-200`**: 增加背景亮度至 200%。
      
```html
<div class="backdrop-brightness-150 bg-opacity-50 p-10">
  亮度調整的背景
</div>
```

使用 `backdrop-brightness-150` 調整背景亮度，使背景亮度增加到 150%。

---
### 3. 對比度調整 (Backdrop Contrast)
- 使用 `backdrop-contrast-*` 類別來調整背景對比度：
    - **`backdrop-contrast-50`**: 50% 對比度，減少背景的對比。
    - **`backdrop-contrast-100`**: 預設背景對比度。
    - **`backdrop-contrast-150`**: 增加背景對比度至 150%。

```html
<div class="backdrop-contrast-150 bg-opacity-50 p-10">
  對比度增加的背景
</div>
```

使用 `backdrop-contrast-150` 將背景對比度增加至 150%，增強背景的顏色對比。

---
### 4. 灰度效果 (Backdrop Grayscale)
- 使用 `backdrop-grayscale` 將背景轉換為灰度：
    - **`backdrop-grayscale-0`**: 無灰度（彩色）。
    - **`backdrop-grayscale`**: 完全灰度（黑白背景）。
      
```html
<div class="backdrop-grayscale bg-opacity-50 p-10">
  黑白背景效果
</div>
```

使用 `backdrop-grayscale` 將背景轉換為黑白效果。

---
### 5. 色相旋轉 (Backdrop Hue Rotate)
- 使用 `backdrop-hue-rotate-*` 來改變背景色彩的色相：
    - **`backdrop-hue-rotate-15`**: 背景色相旋轉 15 度。
    - **`backdrop-hue-rotate-90`**: 背景色相旋轉 90 度。
    - **`backdrop-hue-rotate-180`**: 背景色相旋轉 180 度。
      
```html
<div class="backdrop-hue-rotate-90 bg-opacity-50 p-10">
  色相旋轉的背景
</div>
```

使用 `backdrop-hue-rotate-90` 改變背景色彩，將色相旋轉 90 度。

---
### 6. 反轉效果 (Backdrop Invert)
- 使用 `backdrop-invert` 來反轉背景的顏色：
    - **`backdrop-invert-0`**: 無反轉。
    - **`backdrop-invert`**: 完全反轉顏色。

```html
<div class="backdrop-invert bg-opacity-50 p-10">
  顏色反轉的背景
</div>
```

使用 `backdrop-invert` 將背景顏色完全反轉。