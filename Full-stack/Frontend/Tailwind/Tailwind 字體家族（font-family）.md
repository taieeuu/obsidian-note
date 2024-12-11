---
Date: 2024-09-07
---
### 內建的字體家族類別
**`font-sans`**：

- 使用無襯線字體家族，適合現代化設計。
- 預設字體：`ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Helvetica, Arial, sans-serif`。

```html
<div class="font-sans">這段文字使用 sans-serif 字體家族。</div>
```

當使用了 `font-sans` ，`Tailwind` 會先自動抓取 `ui-sans-serif`，如果沒有則在往後，以此類推。

**`font-serif`**：

- 使用襯線字體家族，適合傳統、正式的設計風格。
- 預設字體：`ui-serif, Georgia, Cambria, Times New Roman, Times, serif`。

```html
<div class="font-serif">這段文字使用 serif 字體家族。</div>
```

**`font-mono`**：

- 使用等寬字體家族，常用於顯示代碼或技術文檔。
- 預設字體：`ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, Liberation Mono, Courier New, monospace`。

```html
<div class="font-mono">這段文字使用 monospace 字體家族。</div>
```
### 自定義字體家族
您可以通過修改 `tailwind.config.js` 來自定義字體家族。這可以讓您在專案中使用自定義字體，並且可以通過 Tailwind 的類別輕鬆應用。可以從 [Google Font](https://fonts.google.com/?query=Pacifico) 下載、使用 **Link** 的方式，進入 `Google Font` 的 `Get Embed Code` 或是 `Import` 的方式。
#### 在 `tailwind.config.js` 中添加自定義字體家族
```js
module.exports = {
  theme: {
    extend: {
      fontFamily: {
        paci: ['Pacifico'], // 自定義字體
      },
    },
  },
}
```

當然你也可以覆蓋目前有的 font 標籤。
#### 在 ./src/css/input.css 中添加 `import` 設定
```css
@import url('https://fonts.googleapis.com/css2?family=Pacifico&display=swap');

@tailwind base;
@tailwind components;
@tailwind utilities;
```




