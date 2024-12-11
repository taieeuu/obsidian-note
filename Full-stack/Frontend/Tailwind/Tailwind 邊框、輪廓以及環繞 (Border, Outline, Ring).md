---
Date: 2024-09-07
---
### `border` (邊框)
`border` 用來設置元素的邊框，可以控制邊框的寬度、顏色和圓角。
#### 類別：
##### 邊框寬度 (`border-{size}`)
- 設置邊框的寬度，例如 `border-2` 表示 2px 寬度的邊框。

```html
<div class="border-2 border-red-500">這段文字有 2px 寬度的紅色邊框</div>
```
##### 邊框顏色 (`border-{color}`)
- 設置邊框顏色，顏色選項和 `text`、`bg` 類似。

```html
<div class="border-4 border-blue-600">這段文字有 4px 寬度的藍色邊框</div>
```
##### 邊框圓角 (`rounded-{size}`)
- 設置元素的圓角，範圍從 `rounded-sm` 到 `rounded-full`，或者自定義尺寸。

```html
<div class="border-2 border-green-500 rounded-lg">這段文字有圓角邊框</div>
```
### `outline` (輪廓)
`outline` 用於設置元素的外輪廓，通常在元素獲得焦點時出現。與 `border` 不同的是，`outline` 不會影響元素的尺寸和佈局。
#### 類別：
##### 輪廓寬度 (`outline-{size}`)：
- 設置輪廓的寬度，例如 `outline-2` 表示 2px 寬度的輪廓。

```html
<div class="outline-2 outline-red-500">這段文字有 2px 寬度的紅色輪廓</div>
```
##### 輪廓顏色 (`outline-{color}`)
- 設置輪廓顏色。

```html
<div class="outline outline-blue-500">這段文字有藍色輪廓</div>
```
##### 輪廓樣式 (`outline-{style}`)：
- 設置輪廓樣式，如 `solid`、`dashed`、`dotted` 等。

```html
<div class="outline outline-dashed outline-green-500">這段文字有虛線輪廓</div>
```
### `ring` (環繞效果)
主要用於聚焦樣式，如按鈕或表單元素的聚焦環。`ring` 的顯示效果會疊加在邊框外部，並且有一個過渡效果，這樣可以創建流暢的 UI 反應。
#### 類別：
##### 環繞寬度 (`ring-{size}`)： 
- 設置環繞的寬度，`ring-2` 表示 2px 寬度的環繞效果。

```html
<div class="ring-2 ring-blue-500">這段文字有 2px 藍色環繞效果</div>
```
##### 環繞顏色 (`ring-{color}`)
- 設置環繞的顏色。

```html
<div class="ring ring-green-500">這段文字有綠色環繞效果</div>
```
##### 內環繞陰影 (`ring-offset-{size}`)
- 設置內環繞陰影的寬度，`ring-offset` 創造了在環繞效果與元素邊框之間的間距。

```html
<div class="ring-4 ring-red-500 ring-offset-2 ring-offset-blue-200">
  這段文字有 4px 寬度的紅色環繞效果，並有 2px 的偏移
</div>
```

### 總結
- **`border`（邊框）**：控制元素的邊框寬度、顏色和圓角，影響元素的尺寸和佈局。
- **`outline`（輪廓）**：不影響元素佈局的外輪廓，通常用於焦點狀態。
- **`ring`（環繞效果）**：為聚焦元素提供環繞效果，通常用於提升交互的可見性，顯示於元素外部。