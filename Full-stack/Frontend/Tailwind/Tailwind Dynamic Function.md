---
Date: 2024-09-08
---

### 類別
Dynamic Function 是一種非常強大的功能，允許根據特定的條件或參數動態生成樣式。這些動態函數包含 `matchBase`、`matchComponents`、`matchUtilities` 和 `matchVariant`，它們可以讓我們根據不同需求動態生成基礎樣式、元件樣式、工具類別或狀態變體。

- **`matchBase`**：動態生成 HTML 標籤的基礎樣式，適合針對標籤進行樣式控制。
- **`matchComponents`**：動態生成元件樣式，適合定義可重用的 UI 元件（如按鈕、卡片等）。
- **`matchUtilities`**：生成動態工具類別，適合生成單一屬性的樣式（如 `rotate`、`padding` 等）。
- **`matchVariant`**：創建動態變體，適合定義不同狀態下的自定義樣式（如 `hover`、`focus`、`active` 等）。
### Dynamic Function `matchComponents`
`matchComponents` 是 Tailwind 的一個 Dynamic Function，它允許我們根據主題設定的值，動態生成一組自定義的元件樣式。這非常適合應用於多個相似的元件，每個元件都有不同的尺寸、間距或顏色等屬性變化。
##### 範例
以下是 `matchComponents` 的範例，通過此函數動態生成不同大小的圓形元件：

```js
const plugin = require('tailwindcss/plugin')

module.exports = {
  content: [
    "./src/**/*.{html, js, jsx}",
  ],
  theme: {
    circleSizes: {
      sm: '3rem',
      md: '5rem',
      lg: '7rem',
      xl: '10rem',
    },
    extend: {},
  },
  plugins: [
    plugin(function({ matchComponents, theme }) {
      matchComponents(
        {
          circle: (value) => ({
            width: value,
            height: value,
            borderRadius: theme('borderRadius.full'), // 圓形
          }),
        },
        {
          values: theme('circleSizes'), // 使用主題中的 circleSizes 來生成變體
        }
      )
    })
  ],
}
```
##### 說明
###### 自定義 `circleSizes`
在 `theme` 中定義了 `circleSizes`，用來設置不同大小的圓形元件。這些尺寸通過 `rem` 值定義，並可以擴展或修改。

```js
circleSizes: {
   sm: '3rem',
   md: '5rem',
   lg: '7rem',
   xl: '10rem',
}
```

這裡我們設置了 4 種不同大小：小 (`sm`)、中 (`md`)、大 (`lg`) 和超大 (`xl`)。
###### `matchComponents` 動態生成元件樣式
- `circle`: 定義了每個圓形的寬度、高度和邊框圓角，透過 `theme('borderRadius.full')` 保證所有元件都是圓形。
    
- `values`: 使用 `theme('circleSizes')` 來定義元件的大小。這會根據 `circleSizes` 生成不同大小的圓形元件。

```js
matchComponents(
  {
    circle: (value) => ({
      width: value,
      height: value,
      borderRadius: theme('borderRadius.full'),
    }),
  },
  {
    values: theme('circleSizes')
  }
)
```

**動態生成結果**：
- `circle-sm` 會生成寬度和高度為 `3rem` 的圓形樣式。
- `circle-md` 則會生成寬度和高度為 `5rem` 的圓形樣式，依此類推。
### 在 HTML 中使用
```html
<div class="circle-sm bg-blue-500"></div>
<div class="circle-md bg-red-500"></div>
<div class="circle-lg bg-green-500"></div>
<div class="circle-xl bg-yellow-500"></div>
```

這些圓形元件會根據定義的大小顯示不同的尺寸，同時可以搭配其他 Tailwind 顏色類別或樣式類別使用。
### 小結
- **Dynamic Function `matchComponents`**：透過動態生成樣式，你可以根據變數或主題中的定義自動生成大量的變體樣式，這讓 Tailwind 具有更高的靈活性和可擴展性。
- **主題定義與樣式一致性**：通過使用 `theme()` 函數來引用主題設定，你可以確保生成的自定義樣式與 Tailwind 預設樣式系統保持一致。
- **減少重複樣式編寫**：使用動態方法可以生成多個變體，而不用手動編寫每個樣式，提升了開發效率。