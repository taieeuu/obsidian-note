---
Date: 2024-09-08
---
繼承上一篇，我們可以將 `plugins` 搬移到另一個腳本中，如下

```js
module.exports =  plugin(function({addBase, addUtilities, addComponents,matchComponents, theme}){
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
      });
   })
```

再將其引入至 `tailwind.config.js` 中

```js
const customPlugin = require('./src/plugins/testplugin')
module.exports = {
    content: [
      "./src/**/*.{html, js, jsx}"],
    theme: {
      circleSizes:{
        sm: '3rem',
        md: '5rem',
        lg: '7rem',
        xl: '10rem',
     },
      extend: {
      },
    },
    plugins: [
      customPlugin,
    ],
  }
```
### Official
在 [Tailwind 官方網站的plugins](https://tailwindcss.com/docs/plugins) 也有提供已設定好模板做使用。
### popular external plugins options and sites
- **Flowbite**: [flowbite.com](https://flowbite.com/)  
    Flowbite 是一個內置 Tailwind CSS 的元件庫，提供現成的 UI 元件，如按鈕、卡片、導航欄、表單等，幫助開發者快速搭建 UI。
    
- **DaisyUI**: [daisyui.com](https://daisyui.com/)  
    DaisyUI 提供了一組 Tailwind CSS 的擴展元件，這些元件風格一致且高度可自定義，適合快速開發和設計一致的應用。
    
- **Tailwind Elements**: [tailwind-elements.com](https://tailwind-elements.com/)  
    Tailwind Elements 是一個開源庫，提供大量用於 Tailwind CSS 的預建元件，幫助加速 UI 設計流程。
    
- **Headless UI**: [headlessui.com](https://headlessui.com/)  
    Headless UI 提供無頭元件（headless components），這些元件是完全無樣式的，讓你可以用 Tailwind CSS 來定義其樣式。
    
- **Tailwind CSS Animate**: [tailwindcss-animate](https://github.com/jamiebuilds/tailwindcss-animate)  
    一個 Tailwind CSS 插件，提供一系列預設的動畫類別，幫助開發者快速應用動畫效果。