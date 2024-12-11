---
Date: 2024-09-08
---
### 1. Self Alignment (自我對齊)
- 使用 `self-` 類別來為單個子項目設置特定的對齊方式：
    - **`self-start`**: 將子項目對齊到容器的起點。
    - **`self-center`**: 將子項目居中對齊。
    - **`self-end`**: 將子項目對齊到容器的終點。
    - **`self-auto`**: 使用自動對齊。

```html
<div class="flex flex-col">
  <div class="p-3 bg-red-200 self-start">01 - Lorem ipsum dolor</div>
  <div class="p-3 bg-blue-200 self-center">02 - Lorem ipsum dolor</div>
  <div class="p-3 bg-green-200 self-end">03 - Lorem ipsum dolor</div>
  <div class="p-3 bg-orange-200 self-auto">04 - Lorem ipsum dolor</div>
</div>
```

子項目使用 `self-start`、`self-center`、`self-end` 等類別來在容器內的不同位置對齊，`self-auto` 會依據其他設定自動對齊。
### 2. Flex Grow (彈性增長)
- 使用 `flex-grow` 來讓子項目根據可用空間擴展。
    - `flex-grow`: 將項目按比例增長以填滿剩餘空間。

```html
<div class="flex">
  <div class="p-3 bg-red-200">Home</div>
  <div class="p-3 bg-blue-200">Product</div>
  <div class="p-3 bg-green-200">Contact us</div>
  <div class="p-3 bg-orange-200 flex-grow text-right">Login</div>
</div>
```

`Login` 項目因為使用了 `flex-grow`，它會擴展以填滿剩餘的水平空間。
### 3. Flex Shrink (彈性縮小)
- 使用 `flex-shrink` 來控制子項目的縮小行為。
    - 預設值是 1，意味著子項目會縮小以適應空間。

```html
<div class="flex">
  <div class="p-3 bg-red-200 w-56 flex-shrink-0">Home</div>
  <div class="p-3 bg-blue-200 w-56">Product</div>
  <div class="p-3 bg-green-200 w-56">Contact us</div>
  <div class="p-3 bg-orange-200 w-56">Login</div>
</div>
```

`Home` 使用了 `flex-shrink-0`，因此當容器空間不足時，它不會縮小，而其他項目會縮小以適應容器。
### 4. Flex Basis (彈性基礎)
- 使用 `basis-` 類別來設置子項目的基礎寬度，這類似於設置 `width`。
    - 可以使用百分比、像素等來定義基礎寬度。

```html
<div class="flex">
  <div class="p-3 bg-red-200 basis-1/4">01</div>
  <div class="p-3 bg-blue-200 basis-1/4">02</div>
  <div class="p-3 bg-green-200 basis-1/4">03</div>
  <div class="p-3 bg-orange-200 basis-2/4">04</div>
</div>
```

每個子項目根據 `basis` 設置的比例分配寬度，最後一個項目占據一半的寬度。
### 5. Flex Order (彈性排序)
- 使用 `order-` 類別來更改子項目的顯示順序。
    - `order-first` 和 `order-last` 將項目顯示為第一或最後項目。
    - `order-[數字]` 來指定順序。

```html
<div class="flex">
  <div class="p-3 bg-red-200 order-3">01</div>
  <div class="p-3 bg-blue-200 order-4">02</div>
  <div class="p-3 bg-green-200 order-1">03</div>
  <div class="p-3 bg-orange-200 order-2">04</div>
</div>
```

使用 `order` 屬性更改子項目的順序，`03` 會排在最前面，`02` 排在最後面，其他項目則按順序排列。
### 6. 空間間距 (Space Between Element)
- 使用 `space-x-` 和 `space-y-` 類別來設置子項目之間的水平或垂直間距。

水平間距
```html
<div class="flex space-x-10">
  <div class="p-3 bg-red-200">01</div>
  <div class="p-3 bg-blue-200">02</div>
  <div class="p-3 bg-green-200">03</div>
  <div class="p-3 bg-orange-200">04</div>
</div>
```

每個子項目之間有 10px 的水平間距，子項目之間會被均等分開。

垂直間距
```html
<div class="flex flex-col space-y-10">
  <div class="p-3 bg-red-200">01</div>
  <div class="p-3 bg-blue-200">02</div>
  <div class="p-3 bg-green-200">03</div>
  <div class="p-3 bg-orange-200">04</div>
</div>
```

每個子項目之間有 10px 的垂直間距，子項目會從上到下排列。
### 7. 間距 Gap (Flex Gap)
- 使用 `gap-` 類別來設置子項目之間的間距，可以水平或垂直分開設置。

```html
<div class="flex gap-5 flex-wrap">
  <div class="p-3 bg-red-200">01</div>
  <div class="p-3 bg-blue-200">02</div>
  <div class="p-3 bg-green-200">03</div>
  <div class="p-3 bg-orange-200">04</div>
</div>
```

子項目之間有 5px 的間距，並且當空間不足時會自動換行。