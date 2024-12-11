---
Date: 2024-09-07
---
`tailwind.config.js` 文件用來自定義框架的設置，特別是 **`theme` 配置**，可以讓開發者擴展或覆蓋 Tailwind 預設的設計系統。這使得 Tailwind 更加靈活，能夠適應不同的項目需求。
### `theme` 配置概述
`theme` 是 Tailwind 中用來定義設計系統的核心部分。它包含了所有的樣式定義，如顏色、字體、間距、邊框、陰影等。

```js
module.exports = {
  theme: {
    extend: {
      // 您可以在這裡擴展預設的設計系統
    },
  },
}
```
### 常見的 `theme` 配置項
#### 1. 顏色 (`colors`)
顏色定義用於控制文字、背景、邊框等元素的顏色。可以自定義顏色來匹配您的品牌風格。

```js
module.exports = {
  theme: {
    extend: {
      colors: {
        customBlue: '#1e40af',
        customRed: '#e63946',
      },
    },
  },
}
```

範例使用：

```html
<div class="text-customBlue">這段文字是自定義藍色。</div>
<div class="bg-customRed">這段背景是自定義紅色。</div>
```
#### 2. 字體家族 (`fontFamily`)
定義字體家族，可以用來為您的網站設置特定的字體風格。

```js
module.exports = {
  theme: {
    extend: {
      fontFamily: {
        sans: ['Helvetica', 'Arial', 'sans-serif'],
        custom: ['Pacifico', 'cursive'],
      },
    },
  },
}
```

範例使用：

```js
<div class="font-sans">這段文字使用 sans-serif 字體。</div>
<div class="font-custom">這段文字使用自定義字體。</div>
```
#### 間距 (`spacing`)
Tailwind 使用 `spacing` 系統來控制邊距和填充（`margin` 和 `padding`）。可以擴展這些值以適應自定義設計需求。

```js
module.exports = {
  theme: {
    extend: {
      spacing: {
        '72': '18rem',
        '84': '21rem',
        '96': '24rem',
      },
    },
  },
}
```

範例使用：

```html
<div class="mt-72">這個元素有 18rem 的上邊距。</div>
```
#### 4. 邊框圓角 (`borderRadius`)
自定義元素的圓角大小。

```js
module.exports = {
  theme: {
    extend: {
      borderRadius: {
        'xl': '1rem',
        '2xl': '1.5rem',
      },
    },
  },
}
```

範例使用：

```html
<div class="rounded-xl">這個元素有 1rem 的圓角。</div>
```
#### 5. 字體大小 (`fontSize`)
定義字體大小的值，這可以擴展 Tailwind 的字體大小系統。

```js
module.exports = {
  theme: {
    extend: {
      fontSize: {
        'xs': '.75rem',
        'lg': '1.125rem',
        '3xl': '1.875rem',
      },
    },
  },
}
```

範例使用：

```html
<div class="text-xs">這是 0.75rem 大小的文字。</div>
<div class="text-3xl">這是 1.875rem 大小的文字。</div>
```
#### 6. 陰影 (`boxShadow`)
Tailwind 提供了陰影系統，可以自定義陰影大小和顏色。

```js
module.exports = {
  theme: {
    extend: {
      boxShadow: {
        'custom': '10px 10px 15px rgba(0, 0, 0, 0.3)',
      },
    },
  },
}
```

範例使用：

```html
<div class="shadow-custom">這個元素有自定義陰影。</div>
```
#### 7. 寬度 (`width`) 和 高度 (`height`)
自定義寬度和高度屬性。

```js
module.exports = {
  theme: {
    extend: {
      width: {
        '96': '24rem',
      },
      height: {
        '96': '24rem',
      },
    },
  },
}
```

範例使用：

```html
<div class="w-96 h-96">這個元素有 24rem 的寬度和高度。</div>
```
### 注意
需要擴展時，務必記得在 `extend` 中添加官方預設的需要擴展的名稱，如果添加到`extend` 外的話，則會覆蓋原本的設置。