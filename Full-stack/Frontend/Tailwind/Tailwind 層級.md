---
Date: 2024-09-08
---
### 1. Base 層
這一層包含最基礎的樣式，通常用來定義 HTML 元素（如標題、段落、表單元素等）的基礎樣式。

範例：
```css
@layer base {
  h1 {
    @apply text-3xl font-bold;
  }
}
```

這會將所有的 `<h1>` 標籤設置為 `text-3xl` 大小並加粗。
### 2. Components 層
這一層專門用來定義自定義的 UI 元件樣式，如按鈕、卡片、導航欄等。這些樣式一般應該保持獨立且可重複使用。

範例：
```css
@layer components {
  .btn-primary {
    @apply bg-blue-500 text-white px-4 py-2 rounded-lg;
  }
}
```

這段代碼定義了一個名為 `.btn-primary` 的按鈕樣式，並且會放置在 **components** 層。
### 3. Utilities 層
這一層用來定義實用工具類別，這些類別通常是小而分離的，專注於應用單一樣式屬性，例如顏色、間距、大小等。您可以在 **utilities 層** 中擴展或自定義 Tailwind 的實用工具。

範例：
```css
@layer utilities {
  .text-shadow {
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
  }
}
```

這樣，您就創建了一個自定義的工具類別 `.text-shadow`，並將其放在 **utilities 層**。
### 層次的優先順序
- **Base** 層的樣式會最先應用，因為它們是最基礎的樣式。
- **Components** 層的樣式接著應用，通常是具體的 UI 組件樣式。
- **Utilities** 層的樣式會最後應用，因為這些類別一般具有最高的特異性，會覆蓋前兩層的樣式。