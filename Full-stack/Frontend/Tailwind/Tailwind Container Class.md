---
Date: 2024-09-08
---
**`container`** 是 **Tailwind CSS** 中的一個工具類別，用於創建一個響應式的固定寬度容器，它會根據螢幕的大小自動調整寬度。通常用於將網頁內容包裝起來，讓頁面佈局在不同裝置上保持一致，同時使內容在視窗中水平居中。
#### 主要特點
1. 響應式寬度
    - `container` 會根據螢幕大小自動調整寬度。Tailwind 預設了幾個響應式的斷點：
        - **`sm` (小螢幕)**：640px
        - **`md` (中螢幕)**：768px
        - **`lg` (大螢幕)**：1024px
        - **`xl` (超大螢幕)**：1280px
        - **`2xl`**：1536px
    - 在小螢幕上，容器寬度會是 100%，而在大螢幕上，容器會有固定的最大寬度。

2. 自動水平置中
    - `container` 類別會自動將容器的內容水平居中，讓頁面內容更對齊視窗的中心。

3. 預設內邊距
    - `container` 會自動應用水平內邊距，確保內容不會緊貼螢幕邊緣。
#### 自訂容器設定
可以在 `tailwind.config.js` 中自訂 `container` 的行為，如置中與內邊距的設定：

```js
module.exports = {
  theme: {
    container: {
      center: true, // 將容器置中
      padding: '2rem', // 設置 2rem 的內邊距
    },
  },
};
```

範例
```html
<div class="container mx-auto">
  <h1 class="text-3xl font-bold">我的響應式容器</h1>
</div>
```

- **`container`**：設置一個響應式容器。
- **`mx-auto`**：讓容器水平置中。
### 適用場景
`container` 通常用於建立頁面佈局的主要結構，如頁眉、頁腳和主要內容區，能夠確保在不同裝置下內容保持一致且居中。