---
Date: 2024-02-07
tags:
  - Unity
---
### `[SelectionBase]`:
- 這是一個Unity的標籤，用於確保在Hierarchy視窗中選擇該物件時，其實際上是選擇了具有該標籤的最上層父物件。這有助於更方便的編輯和調整該物件的屬性。
### `[ExecuteInEditMode]`:
- 這是一個Unity的標籤，表示這個腳本會在編輯模式下執行，而不僅僅是在遊戲運行時。這對於需要在編輯器中實時更新的一些功能非常有用。
### `[DisallowMultipleComponent]`:
- 這是一個Unity的標籤，確保同一物件上只能添加一個該腳本的實例，避免重複添加相同的元件。
### `RequireComponent(typeof(RectTransform))]`:
- 這是一個Unity的標籤，指定該腳本需要附加到具有指定元件（這裡是RectTransform）的物件上。如果該物件上沒有指定的元件，Unity將自動添加這個元件。
### `[AddComponentMenu("UI/Extensions/PagingView")]`:
- 這是一個Unity的標籤，它定義了在Unity編輯器的"Add Component"選單中的位置。在這個例子中，這個腳本將添加到"UI/Extensions/PagingView"這個菜單中，使得在Unity編輯器中更容易找到並添加。