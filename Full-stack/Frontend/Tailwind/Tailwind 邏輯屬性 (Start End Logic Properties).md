---
Date: 2024-09-07
---
**Start** 和 **End** 是指針對文本書寫方向（LTR 或 RTL）來應用的屬性，這些屬性稱為 **邏輯屬性（Logical Properties）**。**Start** 代表開始邊，**End** 代表結束邊。這些邏輯屬性可以根據文本的書寫方向自動調整邊距、對齊、浮動等樣式，使其更靈活，適用於不同的語言環境。
### 應用於哪些屬性？
**Start** 和 **End** 可以應用於許多屬性，如邊距（margin）、填充（padding）、圓角（border-radius）、對齊（text alignment）和浮動（float）。這些屬性根據書寫方向自動適應。
#### 常見的屬性包括：
- **Margin 和 Padding**：`ms`（margin-start），`me`（margin-end），`ps`（padding-start），`pe`（padding-end）。
- **邊框圓角**：`rounded-s`（開始邊圓角），`rounded-e`（結束邊圓角）。
- **浮動（float）**：`float-start`，`float-end`。
- **文本對齊**：`text-start`，`text-end`。
### Start 和 End 的應用語法
#### 邊距和填充
- **`ms-{size}`**：設置開始邊的外邊距（margin-start）。
- **`me-{size}`**：設置結束邊的外邊距（margin-end）。
- **`ps-{size}`**：設置開始邊的內填充（padding-start）。
- **`pe-{size}`**：設置結束邊的內填充（padding-end）。

```html
<!-- 根據書寫方向設置邊距 -->
<div class="ms-4 me-2">這個元素在開始邊有 1rem 邊距，在結束邊有 0.5rem 邊距</div>

<!-- 根據書寫方向設置內填充 -->
<div class="ps-4 pe-2">這個元素在開始邊有 1rem 填充，在結束邊有 0.5rem 填充</div>
```
#### 圓角
- **`rounded-s-{size}`**：設置開始邊的圓角。
- **`rounded-e-{size}`**：設置結束邊的圓角。
#### 文本對齊
- **`text-start`**：根據書寫方向將文本對齊到開始邊。
- **`text-end`**：根據書寫方向將文本對齊到結束邊。
