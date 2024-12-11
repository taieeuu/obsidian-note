---
Date: 2024-09-08
---
`Element Transformation` 用來對 HTML 元素進行 **位移**、**旋轉**、**縮放** 和 **傾斜** 等操作。這些變換效果可以讓元素根據設計需求進行不同的視覺變形，讓頁面內容更加動態和吸引人。

---
### 1. 平移 (Translate)
使用 `translate-x-*` 和 `translate-y-*` 類別來沿 X 軸或 Y 軸平移元素：

- **`translate-x-0`**: X 軸上無位移。
- **`translate-x-6`**: X 軸上移動 6 個單位（1.5rem）。
- **`translate-y-3`**: Y 軸上移動 3 個單位（0.75rem）。

```html
<div class="transform translate-x-6 translate-y-3">
  這個元素被平移
</div>
```

元素被在 X 軸上移動 6 個單位，Y 軸上移動 3 個單位。

---
### 2. 旋轉 (Rotate)
使用 `rotate-*` 類別來旋轉元素：

- **`rotate-0`**: 無旋轉。
- **`rotate-45`**: 旋轉 45 度。
- **`rotate-90`**: 旋轉 90 度。
- **`rotate-180`**: 旋轉 180 度。

```html
<div class="transform rotate-45">
  旋轉 45 度的元素
</div>
```

這個元素旋轉了 45 度。

---
### 3. 縮放 (Scale)
使用 `scale-*` 類別來縮放元素，可以沿著 X 軸、Y 軸或者同時縮放：

- **`scale-100`**: 預設大小（100%）。
- **`scale-50`**: 縮小至 50%。
- **`scale-150`**: 放大至 150%。
- **`scale-x-125`**: 僅沿 X 軸放大 125%。
- **`scale-y-75`**: 僅沿 Y 軸縮小至 75%。

```html
<div class="transform scale-125">
  放大 1.25 倍的元素
</div>
```

這個元素被放大了 1.25 倍。

---
### 4. 傾斜 (Skew)
使用 `skew-*` 類別來傾斜元素，指定沿 X 軸或 Y 軸進行傾斜：

- **`skew-x-12`**: X 軸上傾斜 12 度。
- **`skew-y-6`**: Y 軸上傾斜 6 度。

```html
<div class="transform skew-x-12 skew-y-6">
  傾斜的元素
</div>
```

這個元素在 X 軸上傾斜了 12 度，Y 軸上傾斜了 6 度。

---
### 5. 變換原點 (Transform Origin)
使用 `origin-*` 類別來控制元素的變換原點，這會影響旋轉、縮放或傾斜的中心點：

- **`origin-center`**: 變換從元素的中心開始（預設）。
- **`origin-top`**: 變換從元素的頂部開始。
- **`origin-bottom-right`**: 變換從元素的右下角開始。

```html
<div class="transform rotate-45 origin-top">
  旋轉的原點在頂部
</div>
```

這個元素從頂部進行了 45 度旋轉，而不是從中心點旋轉。

---
### 6. 混合使用變換效果
你可以同時應用多個變換效果來創造複雜的動畫或視覺效果。

```html
<div class="transform translate-x-4 rotate-12 scale-110">
  混合變換效果的元素
</div>
```

這個元素被沿 X 軸平移了 4 個單位，同時旋轉了 12 度並放大了 1.1 倍。
### 總結
1. **位移 (Translate)**：可以沿著 X 軸或 Y 軸移動元素。
2. **旋轉 (Rotate)**：可以旋轉元素，指定角度。
3. **縮放 (Scale)**：可以放大或縮小元素的尺寸。
4. **傾斜 (Skew)**：可以讓元素在 X 軸或 Y 軸上傾斜。
5. **變換原點 (Transform Origin)**：控制元素的變換原點。
