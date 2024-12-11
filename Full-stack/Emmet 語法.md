---
Date: 2024-09-07
---
**Emmet 語法** 是用來幫助開發者快速編寫 HTML、CSS 和 XML 結構。Emmet 語法專注於提高編寫代碼的效率，特別適合用於重複性較強的標籤結構。
### 核心特性
**標籤自動生成**： 只需輸入標籤名稱即可生成對應的 HTML 結構。
- 輸入 `div`，會展開為：

```html
<div></div>
```

**層次關係 (`>`)**： `>` 表示父子關係，用來嵌套元素。
- 輸入 `div>ul>li`，會展開為：

```html
<div>
  <ul>
    <li></li>
  </ul>
</div>
```

**兄弟元素 (`+`)**： `+` 表示兄弟關係，用來生成同一層級的標籤。
- 輸入 `h1+p`，會展開為：

```html
<h1></h1>
<p></p>
```

**多個元素 (`*`)**： `*` 用來生成多個相同的元素。
- 輸入 `ul>li*3`，會展開為：
  
```html
<ul>
  <li></li>
  <li></li>
  <li></li>
</ul>
```

**類和 ID (`.` 和 `#`)**： `.` 用於添加類名，`#` 用於添加 ID。
- 輸入 `div.container#main`，會展開為：

```html
<div id="main" class="container"></div>
```

**屬性 (`[]`)**： `[]` 用來設置標籤屬性。
- 輸入 `input[type="text" name="username"]`，會展開為：

```html
<input type="text" name="username">
```

**內容 (`{}`)**： `{}` 用來定義標籤的內部內容。
- 輸入 `p{Hello World}`，會展開為：

```html
<p>Hello World</p>
```

**分組與更多進階功能**： 可以使用 `()` 進行分組，從而更靈活地生成結構。
- 輸入 `div>(header>h1)+main>p*2+footer`，會展開為：

```html
<div>
  <header>
    <h1></h1>
  </header>
  <main>
    <p></p>
    <p></p>
  </main>
  <footer></footer>
</div>
```
### 常見的 Emmet 縮寫範例：
**生成列表項**：`ul>li*5`

```html
<ul>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
</ul>
```

**生成帶有類和 ID 的結構**：`div.container#main`

```html
<div id="main" class="container"></div>
```

**生成表單元素**：`form>input[type="text"]+input[type="password"]+button[type="submit"]`

```html
<form>
  <input type="text">
  <input type="password">
  <button type="submit"></button>
</form>
```