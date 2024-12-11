---
Date: 2024-09-07
---
### 基礎按鈕樣式
```html
<button class="bg-green-600 px-5 py-2 rounded-lg text-white">Plain</button>
```

這是一個基本按鈕，設置了背景顏色、內邊距、圓角和文字顏色。沒有應用任何交互狀態。
### 1. `hover` 修飾符
```html
<button class="bg-green-600 px-5 py-2 rounded-lg text-white hover:bg-green-400">Hover </button>
```

當使用者將滑鼠懸停在按鈕上時，**`hover:bg-green-400`** 會改變按鈕的背景顏色。`hover` 修飾符讓元素能夠在滑鼠懸停時發生變化。
### 2. `focus` 修飾符
```html
<button class="bg-green-600 px-5 py-2 rounded-lg text-white focus:border-2 focus:border-red-800">Focus</button>
```

當按鈕獲得焦點時（例如被選中或按下 Tab 鍵），`focus` 修飾符將邊框顏色設置為紅色，並增加 2px 的邊框厚度。這有助於讓使用者知道元素已被選中。
### 3. `active` 修飾符
```html
<button class="bg-green-600 px-5 py-2 rounded-lg text-white active:font-bold">Active</button>
```

按鈕在被按下時應用 **`active:font-bold`**，這會將字體加粗，為使用者提供視覺上的反饋。
### 4. 組合修飾符
```html
<button class="bg-green-600 px-5 py-2 rounded-lg text-white hover:bg-red-300 focus:text-red-200 active:border-2 active:border-green-300">Combine</button>
```

這裡展示了多個狀態下的組合效果：

- **`hover:bg-red-300`**：滑鼠懸停時，背景顏色變為紅色。
- **`focus:text-red-200`**：當按鈕獲得焦點時，文字顏色變為紅色。
- **`active:border-2`** 和 **`active:border-green-300`**：按鈕被點擊時，增加 2px 的邊框並將其設置為綠色。
### 5. `group` 修飾符
```html
<div class="group border-2 border-red-600 m-5 p-3 rounded-xl bg-red-100">
  <div class="text-2xl font-bold group-hover:text-red-800">My Heading</div>
</div>
```

**`group`** 允許您將子元素的交互狀態與父元素同步。在這裡，當父元素應用 **`group`** 並被懸停時，子元素的文字顏色會變為紅色。
