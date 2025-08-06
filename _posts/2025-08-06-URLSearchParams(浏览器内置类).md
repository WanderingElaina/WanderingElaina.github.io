

## 📌 原代码

```ts
const params = new URLSearchParams({
  Id: course.id.toString()
})
```

---

## 🔍 这行代码到底是干嘛的？

👉 这段代码的作用是：

> ✅ 创建一个 URL 查询参数对象，并把 `course.id`（课程ID）设置为参数 `Id`。

最终生成的是：

```
Id=3
```

这样你就可以将它拼接到 URL 里，比如：

```
/training-plan-design?Id=3
```

---

## 🧱 分解讲解每一部分：

### 1. `URLSearchParams`

> 是浏览器内置的一个类，用来处理 URL 的“查询参数”。

### 2. `new URLSearchParams({...})`

> 创建一个新的 URL 参数对象，可以传入一个对象，表示要包含哪些参数。

例子：

```ts
new URLSearchParams({ name: 'Alice', age: '20' })
// 结果 -> name=Alice&age=20
```

---

### 3. `course.id.toString()`

> 由于 `URLSearchParams` 需要所有值是字符串类型，所以把课程的 id（数字）转换为字符串。

例子：

```ts
course.id = 3
course.id.toString() // "3"
```

---

### 4. `const params = ...`

> 把创建好的 `URLSearchParams` 对象保存到 `params` 变量里，稍后你可以调用它的 `.toString()` 方法，把它变成：

```
Id=3
```

---

### ✅ 使用场景示例：

```ts
const course = { id: 3 }  // 课程 ID 是 3

const params = new URLSearchParams({
  Id: course.id.toString()
})

const url = `/training-plan-design?${params.toString()}`
console.log(url) // 输出: /training-plan-design?Id=3
```

---

## ✅ 总结一句话：

```ts
const params = new URLSearchParams({
  Id: course.id.toString()
})
```

这行代码的作用是：

> **构造出查询参数字符串 `Id=课程ID`，用于拼接在 URL 中传参跳转页面。**

---

