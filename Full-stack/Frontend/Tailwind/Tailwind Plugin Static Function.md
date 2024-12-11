---
Date: 2024-09-08
---
插件是用來擴充功能和自定義設計系統的一種方式。`static` 函數用於創建靜態值的插件，這些值可以是 CSS 屬性的自定義組合。通過 `addUtilities`、`addComponents` 或 `addBase` 方法，我們可以將這些靜態值加入 Tailwind 的樣式表中。

---
### 1. `addBase`
`addBase` 用於自定義基礎樣式，這些樣式通常應用於 HTML 標籤（例如標題 `<h1>`、`<h2>`）。在此範例中，設定了標題的字體大小。

```js
addBase({
  'h1': { fontSize: '35px' }, // <h1> 的字體大小為 35px
  'h2': { fontSize: '25px' }  // <h2> 的字體大小為 25px
});
```

快速定義標題標籤的預設樣式，而不需要每次都在 HTML 中手動調整。

---
### 2. `addComponents`
`addComponents` 用於定義可重用的元件樣式。在此範例中，我們定義了一個按鈕 (`.btn`) 的樣式：

```js
addComponents({
  '.btn': {
    padding: '8px 16px',        // 按鈕內邊距
    borderRadius: '7px',        // 按鈕圓角
    borderWidth: '2px',         // 按鈕邊框寬度
  }
});
```

在專案中隨處使用 `.btn` 類別，並且保持一致的樣式設定。只需在 HTML 中加上 `.btn` 類別，即可應用這些樣式。

---
### 3. 使用方式
在專案中，您可以這樣使用定義好的樣式：

```html
<h1>這是一個標題</h1>
<h2>這是副標題</h2>

<button class="btn">按鈕</button>
```
### 4. 配置檔案說明
- `content`: 定義 Tailwind 應用樣式的檔案範圍，這樣 Tailwind 只會掃描指定範圍內的檔案來應用樣式。
- `theme.extend`: 如果有需要擴充主題的部分，可以在此區塊添加自定義的設定，例如新增顏色、間距等。
### 整體的tailwind.config.js
```js
const plugin = require('tailwindcss/plugin')
module.exports = {
    content: [
      "./src/**/*.{html, js, jsx}",
    ],
    theme: {
      extend: {
      },
    },
    plugins: [
      plugin(function({addBase, addComponents, addUtilities}){
        addBase({
          'h1':{fontSize:'35px'},
          'h2':{fontSize: '25px'}
        });
        addComponents({
          '.btn': {
            padding: '8px 16px',
            borderRadius:'7px',
            borderWidth:'2px'
          }
        })
      })
    ],
  }
```

