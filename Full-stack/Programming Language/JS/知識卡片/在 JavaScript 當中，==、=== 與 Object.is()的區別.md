---
Date: 2024-02-19
tags:
  - Js
---
# == 鬆散比較 (loose equality)

== 在比較兩個值之前，**會先強制轉換型別與值**，

舉例來說，
下面的這三個例子，之所以會印出 true ，正是因為在比較前，型別被轉換了；否則數字 1 在 JavaScript 中跟字串 '1' 是代表不同的值，嚴格來說不應該相等。**同理 undefined 與 null 在 JavaScript 是不同型別與不同的值，但如果使用 == 會回傳 true。**

```js
console.log(1 == "1"); // true
console.log(0 == false); // true
console.log(undefined == null); // true
```
# === 嚴格比較 (strict equality)

**=== 不會強制轉換型別與值，所以如果是不同型別**，比較兩者會回傳 `false`。不同值的話一樣會回傳 `false`。

不過有兩個情況例外，當我們比較 `+0` 和 `-0`時，嚴格比較會回傳 `true`；以及比較 `NaN` 和 `NaN` 會是 `false`。而這兩個狀況則是同值比較 `Object.is` 派上用場的時候。

```JS
+0 === -0; // true
NaN === NaN; // false
```
## Object.is同值比較 (same-value equality)

同值比較顧名思義是在比較兩個值是不是相等。雖然它是 Object 開頭，但比較的可以是任意的兩個值。例如：

```JS
console.log(Object.is(1, 1)); // true
console.log(Object.is(1, "1")); // false
```

上面提到的兩種在 === 時遇到的問題，可以透過 `Object.is` 有效分辨

```JS
console.log(Object.is(+0, -0)); // false
console.log(Object.is(NaN, NaN)); // true
```

不過如果要有效分辨 `NaN`，在 JavaScript 有一個方法叫 `isNaN` 是可以使用的。端看開發團隊的習慣，假如對於 `Object.is` 感到陌生，可以選擇用 `Number.isNaN`。