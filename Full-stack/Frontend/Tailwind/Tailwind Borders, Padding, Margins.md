---
Date: 2024-09-07
---
### 1. 邊框 (Borders)
Tailwind 提供了一個靈活的 `border` 工具來設置元素的邊框寬度、顏色和樣式。
#### `border-{side}-{size}` 格式：
- **`{size}`**: 邊框的寬度。這可以是 `0`, `1`, `2`, `4`, `8`，表示邊框的像素值。
- **`{side}`**: 這個部分指定邊框應該應用在哪一邊。Tailwind 使用不同的縮寫來表示不同的邊。
	- **`t`**: top（上邊框）
	- **`r`**: right（右邊框）
	- **`b`**: bottom（下邊框）
	- **`l`**: left（左邊框）
	- **`x`**: 橫向邊（左右兩邊）
	- **`y`**: 縱向邊（上下兩邊）
	- **`s`**: start（根據書寫方向的起始邊）
	- **`e`**: end（根據書寫方向的結束邊）

```html
<div class="text-3xl border-8">這段文字有 8px 寬的邊框</div>
```
### 2. 填充 (Padding)
`padding` 用來控制元素內容與邊框之間的內部空間。Tailwind 提供了多種尺寸選擇來靈活調整間距。
#### `p{side}-{size}` 格式：
- **`p`**：基礎類別，用來設置元素的內填充（padding）。
- **`{side}`**：可選部分，指定要應用填充的方向或位置。如果不指定，則應用到四個方向。
    - **`t`**：top（上填充）
    - **`r`**：right（右填充）
    - **`b`**：bottom（下填充）
    - **`l`**：left（左填充）
    - **`x`**：水平方向（左右填充）
    - **`y`**：垂直方向（上下填充）
    - **`s`**：start（根據書寫方向的起始邊填充）
    - **`e`**：end（根據書寫方向的結束邊填充）
- **`{size}`**：設置填充的大小。可選的大小範圍包括：
    - **`0`**：無填充
    - **`px`**：1px 填充
    - **`0.5`**：0.125rem 填充
    - **`1`**：0.25rem 填充
    - **`2`**：0.5rem 填充
    - **`4`**：1rem 填充
    - **`8`**：2rem 填充
    - **`96`**：24rem 填充（最大值）

```html
<div class="text-3xl border-4 pr-4">這段文字有 4px 的右側填充</div>
<div class="text-3xl border-4 p-px">這段文字有 1px 的填充</div>
```
### 3. 外邊距 (Margin)
`margin` 用來控制元素與其他元素之間的外部間距。Tailwind 中的 `margin` 類別非常靈活，可以應用到單邊或四邊。
#### `m{side}-{size}` 格式解釋
- **`m`**：基礎類別，用來設置元素的外邊距（margin），可以應用於所有方向或特定方向。
- **`{side}`**：可選部分，指定要應用邊距的方向。如果不指定，則應用到所有四個邊。
    - **`t`**：top（上邊距）
    - **`r`**：right（右邊距）
    - **`b`**：bottom（下邊距）
    - **`l`**：left（左邊距）
    - **`x`**：水平方向（左右兩邊的邊距）
    - **`y`**：垂直方向（上下兩邊的邊距）
    - **`s`**：start（根據文字書寫方向的起始邊）
    - **`e`**：end（根據文字書寫方向的結束邊）
    - **`auto`**：自動設置邊距（常用來居中元素）
    - **`px`**：1px 的邊距（精確控制）
- **`{size}`**：表示邊距的大小。可選的大小範圍包括：
    - **`0`**：無邊距
    - **`px`**：1px 邊距
    - **`0.5`**：0.125rem 邊距
    - **`1`**：0.25rem 邊距
    - **`2`**：0.5rem 邊距
    - **`4`**：1rem 邊距
    - **`8`**：2rem 邊距
    - **`96`**：24rem 邊距（最大值）

```html
<div class="text-3xl border-4 mb-3">這段文字有 3px 的底部邊距</div>
<div class="text-3xl border-4 m-2">這段文字有 2px 的外邊距</div>
<div class="text-3xl border-4 m-px">這段文字有 1px 的外邊距</div>
```