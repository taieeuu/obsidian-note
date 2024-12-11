---
Date: 2024-09-07
---
### 文字顏色 (`text-{color}`)
文字顏色控制文字的顯示顏色。

```html
<div class="text-2xl text-emerald-800">Lorem ipsum dolor sit amet.</div>
<div class="text-2xl text-yellow-500">Yellow is what we want to see</div>
```
### 背景顏色 (`bg-{color}`)
背景顏色控制元素的背景顯示。可以應用於容器、段落等，並且支持透明度修飾。

```html
<div class="text-2xl bg-blue-400">Lorem ipsum dolor sit amet.</div>
<div class="text-2xl bg-rose-400">Lorem ipsum dolor sit amet.</div>
<div class="text-2xl bg-blue-600 text-white">Lorem ipsum dolor sit.</div>
<div class="text-2xl bg-blue-100 text-black">Lorem ipsum dolor sit amet.</div>
```

**透明度修飾**：您可以使用 `/50` 這樣的語法來應用 50% 的透明度。

```html
<div class="text-3xl bg-indigo-500/50">Lorem ipsum dolor sit amet.</div>
```
### 自定義顏色
也可以使用十六進位格式來自定義顏色，例如：

```html
<div class="text-3xl text-[#1a6b3e] bg-[#f7b536]">Lorem ipsum dolor sit amet.</div>
```
### 邊框顏色 (`border-{color}`)
為邊框設置顏色，並且支持透明度修飾。

```html
<div class="text-3xl bg-blush-100 border-4 border-red-500/70">Lorem ipsum dolor sit.</div>
```
### 支援的顏色：
常用顏色類別包括：

- **中性色**：`slate`、`gray`、`zinc`、`neutral`、`stone`
- **暖色調**：`red`、`orange`、`amber`、`yellow`
- **冷色調**：`lime`、`green`、`emerald`、`teal`、`cyan`、`sky`、`blue`
- **其他色調**：`indigo`、`violet`、`purple`、`fuchsia`、`pink`、`rose`

每種顏色都有從 `50` 到 `950` 的深淺級別，數字越大顏色越深。