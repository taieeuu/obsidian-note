---
Date: 2024-09-08
---
### 1. Grid Columns Layout (網格列佈局)
- 使用 `grid-cols-` 類別來定義網格的列數：
    - **`grid-cols-2`**: 將網格分為兩列。
    - **`grid-cols-3`**: 將網格分為三列。
    - **`grid-cols-4`**: 將網格分為四列。

```html
<div class="grid grid-cols-3">
  <div class="bg-red-200 p-4">Item 1</div>
  <div class="bg-blue-200 p-4">Item 2</div>
  <div class="bg-green-200 p-4">Item 3</div>
</div>
```

將網格分成三列，所有的子項目會自動填滿這些列。

---
### 2. Responsive Grid (響應式網格)
- 使用響應式的 `grid-cols-` 類別來根據螢幕大小調整列數：
    - **`sm:grid-cols-2`**: 小螢幕時顯示兩列。
    - **`md:grid-cols-3`**: 中等螢幕時顯示三列。
    - **`lg:grid-cols-4`**: 大螢幕時顯示四列。

```html
<div class="grid sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4">
  <div class="bg-red-200 p-4">Item 1</div>
  <div class="bg-blue-200 p-4">Item 2</div>
  <div class="bg-green-200 p-4">Item 3</div>
  <div class="bg-yellow-200 p-4">Item 4</div>
</div>
```

根據螢幕大小動態調整列數，小螢幕顯示兩列，中等螢幕顯示三列，大螢幕顯示四列。

---
### 3. Grid Rows Layout (網格行佈局)
- 使用 `grid-rows-` 類別來定義網格的行數：
    - **`grid-rows-2`**: 將網格分成兩行。
    - **`grid-rows-3`**: 將網格分成三行。

```html
<div class="grid grid-rows-2 grid-flow-col">
  <div class="bg-red-200 p-4">Item 1</div>
  <div class="bg-blue-200 p-4">Item 2</div>
  <div class="bg-green-200 p-4">Item 3</div>
  <div class="bg-yellow-200 p-4">Item 4</div>
</div>
```

將網格分成兩行，並且使用 `grid-flow-col` 讓子項目按照列的順序排列。

---
### 4. Column/Row Span (列/行跨距)
- 使用 `col-span-` 和 `row-span-` 來讓子項目跨越多個列或行：
    - **`col-span-2`**: 子項目橫跨兩個列。
    - **`row-span-2`**: 子項目橫跨兩個行。

```html
<div class="grid grid-cols-4 gap-4">
  <div class="bg-red-200 p-4 col-span-2">Item 1</div>
  <div class="bg-blue-200 p-4">Item 2</div>
  <div class="bg-green-200 p-4">Item 3</div>
  <div class="bg-yellow-200 p-4 row-span-2">Item 4</div>
</div>
```

讓 `Item 1` 橫跨兩個列，`Item 4` 則跨越兩個行，實現更靈活的網格佈局。

---
### 5. Gap Spacing (間距)
- 使用 `gap-` 類別來設定子項目之間的間距：
    - **`gap-4`**: 水平和垂直間距皆為 16px。
    - **`gap-x-4`**: 設定水平間距為 16px。
    - **`gap-y-4`**: 設定垂直間距為 16px。

```html
<div class="grid grid-cols-3 gap-4">
  <div class="bg-red-200 p-4">Item 1</div>
  <div class="bg-blue-200 p-4">Item 2</div>
  <div class="bg-green-200 p-4">Item 3</div>
</div>
```

使用 `gap-4` 設定了網格項目之間 16px 的間距，讓佈局更加美觀。

---
### 6. Start/End Positioning (列開始/結束位置)
- 使用 `col-start-` 和 `col-end-` 來設定子項目在哪一列開始或結束：
    - **`col-start-2`**: 子項目從第二列開始。
    - **`col-end-4`**: 子項目在第四列結束。

```html
<div class="grid grid-cols-4">
  <div class="bg-red-200 p-4 col-start-2 col-span-2">Item 1</div>
  <div class="bg-blue-200 p-4 col-end-4">Item 2</div>
  <div class="bg-green-200 p-4">Item 3</div>
</div>
```

讓 `Item 1` 從第二列開始並橫跨兩列，`Item 2` 則在第四列結束。

---
### 7. Grid Auto Flow (網格自動排列)
- 使用 `grid-flow-` 類別來控制網格子項目的自動排列方式：
    - **`grid-flow-row`**: 預設，子項目會按行排列。
    - **`grid-flow-col`**: 子項目會按列排列。

```html
<ul class="grid grid-cols-3 grid-flow-col">
  <li class="grider bg-red-300">01</li>
  <li class="grider bg-blue-300">02</li>
  <li class="grider bg-green-500">03</li>
  <li class="grider bg-purple-300">04</li>
  <li class="grider bg-orange-300">05</li>
  <li class="grider bg-zinc-300">06</li>
  <li class="grider bg-yellow-300">07</li>
  <li class="grider bg-cyan-300">08</li>
  <li class="grider bg-orange-200">09</li>
</ul>
```

使用 `grid-flow-col` 讓子項目按列自動排列，當一列的空間用完後才換行。

---
### 8. 密集網格排列 (Dense Grid Layout)
- 使用 `grid-flow-dense` 類別，讓網格項目更加緊密地排列，避免空隙：
    - **`grid-flow-dense`**: 會讓網格自動排列時填滿空隙，使布局更加緊密。

```html
<ul class="grid grid-cols-3 grid-flow-dense">
  <li class="grider bg-red-300 col-span-2">01</li>
  <li class="grider bg-blue-300 col-span-2">02</li>
  <li class="grider bg-green-500 row-span-3">03</li>
  <li class="grider bg-purple-300">04</li>
  <li class="grider bg-orange-300">05</li>
  <li class="grider bg-zinc-300">06</li>
  <li class="grider bg-yellow-300">07</li>
  <li class="grider bg-cyan-300">08</li>
  <li class="grider bg-orange-200">09</li>
</ul>
```

使用 `grid-flow-dense` 可以讓項目排列時填補空隙，項目會盡可能緊密排列，以避免浪費空間。