---
Date: 2024-09-07
---
## 安裝
我們可以根據 [官方文件](https://tailwindcss.com/docs/installation) 的說明來安裝 `Tailwind`。
### 步驟 1：安裝 Tailwind CSS
在專案的根目錄中，使用以下命令安裝 `Tailwind CSS` 及其依賴。

```sh
npm install -D tailwindcss
```

`-D` 表示將 `Tailwind CSS` 安裝為開發依賴，這樣在部署時不會包含它。
### 步驟 2：初始化 Tailwind 配置
安裝完成後，執行以下命令來生成一個預設的 `tailwind.config.js` 文件。

```sh
npx tailwindcss init
```
### 步驟 3：配置 `content` 路徑
為了讓 Tailwind 正確運作，您需要在 `content` 中指定 Tailwind CSS 要掃描的檔案範圍，以便根據實際使用的類別來生成最小的 CSS 文件。範例如下：

```js
module.exports = {
  content: [
    "./src/**/*.{html,js,jsx,ts,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

- `content`: 這裡的配置會告訴 Tailwind 掃描 `src` 資料夾中的所有 `.html`、`.js`、`.jsx`、`.ts`、`.tsx` 文件，並根據使用的 Tailwind 類別來生成對應的樣式。
- `theme`: 此部分用於定制 Tailwind 的設計系統，如顏色、間距、字體等。
- `plugins`: 可以在這裡添加額外的插件來擴展 Tailwind 的功能。
### 步驟 4：在樣式文件中引入 Tailwind
在您的樣式文件中，引入 Tailwind 的基礎樣式：

```js
@tailwind base; /* html tags: ul, div, span, h1, h2 etc */
@tailwind components;
@tailwind utilities;
```

**參數說明**
- `@tailwind base;` : 這部分包括了瀏覽器預設樣式的重置與全局的基本樣式（例如 `ul`、`div`、`span`、`h1`、`h2` 等 HTML 標籤的初始樣式）。

- `@tailwind components;` : 這部分包含 Tailwind 預定義的可重複使用的組件樣式。例如，表單、按鈕、卡片等組件的樣式可以在這裡進行設定。如果您使用 Tailwind 的插件或自定義組件，這些組件樣式也會在這裡被引入。

- `@tailwind utilities;` : 這部分提供大量的功能性類別，讓您可以透過使用簡單的類別名稱來快速設計網頁樣式。這些類別多為原子化的設計，專注於單一功能，例如字體大小、間距、顏色等。這是 Tailwind 的核心特色，使得設計過程非常靈活和高效。
### 步驟 5：運行 Tailwind
最後，在開發過程中，您可以使用以下命令來生成 Tailwind CSS：

```sh
npx tailwindcss -i ./src/input.css -o ./dist/output.css
```

這條命令用來編譯 Tailwind CSS 的樣式文件，並生成最終的 CSS 輸出文件，載入至 `./dist/output.css` ，使開發者能夠使用 css 。

**注意** : 當在 html 中使用 `Tailwind css` 時， 需要查看樣式，都要記得編譯一次 `Tailwind css` ，讓 `output.css` 的內容重新的加載。

由於每次都要加載實在太麻煩了，所以我們可以使用 `--watch` 參數，讓他自動偵測是否有變動。如下命令。

```sh
npx tailwindcss -i ./src/input.css -o ./dist/output.css --watch
```

這條命令會監視您的 `input.css` 檔案，並且在每次修改後生成對應的 `output.css`。

### 步驟 6：HTML加載 CSS
```html
<link rel="stylesheet" href="../dist/output.css">
```

- **`rel="stylesheet"`**: 告訴瀏覽器該鏈接的資源是樣式表（CSS），它將被用來設定頁面的外觀和佈局。