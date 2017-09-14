---
title:  "Types in JavaScript"
categories: learn
tags: javascript you-dont-know-js
read_time: 10
---
## JavaScript 內建的 Types
* ```null```
* ```undefined```
* ```boolean```
* ```number```
* ```string```
* ```object```
* ```symbol```（於 ES6 中加入）

除了 ```object``` 以外的 type 都稱為 **primitives**

---

## typeof 的用法
JavaScript 中 ```typeof``` 這個 operator 會檢查傳入值的 type，然後回傳對應的 string。

```javascript
typeof undefined     === "undefined"; // true
typeof true          === "boolean";   // true
typeof 42            === "number";    // true
typeof "42"          === "string";    // true
typeof { life: 42 }  === "object";    // true

// added in ES6!
typeof Symbol()      === "symbol";    // true
```

但是要需要注意的是，在 ```null``` 的情況，回傳的不是 string ```"null"```，而是 ```"object"```。
```javascript
typeof null === "object"; // true
```

因此，如果要用 ```typeof``` 來區分出 ```null```，需要配合 ```null``` 的 **falsy** 特性。
```javascript
var a = null;
(!a && typeof a === "object"); // true
```

> ```nulll``` 是唯一一個 **falsy** 而且 ```typeof``` 回傳又是 ```object``` 的 primitive 值

而 ```typeof``` 除了上面的 type 以外，還有一個額外的回傳值叫 ```"function"```。

```javascript
typeof function a(){ /* .. */ } === "function"; // true
```

有趣的是，function 本身並不是 top-level type 的一種。事實上 function 是 object 的 **subtype**，也稱作是 **callable object**，一個擁有 ```[[Call]]``` 屬性的 object。

也因為 function 是 object 的 subtype，他也和 object 一樣可以擁有 properties，例如：

```javascript
function a(b, c) {}
a.length;      // 2，代表這個 function 的參數數量
a.length = 5;  // a.length 是不可改的
a.length;      // 仍然是 2
```

> function 的 properties 是綁定於該 function，不同於 function 內的區域變數，不但不需要呼叫該 function 可以直接修改和取得該值。

### 變數的動態型別
**JavaScript 的變數沒有固定型別，型別是綁定在『值』上。**而變數可以隨時指定其他值。

```javascript
var a = 42;
typeof a;  // "number"

a = "42";
typeof a;  // "string"
```

---

## undefined 和尚未宣告
如果一個變數目前沒有被指定任何值，則對他取值會得到 ```undefined```，而對他使用 ```typeof``` 會得到 ```"undefined"```

```javascript
var a;
typeof a;  // "undefined"
```

這和尚未宣告是不同的，對尚未宣告的變數取值（其實對 JavaScript 而言根本不知道它是個變數）則會產生 ```ReferenceError``` 錯誤。

```javascript
var a;
a;  // "undefined"
b;  // ReferenceError: b is not defined
```

令人混淆的是，對他們使用 typeof 回傳的值卻都是 ```"undefined"```，而且也不會有錯誤，這是 typeof **特殊的 safety guard 機制**。

```javascript
var a;
typeof a;  // "undefined"
typeof b;  // "undefined"
```

這個 safety guard 機制可以用來測試某個給定的變數名稱是否存在且不會產生 Exception。

```javascript
// 如果 DEBUG 沒有被 declared，則會產生 ReferenceError
if (DEBUG) {
  console.log("Debugging is starting");
}

// 使用 typeof 來檢查就不會發生 Exception
if (typeof DEBUG !== "undefined") {
  console.log("Debugging is starting");
}

// 事實上，你也可以使用 try ... catch 來做到這件事情
var declared = true;
try {
  DEBUG;
} catch (e) {
  if (e.name == "ReferenceError") {
    declared = false;
  }
}

if (declared) {
  console.log("Debugging is starting");
}
```

這邊整理一個列表代表不同的值在不同的處理方式下的結果：

<table><tr><th>Value of x</th><th>x</th><th>typeof</th><th>try ... catch</th></tr><tr><td>undeclared</td><td>ReferenceError</td><td>"undefined"</td><td>false</td></tr><tr><td>undefined</td><td>undefined</td><td>"undefined"</td><td>true</td></tr><tr><td>null</td><td>null</td><td>"object"</td><td>true</td></tr></table>

在**瀏覽器**中，如果是要檢查某個全域變數是否有被定義和賦值，也可以使用 ```window``` 來取值，也不會產生 ReferenceError。但是要注意，這種 JavaScript 程式碼只能於瀏覽器中使用，如果是在後端（例如 Node.js）就沒有 ```window``` 全域變數可以這樣使用。

```javascript
if (window.DEBUG) {
  // 在瀏覽器中，即使 DEBUG 沒有被宣告，也不會產生 ReferenceError，因為 window 本身存在，只是沒有一個叫做 DEBUG 的 property 而已
  // 在 Node.js 之類沒有預設定義 window 的框架中一樣會產生 ReferenceError
}
```
