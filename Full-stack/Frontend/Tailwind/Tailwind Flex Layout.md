---
Date: 2024-09-08
---
**Flexbox** 用來在容器內部管理元素的排列和對齊方式。 **Tailwind CSS** 通過一系列簡潔的 `flex` 類別，讓我們可以快速實現靈活且響應式的佈局。
### 1. 啟用 Flex 布局
- 要啟用 **Flexbox**，可以使用 `flex` 類別，讓容器中的子項目成為彈性項目。
  
```html
<div class="flex">
  <div class="bg-red-200 p-2">Item 1</div>
  <div class="bg-blue-200 p-2">Item 2</div>
  <div class="bg-green-200 p-2">Item 3</div>
</div>
```
### 2. 主軸方向 (Flex Direction)
- **`flex-row`**: 橫向排列 (預設)。
- **`flex-row-reverse`**: 反向橫向排列。
- **`flex-col`**: 縱向排列。
- **`flex-col-reverse`**: 反向縱向排列。

```html
<div class="flex flex-row">
  <div class="bg-red-200 p-2">Item 1</div>
  <div class="bg-blue-200 p-2">Item 2</div>
  <div class="bg-green-200 p-2">Item 3</div>
</div>
```
### 3. 彈性子項目換行 (Flex Wrap)
- **`flex-wrap`**: 允許子項目換行。
- **`flex-nowrap`**: 預設值，不允許子項目換行。
- **`flex-wrap-reverse`**: 反向換行。

```html
<div class="flex flex-wrap">
  <div class="bg-red-200 p-2 w-1/3">Item 1</div>
  <div class="bg-blue-200 p-2 w-1/3">Item 2</div>
  <div class="bg-green-200 p-2 w-1/3">Item 3</div>
  <div class="bg-orange-200 p-2 w-1/3">Item 4</div>
</div>
```
### 4. 主軸對齊 (Justify Content)
- 控制彈性子項目在主軸上的對齊方式：
    - **`justify-start`**: 靠左對齊 (預設)。
    - **`justify-center`**: 置中排列。
    - **`justify-end`**: 靠右對齊。
    - **`justify-between`**: 子項目間距均等，首尾無間距。
    - **`justify-around`**: 子項目間距均等，首尾也有間距。
    - **`justify-evenly`**: 子項目間距完全均等。

```html
<div class="flex justify-center">
  <div class="bg-red-200 p-2">Item 1</div>
  <div class="bg-blue-200 p-2">Item 2</div>
  <div class="bg-green-200 p-2">Item 3</div>
</div>
```
### 5. 交叉軸對齊 (Align Items)
- 控制彈性子項目在交叉軸上的對齊方式：
    - **`items-start`**: 向交叉軸起點對齊。
    - **`items-center`**: 交叉軸居中。
    - **`items-end`**: 向交叉軸終點對齊。
    - **`items-baseline`**: 基線對齊。
    - **`items-stretch`**: 預設值，子項目拉伸填滿容器高度。

```html
<div class="flex items-center h-24">
  <div class="bg-red-200 p-2">Item 1</div>
  <div class="bg-blue-200 p-2">Item 2</div>
  <div class="bg-green-200 p-2">Item 3</div>
</div>
```
### 6. 單個子項目對齊 (Align Self)
- 針對單一子項目設定對齊方式：
    - **`self-start`**: 向起點對齊。
    - **`self-center`**: 居中對齊。
    - **`self-end`**: 向終點對齊。
    - **`self-stretch`**: 拉伸填滿容器高度。

```html
<div class="flex">
  <div class="self-start bg-red-200 p-2">Item 1</div>
  <div class="self-center bg-blue-200 p-2">Item 2</div>
  <div class="self-end bg-green-200 p-2">Item 3</div>
</div>
```
### 7. 內容對齊 (Align Content)
- 控制多行內容在容器中的對齊方式：
    - **`content-start`**: 向上對齊。
    - **`content-center`**: 居中對齊。
    - **`content-end`**: 向下對齊。
    - **`content-between`**: 行間距均等，首尾無間距。
    - **`content-around`**: 行間距均等，首尾有間距。
    - **`content-evenly`**: 行間距完全均等。
      
```html
<div class="flex flex-wrap content-center h-64">
  <div class="bg-red-200 p-2 w-1/4">Item 1</div>
  <div class="bg-blue-200 p-2 w-1/4">Item 2</div>
  <div class="bg-green-200 p-2 w-1/4">Item 3</div>
  <div class="bg-orange-200 p-2 w-1/4">Item 4</div>
</div>
```
### 8. 響應式 Flexbox
- 可以根據螢幕大小改變佈局。例如在小螢幕使用 `flex-col`，大螢幕使用 `flex-row`

```html
<div class="flex flex-col md:flex-row">
  <div class="bg-red-200 p-2">Item 1</div>
  <div class="bg-blue-200 p-2">Item 2</div>
  <div class="bg-green-200 p-2">Item 3</div>
</div>
```