---
Date: 2024-09-08
---
### 1. animate-spin
- 讓元素旋轉。

```html
<div class="animate-spin w-16 h-16 border-4 border-blue-500 border-t-transparent rounded-full"></div>
```

這個例子創建了一個帶有旋轉動畫的圓形邊框，邊框會不斷旋轉。

---
### animate-ping
- 產生一個擴展並淡出效果，類似波紋的擴展。

```html
<div class="animate-ping w-16 h-16 bg-blue-500 rounded-full"></div>
```

這個圓形元素會產生一個淡出的擴展動畫，像波紋一樣。

---
### animate-pulse
- 讓元素產生淡入淡出的脈衝效果。

```html
<div class="animate-pulse w-16 h-16 bg-blue-500"></div>
```

這個元素會產生一個周期性的淡入淡出的脈衝效果。
### 4. animate-bounce
- 讓元素產生跳動效果。
  
```html
<div class="animate-bounce w-16 h-16 bg-blue-500"></div>
```

這個元素會上下跳動，類似於彈簧效果。

---
### 自定義動畫
除了預設的動畫類別外，Tailwind CSS 也允許你創建自定義動畫。你可以通過 `@keyframes` 定義動畫步驟，然後使用 `animation` 屬性將動畫應用到元素。
#### 1. 定義自定義動畫
首先，你需要在 `tailwind.config.js` 文件中擴展 Tailwind 的動畫設定：

```js
module.exports = {
  theme: {
    extend: {
      animation: {
        'spin-slow': 'spin 3s linear infinite',
        'wiggle': 'wiggle 1s ease-in-out infinite',
      },
      keyframes: {
        wiggle: {
          '0%, 100%': { transform: 'rotate(-3deg)' },
          '50%': { transform: 'rotate(3deg)' },
        }
      }
    }
  }
}
```

我們定義了一個 `wiggle` 動畫，讓元素從 -3 度旋轉到 3 度，並且這個動畫持續 1 秒，無限循環。

---
#### 2. 應用自定義動畫
在 HTML 中使用這個自定義動畫：

```html
<div class="animate-wiggle w-16 h-16 bg-blue-500"></div>
```

這個元素會按照我們定義的 `wiggle` 動畫進行左右搖擺。
### 動畫相關的屬性
- **`animate-`**：指定使用的動畫類別。
- **`duration-`**：設置動畫的持續時間（以毫秒為單位），例如 `duration-500` 表示動畫持續 500 毫秒。
- **`delay-`**：設置動畫開始的延遲時間，例如 `delay-300` 表示動畫延遲 300 毫秒後開始。
- **`ease-`**：設置動畫的加速度曲線，例如 `ease-in`、`ease-out`、`ease-in-out`。
- **`infinite`**：動畫無限循環。
- **`once`**：動畫執行一次。