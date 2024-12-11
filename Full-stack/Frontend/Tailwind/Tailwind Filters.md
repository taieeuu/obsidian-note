---
Date: 2024-09-08
---
**`filter`** 是 Tailwind CSS 中的一個實用功能，用來為元素添加一系列的視覺效果（如模糊、亮度、對比度、灰度等）。這些效果可以應用在圖片、文字、按鈕、區塊元素等，讓頁面變得更加生動和動態。
### 1. 啟用 Filters
- 使用 `filter` 類別來啟用所有的過濾器。
- 如果需要禁用過濾效果，可以使用 `filter-none` 類別。

```html
<img src="image.jpg" class="filter" alt="Filtered Image">
```

`filter` 類別啟用了 Tailwind 中的所有過濾器，讓你可以對元素應用多種視覺效果。

---
### 2. **模糊效果 (Blur)**
- 使用 `blur-*` 類別來應用模糊效果：
    - **`blur-sm`**: 小幅模糊。
    - **`blur`**: 預設的模糊。
    - **`blur-md`**: 中等模糊。
    - **`blur-lg`**: 大幅模糊。
    - **`blur-xl`**: 非常大幅的模糊。
    - **`blur-2xl`**: 超級大幅模糊。
    - **`blur-3xl`**: 極致模糊。

```html
<img src="image.jpg" class="filter blur-md" alt="Blurred Image">
```

使用 `blur-md` 將圖片應用中等程度的模糊效果。

---
### 3. 亮度調整 (Brightness)
- 使用 `brightness-*` 類別來調整亮度：
    - **`brightness-0`**: 完全黑暗（亮度為 0）。
    - **`brightness-50`**: 調整亮度至 50%。
    - **`brightness-100`**: 原始亮度（100%）。
    - **`brightness-150`**: 增加亮度至 150%。
    - **`brightness-200`**: 增加亮度至 200%。

```html
<img src="image.jpg" class="filter brightness-150" alt="Brightened Image">
```

使用 `brightness-150` 將圖片亮度增加至 150%。

---
### 4. 對比度調整 (Contrast)
- 使用 `contrast-*` 類別來調整對比度：
    - **`contrast-0`**: 無對比度（0%）。
    - **`contrast-50`**: 50% 對比度。
    - **`contrast-100`**: 預設對比度（100%）。
    - **`contrast-150`**: 150% 對比度。
    - **`contrast-200`**: 200% 對比度。

```html
<img src="image.jpg" class="filter contrast-150" alt="High Contrast Image">
```

使用 `contrast-150` 將圖片對比度增加至 150%。

---
### 5. 灰度效果 (Grayscale)
- 使用 `grayscale` 類別將元素轉換為灰度（黑白）：
    - **`grayscale-0`**: 無灰度（彩色）。
    - **`grayscale`**: 100% 灰度（完全黑白）。

```html
<img src="image.jpg" class="filter grayscale" alt="Grayscale Image">
```

使用 `grayscale` 將圖片轉換為完全的黑白效果。

---
### 6. 色相旋轉 (Hue Rotate)
- 使用 `hue-rotate-*` 類別來旋轉圖片的色相：
    - **`hue-rotate-15`**: 色相旋轉 15 度。
    - **`hue-rotate-30`**: 色相旋轉 30 度。
    - **`hue-rotate-60`**: 色相旋轉 60 度。
    - **`hue-rotate-90`**: 色相旋轉 90 度。
    - **`hue-rotate-180`**: 色相旋轉 180 度。

```html
<img src="image.jpg" class="filter hue-rotate-90" alt="Hue Rotated Image">
```

使用 `hue-rotate-90` 讓圖片色相旋轉 90 度，改變整體色彩。

---
### 7. 反轉效果 (Invert)
- 使用 `invert` 將圖片的顏色進行反轉：
    - **`invert-0`**: 無反轉效果。
    - **`invert`**: 完全反轉顏色。

```html
<img src="image.jpg" class="filter invert" alt="Inverted Image">
```

使用 `invert` 完全反轉圖片的顏色。

---
### 8. 不透明度 (Opacity)
- 使用 `opacity-*` 類別來調整圖片的不透明度：
    - **`opacity-0`**: 完全透明（不可見）。
    - **`opacity-50`**: 50% 透明度。
    - **`opacity-100`**: 完全不透明（預設）。

```html
<img src="image.jpg" class="filter opacity-50" alt="Semi-transparent Image">
```

使用 `opacity-50` 將圖片透明度調整為 50%。

---
### 9. 飽和度 (Saturate)
- 使用 `saturate-*` 類別來調整圖片的飽和度：
    - **`saturate-0`**: 無飽和度（完全去色）。
    - **`saturate-50`**: 50% 飽和度。
    - **`saturate-100`**: 預設飽和度（100%）。
    - **`saturate-150`**: 150% 飽和度。
    - **`saturate-200`**: 200% 飽和度。

```html
<img src="image.jpg" class="filter saturate-150" alt="Saturated Image">
```

使用 `saturate-150` 將圖片的色彩飽和度增加至 150%。

---
### 10. 底片效果 (Sepia)
- 使用 `sepia` 類別將圖片轉換為褐色的底片效果：
    - **`sepia-0`**: 無效果。
    - **`sepia`**: 應用底片效果（褐色調）。

```html
<img src="image.jpg" class="filter sepia" alt="Sepia Image">
```

使用 `sepia` 將圖片轉換為復古風格的褐色調效果。
### 常見 Filter 類別：
- **模糊效果**：`blur`、`blur-sm`、`blur-md`
- **亮度調整**：`brightness-50`、`brightness-150`
- **對比度調整**：`contrast-50`、`contrast-150`
- **灰度效果**：`grayscale`
- **色相旋轉**：`hue-rotate-90`
- **反轉效果**：`invert`
- **飽和度**：`saturate-150`
- **底片效果**：`sepia`