---
Date: 2024-05-20
---
## 套件
- Tailwind
- Vscode Extention : Es7 + React/Redux/React Native snippets
## Start
```cmd
npm create vite@latest create-crash-2024
```

使用map來遍歷列表顯示畫面，但這樣做你會發現有個警告，告訴你說清單中的每個子項目都應該有一個唯一的key。

```jsx
const App = () => {
  const x = 5
  const y = 10
  const names = ['John', 'Jane', 'Doe', 'Sara']
  return (
    <>
      <div className='text-5xl'>App</div>
      <p>The sum of {x} and {y} is {x+y}</p>
      <ul>
        {names.map((name) => (
          <li>{name}</li>
))}
      </ul>
    </>
  )
}
```

所以你可以改為，如下。
```jsx
      <ul>
        {names.map((name, index) => (
          <li key={index}>{name}</li>
))}
      </ul>
```

判斷語句顯示組件，你不能在return 中寫判斷，你只能而外在前面寫另外的 return 或是說裡面根據參數來判斷添加組件 ，如下程式碼的一、二部分。

```jsx
const App = () => {
  const x = 5
  const y = 10
  const names = ['John', 'Jane', 'Doe', 'Sara']
  const loggedIn = True
  // 第一部分
  if(loggedIn){
	  return (<h1>Hello member</h1>)
  }
  return (
    <>
      <div className='text-5xl'>App</div>
      <p>The sum of {x} and {y} is {x+y}</p>
      <ul>
        {names.map((name) => (
          <li>{name}</li>
))}
      </ul>
      //第二部分
      {loggedIn ? <h1>Hello member</h1> : <h1>Hello Guest</h1>}
    </>
  )
}
```

如果說要讓文字有風格，則可以這樣做

```jsx
<p style={{color:'red',fontSize:'24px'}}>hello</p>
```

![[Pasted image 20240523204155.png]]

可以看到我們的文字已經有上述的風格了

但我們在 Tailwind 更常使用這樣的方式去呼叫。
```jsx
const styles = {
	color:'red',
	fontsize:'24px'
}
<p style={styles}>hello</p>
```

更改路徑，我們可以使用`react-router-dom`在jsx中進行路由的配置，像這樣。

```jsx
const router = createBrowserRouter(
  createRoutersFromElements(<Route path='/about' element={<HomePage/>} />)
)
const App = () => {
  return (
    <RouterProvider router={router}/>
  )
}
```

也可以更改成 index 方式

```jsx
const router = createBrowserRouter(
  createRoutersFromElements(<Route index element={<HomePage/>} />)
)
```

我們不使用 a tags 因為它會刷新整個頁面，讓我們改為 link tag
