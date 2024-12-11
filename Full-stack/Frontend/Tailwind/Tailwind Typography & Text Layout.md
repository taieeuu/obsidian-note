---
Date: 2024-09-08
---
### 1. 字體平滑處理（Antialiased & Subpixel Antialiased）
- **`antialiased`**：讓文本顯示為抗鋸齒狀態，使字體看起來更平滑。
- **`subpixel-antialiased`**：在子像素級別進行抗鋸齒處理，讓字體邊緣更清晰。

```html
<div class="antialiased">這段文字使用抗鋸齒處理。</div>
<div class="subpixel-antialiased">這段文字使用子像素抗鋸齒處理。</div>
```
### 2. 字間距 (Tracking)
**Tracking** 用來調整字母之間的間距，Tailwind 提供了多種選擇：

- **`tracking-tight`**：緊密的字母間距。
- **`tracking-wide`**：寬鬆的字母間距。
- **`tracking-widest`**：最寬鬆的字母間距。

```html
<div class="tracking-tight">字母間距緊密</div>
<div class="tracking-wide">字母間距寬鬆</div>
```
### 3. 行高 (Leading)
行高可以調整多行文本之間的距離：

- **`leading-tight`**：緊湊的行高。
- **`leading-loose`**：寬鬆的行高。
- **`leading-[3-10]`**：具體設置行高的數值。

```html
<div class="leading-tight">行高緊湊</div>
<div class="leading-7">行高適中</div>
```
### 4. 縮排 (Indent)
縮排用來設定段落的縮進，甚至可以使用負縮排讓文本向左移動：

- **`indent-10`**：設置正縮排，縮進 10 單位。
- **`-indent-10`**：設置負縮排，文本向左移動。

```html
<div class="indent-10">這段文字有 10 單位的縮排。</div>
<div class="-indent-10">這段文字有 10 單位的負縮排。</div>
```
### 5. 文字截斷 (Truncate)
`truncate` 可以用來截斷超出一行顯示的文字，並在結尾顯示省略號。

```html
<div class="truncate">這是一段很長的文字，會被截斷並顯示省略號。</div>
```
### 6. 行數限制 (Line Clamp)
這個工具用來限制顯示的文本行數，並隱藏超過指定行數的文本。您可以設置行數，比如 `line-clamp-3`，限制文本最多顯示三行，超過部分會被隱藏。

```html
<div class="line-clamp-3">這是一段很長的文字，超過三行會被截斷。</div>
```