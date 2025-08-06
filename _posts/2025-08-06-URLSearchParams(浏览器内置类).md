

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

来**详细解释为什么要用 `URLSearchParams`**，而不是直接拼字符串的方式：

---

### ✅ **先看你的写法能不能用：**

你写的这个形式：

```ts
const url = `/training-plan-design?$course.id.toString()}`
```

其实是 **有语法错误的**，正确拼接方式得是：

```ts
const url = `/training-plan-design?Id=${course.id.toString()}`
```

这样是可以运行的，确实也能得到 `/training-plan-design?Id=3`。

那问题来了：

---

### ❓ 那为什么还要用 `URLSearchParams`？

因为 **`URLSearchParams` 更安全、更强大、更易维护。**

---

### 📌 **优点对比分析：**

| 方式                   | 优点                                              | 缺点                                                  |
| -------------------- | ----------------------------------------------- | --------------------------------------------------- |
| 字符串拼接方式              | 简单、快速                                           | ❌ 不安全，容易出错 <br> ❌ 对特殊字符（如空格、&、=）不编码 <br> ❌ 参数多时可读性差 |
| `URLSearchParams` 方式 | ✅ 自动对参数编码（防止乱码或语法错误） <br> ✅ 可传多个参数 <br> ✅ 更符合标准 | 写法稍微复杂一点                                            |

---

### 📍 举个实际例子：

#### 1. 字符串拼接：

```ts
const keyword = 'vue+react';
const url = `/search?query=${keyword}`;
```

结果：

```
/search?query=vue+react
```

但浏览器解析可能把 `+` 解释成空格（不是你想要的意思）！

---

#### 2. 使用 `URLSearchParams`：

```ts
const keyword = 'vue+react';
const params = new URLSearchParams({ query: keyword });
const url = `/search?${params.toString()}`
```

结果：

```
/search?query=vue%2Breact
```

✅ `+` 自动转成 `%2B`，这样就不会出错了，**更安全！**

---

### 🔁 多参数也很方便：

```ts
const params = new URLSearchParams({
  id: '3',
  name: '张三',
  role: 'admin'
});
const url = `/user?${params.toString()}`
```

结果：

```
/user?id=3&name=%E5%BC%A0%E4%B8%89&role=admin
```

✅ 自动转义中文！

---

### ✅ 总结

| 为什么用 `URLSearchParams`？ |
| ----------------------- |
| ✅ 更安全（防止语法错误）           |
| ✅ 自动编码参数                |
| ✅ 结构清晰，便于维护             |
| ✅ 支持多个参数                |

---

如果你只是快速传一个参数，简单拼接也行。但**在真实项目中，推荐用 `URLSearchParams` 来构建 URL 查询字符串**，尤其是涉及到多个参数、中文、特殊字符时。

