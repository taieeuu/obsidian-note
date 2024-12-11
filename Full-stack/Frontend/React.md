---
Date: 2024-04-23
tags:
---
# 前言
React是屬於Single Page Application 單頁式應用程式
**一般**
![[React 1.png|500]]
**React**
![[React 2.png|500]]
# 組件 Component
- 把多個html元素組成一個
- 把網頁拆成不同部份來寫

兩種方式創建Component
- class
- function
>[!info]
>只要一個函數中回傳一個html元素，那這個檔案就為一個組件。
>那現在我們這邊不是在撰寫JS，而是JSX (javascript XML)，他可以讓你在js中寫html，但這樣問題又變成遊覽器是不懂js的，所以JSX => 編譯工具 => Javascript

**補充**
React與HTML物件本質都是JS物件，只是都是被JSX語法所包起來

Example:
```js
function MyComponent() {
  return <div>Hello World</div>;
}

function App() {
  return (
    <div>
      <MyComponent />
      <MyComponent />
    </div>
  )
}

export default App
```

我們可以在main.jsx當中看見我們導入的`APP`，也就是說下面這句就是把她轉換成html過程。
```js
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'

  
ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
```
**補充**
基本上所有的組件都會放置在App.jsx當中，進而形成樹狀結構，所以App又有另一個名稱為根組件，
且我們在index.html也會發現在body中會有一個div 中的 id="root"，也就是react會把我們所寫的html通通都塞到root元件裡面
# JSX
- 不能同時回傳多個components，只能回傳1個組件。也就是必須把組件包成一個元素。這也是JS語法

下面這個寫法，是回傳一個div，但如果是想要單純回傳的話，可使用<></>。圖二
```js
function App() {
  return (
    <div>
      <MyComponent />
      <MyComponent />
    </div>
  )
}
```

圖二，下圖中是JS中的寫法，我們稱為Fragment
```js
function App() {
  return (
    <>
      <MyComponent />
      <MyComponent />
    </>
  )
}
```

**重點**
```js
function App() {
  return (
    <div>
      <MyComponent />
      <MyComponent />
    </div>
  )
}
```
>[!info]
>如果return後面沒有()，則程式會無法執行，因為return 代表的是讓函數停止執行，所以return 下面程式碼就不會被執行。這是為JS的語法特性

# Html中寫JS
亦可寫變數與函數
```js
function App() {
  const text = 'hello world'
  return (
    <div>
    <input type="text" placeholder={text}/>
	  <h1>{text}</h1>
	  <h1>{text.toUpperCase()}</h1>
      <MyComponent />
      <MyComponent />
    </div>
  )
}
```
- 在HTML中，空元素的斜線是可有可無的
- 在JSX中，空元素必須要有斜線

html中的class == js中的className，因為在js中class式關鍵字，所以為了避免衝突jsx改為使用className。
同上在html中的for，在jsx中變為htmlFor。
**注意**
這些屬性是使用駝峰式命名法來命名的，前面字母微小寫，後面為大寫，兩個以上的單詞。e.g. className

你在其他地方可能也會看到使用兩個{{}}，這其實只是在jsx中使用物件而已，最常出現的地方式在`style`中。
外面的大括號代表我們要在jsx中使用Js，裡面的括號代表物件。而裡面的backgroundColor並不是真正的css，只是利用js物件來模擬css的一種方式。
e.g. 在css中屬性值式不需要加上引號的、詞之間使用`-`來隔開

```js
function App() {
  const text = 'hello world'
  return (
    <div>
    <input type="text" placeholder={{text}}/>
	  <h1 style={{backgroundColor: 'red'}}>{text}</h1>
	  <h1>{text.toUpperCase()}</h1>
      <MyComponent />
      <MyComponent />
    </div>
  )
}
```
圖二
```css
{
	background-color:red;
}
```

這種在html標籤中使用css我們稱為inline css，但我們更常在其他檔案中編寫css

App.css
```css
h1 {
    color: blue;
}

```

在正常的JS中你其實不能導入css，但這邊能做的原因是vite會把你導入的css檔案插入到html當中。

React中寫事件處理
```js
function App() {
  const text = 'hello world'
  return (
    <>
	<button onClick={function(){alert('hello')}}>我是按鈕</button>
      <MyComponent />
    </>
  )
}
```
也可以寫成
```js
function App() {

	const handleClick = () =>{
		aler('hello')
	}
	
	return (
	<>
	<button onClick={handleClick}>我是按鈕</button>
	  <MyComponent />
	</>
  )
}
```

**注意**
切勿寫成下面這樣，如果多加()，代表執行的意思
```js
function App() {

	const handleClick = () =>{
		aler('hello')
	}
	
	return (
	<>
	<button onClick={handleClick()}>我是按鈕</button>
	  <MyComponent />
	</>
  )
}
```
# JSX中的陣列
在jsx使用陣列時，需要給他們各自加上key屬性否則會產生警告，加上key值主要原因是這樣可以讓react精準定位陣列每一個元素，進而提高渲染效率。
```js
function App() {

	const listItems = [
		<MyComponent key="0"/>
		<MyComponent key="1"/>
		<MyComponent key="2"/>
	];
	
	return (
	<>
		{listItems}
	</>
  )
}
```

## 兩個常見的陣列 map() and filter()
### map() 
能夠讓你把一個陣列轉換成另一個陣列
**注意**
不能使用下列寫法，因為在 JSX 中插入 `listItems` 陣列時，React 會嘗試將整個陣列轉換為字串並渲染它，而不是將每個元素渲染為單獨的元素。因此，這樣的寫法不會產生預期的結果。
想要渲染 `listItems` 陣列中的每個元素，需要使用 `map()` 方法來將它轉換為一組 JSX 元素。這樣可以確保每個陣列元素都被正確地轉換為一個單獨的 JSX 元素，並且被渲染到 DOM 中。
```js
function App() {
	const listItems = [ 
		{ id: 1, name: "apple" },
		{ id: 2, name: "banana" }, 
		{ id: 3, name: "orange" }, 
	]; 
	return ( 
		<div> {listItems } </div> 
	); 
}
```
### Filter
```js
function App() {
  const listItems = [
    { id: 1, name: "apple" },
    { id: 2, name: "banana" },
    { id: 3, name: "orange" },
  ]

  const filteredItems = listItems.filter((item) => {
    if (item.id !== 2) {
      return true;
    }
  })

  return (
    <div>
      {filteredItems.map((item) => {
        return <div>{item.name}</div>;
      })}
    </div >
  )
}
```
# React 條件判斷
只要使用js語法都需要使用 { } 大括號來包起來
```js
function App() {
	return(
	//<div className={'c ${false ? 'a':'b'}'}>
		<div className={false? 'a':'b'}>
		{true ? <h1>hello</h1>:<h1>world</h1>}
		</div>
	) 
}
```
**補充**
`<div className={'c ${false ? 'a':'b'}'}>`，這邊className後面使用大括號，是因為後面的否引號字串為JS語法與html的雙引號不一樣。

還有另一種 條件判斷 &&，在網頁上的&&用法
```jsx
function App() {
	return(
	//<div className={'c ${false ? 'a':'b'}'}>
		<div className={false? 'a':'b'}>
			{true && <h1>hello</h1>}
		</div>
	) 
}
```
# Props
props = properties = 屬性，讓你從父組件傳遞資料到子組件
```jsx
function MyComponent(props) {
	console.log(props)
	props.c()
	return <>
		div><{props.a}/div>
		div><{props.b}/div>
	</>
}

function App() {
  return (
    <>
      <MyComponent a="hello world" b="你好"
      c={()=>{console.log(123);}/>
    </>
  )
}
export default App

# --result--
hello world
你好
```
也可以使用解構附值來取得裡面資料。ps.物件的解構值是以屬性的名稱來取用資料
```jsx
function MyComponent({a,b,c}) {
	c()
	return <>
		div><{a}/div>
		div><{b}/div>
	</>
}

function App() {
  return (
    <>
      <MyComponent a="hello world" b="你好"
      c={()=>{console.log(123);}/>
    </>
  )
}
export default App

# --result--
hello world
你好
```
## Props傳遞組件
在MyComponent 標籤中，寫入另一個組件SecondComponent，那React會把這個SecondComponent賦予到一個叫做`children`的屬性上面，所以這裡我們可以使用解構附值來取得children屬性。
這種也是常見的寫法，我們通常叫做composition(組合)
```jsx
function SecondComponent(){
	return <h1>hello world</h1>
}

//這邊的children就等於SecondComponent
function MyComponent(children) {
	return<>{children}</>
}

function App() {
  return (
	  <>
	  <MyComponent>
		  <SecondComponent/>
	  <MyComponent/>
	  </>
  )
}
export default App

# --result--
hello world
```
也可以這樣寫
```jsx
	  <MyComponent a={<SecondComponent/>}>
```
# State 狀態
在編寫程式時，我們常常需要跟資料打交道，而我們通常會把資料儲存在變數裡面。
但是在React中則不一樣，我們通常會把資料儲存在一個東西叫State

以下為經典範例
```jsx
function MyComponent(){
	let clicks=0;
	const handleClick = () => {
		clicks++;
		console.log(clicks);
	}
	return (
		<>
			<button onClick={handleClick}>點擊次數:{clicks}</button>
		</>
	)
}
```

>[!info]
>當我們點擊按鈕時，我們會發現網頁上的次數沒有增加，但console裡的卻改變了。這個現象其實並不奇怪。
>因為當我們點擊按鈕時我們確實給click變數加上1，然後再將clicks給印出來，所以console裡的次數就會跟著改變，但是button裡的class，我們並沒有寫程式碼來把html元素重印一次，所以她的次數會一直保持在0。因此在React中當變數的值發生變化時，網頁是不會跟者改變的，那遇到這樣情況，我們可以把資料儲存在state裡面，而當資料內容發生改變時React就會更新html元素

要使用state()，我們會使用到React中一個函數useState()
```jsx
import { useState } from 'react'

function MyComponent(){
	const [clicks, setClicks] = useState(0);
	const handleClick = () => {
		setClicks(clicks + 1)
		console.log(clicks);
	}
	return (
		<>
			<button onClick={handleClick}>點擊次數:{clicks}</button>
		</>
	)
}
```

>[!info]
>首先要給useState()一個參數，而這個參數代表state初始值0，然後useState會回傳一個陣列，而陣列裡面會存放兩個東西，(1)state內容 (2)更改state內容的函數

**注意**
const [clicks, setClicks] = useState(0);，這裡我們是使用結構附值來取得資料。物件的結構附值順序不重要但名字很重要，而陣列的結構附值相反，順序很重要，名字不重要。

## 新手容易犯的錯誤
```jsx
import { useState } from 'react'

function MyComponent(){
	const [clicks, setClicks] = useState(0);
	const handleClick = () => {
		clicks++;
		console.log(clicks);
	}
	return (
		<>
			<button onClick={handleClick}>點擊次數:{clicks}</button>
		</>
	)
}
```
>[!info]
>這裡setClicks(clicks + 1)，改為了clicks++，很多初學者都會這樣做。
>但這樣寫是錯誤的，因為他是const你不能更改，再者如果可以更改了，React也部會知道要更新這裡的html。

![[React 3.png|500]]

React 的渲染有兩種
1. 初次渲染 initial rendering
2. 重新渲染 re-rendering
>[!info]
>顧名思義(1)第一次React把jsx轉換成html元素時。
>		(2)React重新再把jsx轉換成html元素時。 
>例如網頁初次載入時，他就叫初次渲染，而當我們典籍呼叫這個setClicks函數時，React會察覺到state改變了所以會再重新渲染html元素

**注意**
不同組件之間得state是互相獨立的
![[React 4.png|600]]
也可以傳遞給子組件
```jsx
import { useState } from 'react'
function ChildComponent({clicks}){
	return <div>{clicks}</div>
}
function MyComponent(){
	const [clicks, setClicks] = useState(0);
	const handleClick = () => {
		setClicks(clicks+1);
		console.log(clicks);
	}
	return (
		<>
			<button onClick={handleClick}>點擊次數:{clicks}</button>
			<ChildComponent clicks={clicks}/>
		</>
	)
}
```