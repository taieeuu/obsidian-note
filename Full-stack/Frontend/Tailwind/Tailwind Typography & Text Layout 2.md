---
Date: 2024-09-08
---
### 1. 溢出處理（Overflow Handling）
Tailwind 提供了幾種處理溢出內容的方式：

- **`overflow-hidden`**：隱藏超出容器的內容。
- **`overflow-scroll`**：如果內容溢出，則顯示滾動條。
- **`overflow-visible`**：讓溢出的內容可見。
- **`overflow-clip`**：直接裁剪溢出的內容，不顯示滾動條。

```html
<div class="h-24 overflow-y-scroll">這是一段很長的文字，會在垂直方向顯示滾動條。</div>
```
### 2. 空白處理（Whitespace Handling）
使用 Tailwind 的空白處理工具，您可以控制文本中的空白字符如何顯示：

- **`whitespace-normal`**：忽略多餘空白，類似 HTML 的默認行為。
- **`whitespace-pre`**：保留所有空白字符，像 `<pre>` 一樣顯示。
- **`whitespace-nowrap`**：文本不會換行，超出部分會溢出。
- **`whitespace-pre-line`**：保留斷行符號，但多餘的空白被壓縮。

```html
<div class="whitespace-nowrap overflow-x-scroll">這是一段不會換行的文字。</div>
```
### 3. 自定義內容插入（Content Utilities）
使用 `before` 和 `after` 假元素來插入自定義內容，這些工具可以通過 `content` 屬性設置顯示的文本：

- **`before:content`** 和 **`after:content`**：分別在元素前後插入自定義文本。
- **`attr()`** 函數可以從 HTML 屬性中提取值並顯示。

範例：
```html
<div class="before:content-['Girl_greet']">people</div>
<div greet="Welcome Home" class="before:content-[attr(greet)]">people</div>
```

注意如果`Girl greet`要空白的話，需使用下引線。-> `Girl_greet`。