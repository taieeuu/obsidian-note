---
Date: 2024-09-08
---
### 1. `@apply`
`@apply` 指令允許您將 Tailwind 的實用工具類別應用到自定義的 CSS 中，這對於需要重複使用相同樣式的情況非常實用。

範例
```css
.btn {
  @apply px-4 py-2 bg-blue-500 text-white rounded-lg;
}
```

這樣，`.btn` 類別就會應用 Tailwind 的 `px-4`、`py-2`、`bg-blue-500` 等工具類別，使按鈕擁有一致的樣式。
### 2. `@screen`
`@screen` 指令讓您可以根據不同的屏幕尺寸應用樣式，這樣可以更好地支持響應式設計。

範例
```css
.header {
  @apply text-lg;

  @screen md {
    @apply text-xl;
  }

  @screen lg {
    @apply text-2xl;
  }
}
```

這段代碼會根據不同的屏幕寬度調整 `.header` 的字體大小，小屏幕下是 `text-lg`，中屏幕時是 `text-xl`，大屏幕是 `text-2xl`。
### 3. `@layer`
`@layer` 指令允許您將自定義樣式插入到 Tailwind 的核心層（如 **base**、**components** 或 **utilities**），確保自定義樣式和框架內建樣式有一致的層次結構。

範例
```css
@layer components {
  .card {
    @apply p-4 bg-white shadow-lg rounded-lg;
  }
}
```

這樣，`.card` 組件會被添加到 Tailwind 的 **components** 層。
### 4. `@variants`
`@variants` 指令允許您為自定義樣式定義不同的狀態變化，例如 `hover`、`focus` 或是您自定義的其他狀態。

範例
```css
@variants hover, focus {
  .btn-custom {
    @apply bg-green-500 text-white;
  }
}
```

這樣，`.btn-custom` 在 `hover` 和 `focus` 狀態時會應用綠色背景和白色文字。