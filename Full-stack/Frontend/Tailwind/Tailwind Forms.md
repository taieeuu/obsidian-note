---
Date: 2024-09-08
---
### 1. 文本框 (Input Fields)
文本框是表單中最常見的元素，可以通過 `border`、`rounded`、`p` 等 Tailwind 類別來設置外觀。
##### 範例：單行文本框
```html
<input type="text" placeholder="輸入文字" class="border border-gray-300 rounded-lg p-2 focus:outline-none focus:ring-2 focus:ring-blue-500">
```

- `border`：設置邊框。
- `rounded-lg`：設置圓角。
- `p-2`：設置內邊距（padding）。
- `focus:ring-2` 和 `focus:ring-blue-500`：在獲得焦點時顯示藍色的邊框光圈。
##### 範例：多行文本框 (Textarea)
```html
<textarea placeholder="輸入更多文字" class="border border-gray-300 rounded-lg p-3 focus:outline-none focus:ring-2 focus:ring-blue-500"></textarea>
```

這是一個多行的文本框，應用了與單行文本框類似的樣式。

---
### 2. 選擇框 (Select Menu)
選擇框可以使用 `select` 元素，並應用 Tailwind 的類別來美化。

```html
<select class="border border-gray-300 rounded-lg p-2 focus:outline-none focus:ring-2 focus:ring-blue-500">
  <option>選項 1</option>
  <option>選項 2</option>
  <option>選項 3</option>
</select>
```

這個選擇框設置了灰色邊框、圓角和內邊距，並且在獲得焦點時顯示藍色光圈。

---
### 3. 單選按鈕 (Radio Buttons)
單選按鈕可以用 `radio` 類型的 `input` 元素來創建，並可以通過 `form-radio` 類別來美化。

```html
<label class="inline-flex items-center">
  <input type="radio" class="form-radio text-blue-500" name="radio" value="1">
  <span class="ml-2">選項 1</span>
</label>
<label class="inline-flex items-center">
  <input type="radio" class="form-radio text-blue-500" name="radio" value="2">
  <span class="ml-2">選項 2</span>
</label>
```

`form-radio` 類別用來美化單選按鈕，`text-blue-500` 指定選中後按鈕的顏色。

---
### 4. 複選框 (Checkboxes)
複選框可以通過 `checkbox` 類型的 `input` 元素來創建，並使用 `form-checkbox` 類別來美化。

```html
<label class="inline-flex items-center">
  <input type="checkbox" class="form-checkbox text-blue-500">
  <span class="ml-2">選項 1</span>
</label>
<label class="inline-flex items-center">
  <input type="checkbox" class="form-checkbox text-blue-500">
  <span class="ml-2">選項 2</span>
</label>
```

`form-checkbox` 類別用來美化複選框，並設置選中後的顏色為藍色。

---
### 5. 按鈕 (Buttons)
按鈕可以使用 `button` 元素，並通過 `bg`、`text`、`p` 等 Tailwind 類別來自定義其外觀。

```html
<button class="bg-blue-500 text-white font-bold py-2 px-4 rounded hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500">
  提交
</button>
```

- `bg-blue-500`：設置按鈕的背景顏色。
- `text-white`：設置按鈕文字的顏色。
- `font-bold`：設置加粗文字。
- `py-2` 和 `px-4`：設置垂直和水平的內邊距。
- `hover:bg-blue-700`：滑鼠懸停時背景顏色變深。
- `focus:ring-2`：當按鈕獲得焦點時顯示藍色光圈。
### 6. 表單分組 (Form Groups)
為了結構化表單，可以使用 Tailwind 的 `flex` 或 `grid` 系列類別來將表單元素進行分組和佈局。
##### 範例：表單分組
```html
<div class="grid grid-cols-1 gap-6">
  <div>
    <label class="block text-sm font-medium text-gray-700">用戶名</label>
    <input type="text" class="mt-1 block w-full border border-gray-300 rounded-lg p-2">
  </div>
  <div>
    <label class="block text-sm font-medium text-gray-700">密碼</label>
    <input type="password" class="mt-1 block w-full border border-gray-300 rounded-lg p-2">
  </div>
</div>
```

- `grid grid-cols-1`：將表單元素佈局為一列。
- `gap-6`：設置每個表單元素之間的間距。
### 7. 錯誤提示 (Validation States)
表單驗證時，可以使用顏色提示用戶錯誤或正確的輸入狀態。
##### 範例：錯誤輸入
```html
<input type="text" class="border border-red-500 rounded-lg p-2 focus:outline-none focus:ring-2 focus:ring-red-500">
<p class="text-red-500 text-sm mt-1">這是一個錯誤的提示</p>
```

當表單輸入錯誤時，紅色的邊框和錯誤提示文字可以提醒用戶。

