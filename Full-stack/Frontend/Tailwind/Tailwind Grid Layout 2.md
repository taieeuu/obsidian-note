---
Date: 2024-09-08
---
### 1. 水平對齊 (Horizontal Alignment)
- 使用 `justify-items-*` 類別來控制每個子項目的水平方向對齊：
    - **`justify-items-start`**: 將項目對齊到容器的起點。
    - **`justify-items-center`**: 將項目居中對齊。
    - **`justify-items-end`**: 將項目對齊到容器的終點。
    - **`justify-items-stretch`**: 預設，將項目拉伸填滿容器寬度。

```html
<ul class="grid grid-cols-3 justify-items-start">
    <li class="grider bg-red-300">01</li>
    <li class="grider bg-blue-300">02</li>
    <li class="grider bg-green-500">03</li>
    <li class="grider bg-purple-300">04</li>
    <li class="grider bg-orange-300">05</li>
    <li class="grider bg-zinc-300">06</li>
</ul>
```

所有子項目在水平方向上對齊到容器的左側。

---
### 2. 垂直對齊 (Vertical Alignment)
- 使用 `items-*` 類別來控制每個子項目的垂直對齊方式：
    - **`items-start`**: 將項目對齊到容器的頂部。
    - **`items-center`**: 將項目在容器內居中對齊。
    - **`items-end`**: 將項目對齊到容器的底部。
    - **`items-stretch`**: 預設，項目會拉伸填滿容器的高度。

```html
<ul class="grid grid-cols-3 min-h-screen items-start">
    <li class="grider bg-red-300">01</li>
    <li class="grider bg-blue-300">02</li>
    <li class="grider bg-green-500">03</li>
    <li class="grider bg-purple-300">04</li>
    <li class="grider bg-orange-300">05</li>
    <li class="grider bg-zinc-300">06</li>
</ul>
```

子項目在垂直方向對齊到容器的頂部，並且容器的最小高度設為 `min-h-screen`。

---
### 3. 內容對齊 (Content Alignment)
- 使用 `content-*` 類別來控制整體內容在容器中的對齊方式：
    - **`content-start`**: 將內容對齊到容器的起點。
    - **`content-center`**: 將內容在容器內居中對齊。
    - **`content-end`**: 將內容對齊到容器的終點。
    - **`content-between`**: 將內容等距排列，首尾無間距。
    - **`content-around`**: 將內容等距排列，首尾有間距。
    - **`content-evenly`**: 將內容均勻地分佈在容器內。

```html
<ul class="grid min-h-screen grid-cols-3 content-start">
  <li class="grider bg-red-300">01</li>
  <li class="grider bg-blue-300">02</li>
  <li class="grider bg-green-500">03</li>
  <li class="grider bg-purple-300">04</li>
  <li class="grider bg-orange-300">05</li>
  <li class="grider bg-zinc-300">06</li>
</ul>
```

這段程式碼會將所有內容對齊到網格容器的頂端。

---
### 4. Place Items (項目對齊)
- 使用 `place-items-*` 同時控制子項目的水平方向和垂直方向的對齊：
    - **`place-items-start`**: 項目水平方向和垂直方向對齊到起點。
    - **`place-items-center`**: 項目在水平方向和垂直方向上都居中。
    - **`place-items-end`**: 項目水平方向和垂直方向對齊到終點。

```html
<ul class="grid grid-cols-3 min-h-screen place-items-stretch">
    <li class="grider bg-red-300">01</li>
    <li class="grider bg-blue-300">02</li>
    <li class="grider bg-green-500">03</li>
    <li class="grider bg-purple-300">04</li>
    <li class="grider bg-orange-300">05</li>
    <li class="grider bg-zinc-300">06</li>
</ul>
```

子項目會同時在水平方向和垂直方向上拉伸，以填滿網格容器。

---
### 5. Place Content (內容排列)
- 使用 `place-content-*` 同時控制整體內容的水平方向和垂直方向排列：
    - **`place-content-start`**: 內容從容器的起點開始。
    - **`place-content-center`**: 內容居中排列。
    - **`place-content-end`**: 內容排列到容器的末端。
    - **`place-content-between`**: 內容之間的距離平均分配。
      
```html
<ul class="grid grid-cols-3 min-h-screen place-content-stretch">
    <li class="grider bg-red-300">01</li>
    <li class="grider bg-blue-300">02</li>
    <li class="grider bg-green-500">03</li>
    <li class="grider bg-purple-300">04</li>
    <li class="grider bg-orange-300">05</li>
    <li class="grider bg-zinc-300">06</li>
</ul>
```

所有內容會在垂直和水平方向上被拉伸，填滿整個網格容器。

---
### 6. Aligning Individual Item (對齊單個子項目)
- 使用 `self-*`、`justify-self-*` 和 `place-self-*` 來獨立控制某一個子項目的對齊：
    - **`self-*`**: 控制單個子項目的垂直對齊。
    - **`justify-self-*`**: 控制單個子項目的水平方向對齊。
    - **`place-self-*`**: 同時控制單個子項目的水平方向和垂直方向對齊。

```html
<ul class="grid grid-cols-3 min-h-screen">
    <li class="grider bg-red-300 self-end">01</li>
    <li class="grider bg-blue-300 justify-self-end">02</li>
    <li class="grider bg-green-500 place-self-end">03</li>
    <li class="grider bg-purple-300 place-self-start justify-self-end">04</li>
    <li class="grider bg-orange-300 place-self-end justify-self-start">05</li>
    <li class="grider bg-zinc-300">06</li>
    <li class="grider bg-yellow-300">07</li>
    <li class="grider bg-cyan-300">08</li>
    <li class="grider bg-orange-200">09</li>
</ul>
```

各個子項目使用不同的對齊方式進行水平方向和垂直方向的對齊控制，具體的對齊方式可以根據需要獨立設定。