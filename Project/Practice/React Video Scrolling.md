---
Date: 2024-05-12
---
# 套件
```cmd
npm i framer-motion # 他有很棒的hook，稱為用戶滾動

```

安裝為之後開始來撰寫

```js
import { useRef } from "react"

function App() {
  const ref = useRef<HTMLCanvasElement>(null)
  return (
    <div style={{height:'5000px'}}>
      <canvas  
      style={{border:'1px solid red'}}
      width={1000}
      height={1000}
      ref={ref}
      ></canvas>
    </div>
  );
}
export default App
```
>[!info]
>**useRef**: 是 React 提供的一个 Hook 函数，用于创建一个可变的 ref 对象。这个 ref 对象可以在组件的整个生命周期中存储一个可变的值，并且不会触发组件的重新渲染。
>返回中，創建一個 canvas 元素，設置為5000px ,以便在頁面部會滾動，其在內部創建canvas元素，設置一些樣式、邊框、高度，並通過 ref 屬性將之前創建的 ref 引用與之前的 canvas 元素關聯起來。
>這樣 ref 引用就可以在組件中被訪問，並且可以用於操作或獲取 canvas 元素 DOM 節點。

這樣的效果，就是底部會有一個框框。
![[React Video Scrolling 1.png|500]]

然後我們需要實現滾動圖片動的效果，也就是使用者會從上往下滾動，圖片會呈現一貞一貞的播放圖片，所以我們需要在底部新增一個空白地區。添加`<div style={{height:'2000px'}}></div>`
```js
import { useRef } from "react"

function App() {
  const ref = useRef<HTMLCanvasElement>(null)
  return (
    <div style={{height:'5000px'}}>
	<div style={{height:'2000px'}}></div>`
      <canvas  
      style={{border:'1px solid red'}}
      width={1000}
      height={1000}
      ref={ref}
      ></canvas>
    </div>
  );
}
export default App
```

實現效果如下。

![[video-scrolling.gif]]

接著我們會需要他方框位置，這樣才能計算我們甚麼時候切換圖片。

```jsx
import { useMotionValueEvent, useScroll } from "framer-motion";
import { useRef } from "react"

function App() {
  const ref = useRef<HTMLCanvasElement>(null)
  const { scrollYProgress} = useScroll({
    target: ref,
    offset: ['center end', 'start start']
  });

  useMotionValueEvent(scrollYProgress,
    'change', (latest) => {
      console.log(latest)
    }
  )

  return (
    <div style={{height:'6000px'}}>
      <div style={{height:'2000px'}}></div>
      <canvas  
      style={{border:'1px solid red'}}
      width={1000}
      height={1000}
      ref={ref}
      ></canvas>
    </div>
  );
}
export default App
```
>[!info]
>1. 使用 useScroll 監聽頁面的滾動事件，並從返回對象中解鉤出 scrollYProgress 屬性，scrollYProgress表示當前頁面的垂直滾動進度，`[0, 1]`，頂部至底部。
>	這邊的target元素是監聽目標，目前創建的 ref 引用指向的 canvas 元素
>	offset 是一個數組，用於設置滾動的起始和結束的位置。
>2. 使用 useMotionValueEvent ， hook 監聽 scrollYProgress 的變化事件。當前進度發生變化時，執行回掉函數，並將最新滾動進度輸出到控制台上

接著我們需要將圖片load出來了。

```jsx
import { useMotionValueEvent, useScroll, useTransform } from "framer-motion";
import { useCallback, useMemo, useRef } from "react"

function App() {
  const ref = useRef<HTMLCanvasElement>(null)
  const { scrollYProgress} = useScroll({
    target: ref,
    offset: ['center end', 'start start']
  });
  const images = useMemo(() => {
    const loadedImages: HTMLImageElement[] = [];
    for (let i = 1; i <= 86; i++) {
      const img = new Image();
      img.src = `/images/${i}.webp`;
      loadedImages.push(img)
    }
    return loadedImages;
  }, [])
  const currentIndex = useTransform(scrollYProgress, [0, 1], [1, 86])
  const render = useCallback(
    (index: number) => {
      if(images[index-1]){
        ref.current?.getContext('2d')?.drawImage(images[index-1], 0, 0);
      }
    },
    [images]
  )
  useMotionValueEvent(currentIndex,
    'change', (latest) => {
      render(Number(latest.toFixed())); //0.2324 => 0 or 1.89 => 2
    }
  )
  return (
    <div style={{height:'4000px', background:'black'}}>
      <div style={{height:'500px'}}></div>
      <canvas  
      width={1000}
      height={1000}
      ref={ref}
      ></canvas>
    </div>
  );
}
export default App
```
>[!info]
>1. 使用 useMemo() hook 建立了一個名為 images 的 memoized 值。 這個值包含了一個包含了 86 張圖像的數組，而每張圖像都是 HTMLImageElement 類型的物件。 這個陣列被快取起來，以避免在每次元件重新渲染時都重新建立。且 return 到 images中。
>2. 使用 useTransform() hook 監聽頁面捲動進度 scrollYProgress，並將捲動進度對應到影像陣列的索引範圍 `[1, 86]`。 這個過程將頁面滾動進度轉換為對應的圖像索引。也就是`[0,1]`的區間放大到`[1, 86]`區間。
>3. 建立了一個名為 `render` 的回呼函數，它接收一個參數 `index`，表示要渲染的影像索引。 在這個函數中，透過檢查 `images` 陣列中是否存在對應索引的影像，如果存在，則將該影像繪製到畫布上。
>4. 使用 useMotionValueEvent() hook 監聽捲動進度的變化，當捲動進度改變時，執行 render 函數，並將目前捲動進度轉換為整數（對應影像陣列的索引）作為參數傳遞給 render 函數。

結果如下 : 
![[video-scrolling2.gif]]