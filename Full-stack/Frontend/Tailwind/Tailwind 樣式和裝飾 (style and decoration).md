---
Date: 2024-09-07
---
### 文字對齊 (`text-*`)
- **`text-left`**: 左對齊。
- **`text-right`**: 右對齊。
- **`text-center`**: 置中對齊。
- **`text-justify`**: 兩端對齊。
- **`text-start`** 和 **`text-end`**: 根據文字書寫方向的起點和終點進行對齊。

```html
<div class="text-left">左對齊的文字</div>
<div class="text-right">右對齊的文字</div>
<div class="text-justify">兩端對齊的文字</div>
<div class="text-center">置中對齊的文字</div>
```
### 文字大小寫 (`text-case`)
控制文字的大小寫方式：

- **`normal-case`**: 正常大小寫（無變化）。
- **`uppercase`**: 轉換為全大寫。
- **`lowercase`**: 轉換為全小寫。
- **`capitalize`**: 每個單詞的首字母大寫。

```html
<div class="uppercase">這段文字全大寫。</div>
<div class="lowercase">這段文字全小寫。</div>
<div class="capitalize">每個單詞的首字母大寫。</div>
```
### 字體樣式與粗細
- **`font-bold`**: 粗體字。
- **`font-semibold`**: 半粗體字。
- **`italic`**: 斜體字。

```html
<div class="font-bold">粗體字</div>
<div class="italic">斜體字</div>
<div class="font-bold italic">粗體斜體字</div>
```
### 文字裝飾 (`text-decoration`)
- **`underline`**: 下劃線。
- **`line-through`**: 刪除線。
- **`overline`**: 上劃線。

```html
<div class="underline">下劃線文字</div>
<div class="line-through">刪除線文字</div>
<div class="overline">上劃線文字</div>
```
### 裝飾顏色與樣式
- **裝飾顏色**：使用 `decoration-{color}` 設置顏色。
- **裝飾樣式**：使用 `decoration-{style}` 設置樣式，支持 `solid`、`dashed`、`dotted`、`double` 和 `wavy`。
- **裝飾厚度**：使用 `decoration-{thickness}` 設置線的厚度。

```html
<div class="underline decoration-red-500">紅色下劃線</div>
<div class="underline decoration-double">雙線下劃線</div>
<div class="underline decoration-4 decoration-blue-400">藍色粗下劃線</div>
```