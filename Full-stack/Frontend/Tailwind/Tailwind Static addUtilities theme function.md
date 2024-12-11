---
Date: 2024-09-08
---
`addUtilities` 用來添加自定義的實用工具類別（Utility Classes）。它允許開發者在 Tailwind 的基礎上擴展新的樣式，這些樣式可以是簡單的單一屬性，也可以是複雜的樣式組合。

`addUtilities` 是靜態的，這意味著你可以在 `tailwind.config.js` 中明確定義具體的樣式，並將它們直接應用在項目中。

---
### 使用範例
假設我們想要添加一些自定義的背景漸層和邊框光暈樣式，以下是如何使用 `addUtilities` 動態生成這些樣式的範例：

基本範例
```js
const plugin = require('tailwindcss/plugin');

module.exports = {
  plugins: [
    plugin(function({ addUtilities, theme }) {
      const newUtilities = {
        '.bg-gradient-orange': {
          backgroundImage: `linear-gradient(to right, ${theme('colors.orange.300')}, ${theme('colors.orange.500')})`,
          color: 'white',
        },
        '.border-orange-glow': {
          boxShadow: `0 0 5px ${theme('colors.orange.500')}, 0 0 10px ${theme('colors.orange.100')}`,
        },
      };

      addUtilities(newUtilities, ['responsive', 'hover']);
    }),
  ],
};

```

解析

1. 在 `newUtilities` 中，我們定義了兩個自定義樣式：
	- **`.bg-gradient-orange`**：這是一個背景漸層，從 `orange.300` 到 `orange.500`，並將文字顏色設為白色。
	- **`.border-orange-glow`**：這是一個邊框陰影樣式，使用了兩個不同大小的陰影來模擬光暈效果。
	
2. 使用 `theme()`: 使用 `theme()` 函數從 Tailwind 的主題配置中動態取得顏色值（如 `orange.300` 和 `orange.500`）。這樣可以確保自定義樣式與 Tailwind 的整體設計系統保持一致性。
   
3. 應用 `addUtilities` 最後，我們使用 `addUtilities` 方法來添加這些自定義樣式，並且透過 `['responsive', 'hover']` 參數來啟用響應式和 `hover` 狀態的變體。
	- **`responsive`**：這將生成響應式版本的這些樣式，讓你在不同的螢幕寬度下使用（如 `md:bg-gradient-orange`）。
	- **`hover`**：這將生成 `hover` 狀態的版本，讓你可以在滑鼠懸停時應用這些樣式（如 `hover:border-orange-glow`）。
	  
### 在 HTML 中使用
定義完自定義樣式後，我們就可以在 HTML 中使用這些類別：

```html
<div class="bg-gradient-orange p-4">
  這是橙色漸層背景的區塊
</div>

<div class="border-orange-glow p-4">
  這是橙色光暈邊框的區塊
</div>
```