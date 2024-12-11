---
Date: 2024-09-07
---
`Tailwind` 主要邊框樣式和線條效果：`border`（邊框）、`outline`（輪廓）、`ring`（環繞效果）、`divide`（分隔線），以及它們的不同應用和效果
### 1. `border` (邊框)
- **`border-4`**：這表示設置元素的邊框寬度為 4px。

```html
<div class="text-2xl border-4 m-4 p-3">Border - Lorem ipsum dolor sit amet.</div>
```
### 2. `outline` (輪廓)
- **`outline`**：表示設置元素的輪廓。與邊框不同，`outline` 不會影響元素的佈局，通常用來標記焦點。
- **`outline-4`**：設置輪廓寬度為 4px。

```html
<div class="text-2xl outline outline-4 m-4 p-3">Outline - Lorem ipsum dolor sit amet.</div>
```
### 3. `ring` (環繞效果)
- **`ring-4`**：表示設置元素的環繞效果（類似於焦點狀態下的環繞效果）寬度為 4px。

```html
<div class="text-2xl ring-4 m-4 p-3">Ring - Lorem ipsum dolor sit amet.</div>
```
### 4. `divide` (分隔線)
`divide` 用來在列表元素（如 `<ul>`）或行內元素（如 `<p>`）之間添加分隔線。您可以控制分隔線的方向（`x` 或 `y`）和寬度。
#### **`divide-y`**：設置垂直方向的分隔線
- **`divide-y-8`**：表示在列表項目之間設置 8px 的垂直分隔線。

```html
<ul class="divide-y-8">
  <li>item 1</li>
  <li>item 2</li>
  <li>item 3</li>
  <li>item 4</li>
</ul>
```
#### **`divide-x`**：設置水平方向的分隔線
- **`divide-x-4`**：表示在行內元素（`<span>`）之間設置 4px 的橫向分隔線。

```html
<p class="divide-x-4">
  <span class="ml-5 pl-5">item 1</span>
  <span class="ml-5 pl-5">item 2</span>
  <span class="ml-5 pl-5">item 3</span>
  <span class="ml-5 pl-5">item 4</span>
</p>
```
### 總結：
- **`border`**：設置元素的邊框寬度。
- **`outline`**：設置元素的輪廓，不影響佈局。
- **`ring`**：設置元素的環繞效果，通常用來表示焦點狀態。
- **`divide`**：用來在列表元素（垂直方向）或行內元素（水平方向）之間添加分隔線，控制分隔線的寬度。