---
Date:
---
# 介紹
本專案使用
- vite@5.2.1
開始
```cmd
npm create vite@5.2.1
```
# 輸入數值
```jsx
import { useState } from "react";

function CreateForm(props) {
    const [content, setContent] = useState(0);
    return (
        <form className="create-form">
            <input type="text" placeholder="輸入待辦事項" value={content} />
            <button type="submit">加入</button>
        </form>
    )
}
export default CreateForm;
```

>[!info]
>這種情況下，我們是無法在input裡面輸入任何東西的，因為content為const，且useState中為空字串。
>所以這時需要使用input的一個屬性onChange()

onChange()，他會在input的值發生改變時執行。那我們還需要知道輸入的值是多少並接收 。所以這裡會需要用到event object 事件物件，它裡面會存放用戶設定的數據，而這個數據就會被儲存在state裡面
```jsx
import { useState } from "react";

function CreateForm() {
    const [content, setContent] = useState(0);
    return (
        <form className="create-form">
            <input type="text"
                placeholder="輸入待辦事項"
                value={content}
                onChange={(e) => setContent(e.target.value)}
            />
            <button type="submit">加入</button>
        </form>
    )
}

export default CreateForm;
```

>[!info]
>這種把state與input元素結合在一起方式，他的專有名詞為two-way binding 雙向綁定
>two-way binding 雙向綁定 : 當UI發生改變時，state會跟著改變;同樣的當state改變時，UI也會跟著改變

# 取消事件的預設行為
使用e.preventDefault()可以取消預設行為，這樣使案蝦button後不會刷新頁面
```jsx
import { useState } from "react";

function CreateForm({addTodo}) {
    const [content, setContent] = useState('');
    const handleSubmit = (e) => {
        e.preventDefault()
        addTodo(content);
        setContent('');
    }
    return (
        <form className="create-form" onSubmit={handleSubmit}>
            <input type="text"
                placeholder="輸入待辦事項"
                value={content}
                onChange={(e) => setContent(e.target.value)}
            />
            <button type="submit">加入</button>
        </form>
    )
}
export default CreateForm;
```
# 使用icon

