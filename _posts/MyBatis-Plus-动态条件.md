这是 MyBatis-Plus 中 `QueryWrapper` 的一个用法，
其中我们来**拆解讲解一下**：

---

### 🌟 这行代码

```java
queryWrapper.eq(queryParam.getId() != null, "id", queryParam.getId());
```

### 🌿 先看 `.eq()` 方法

MyBatis-Plus 的 `.eq()` 方法是：

```java
.eq(boolean condition, String column, Object value)
```

它表示：

> **当 `condition` 为 `true` 时，才会追加 `column = value` 这个查询条件。**

否则**什么都不做**，条件不会加到 SQL 中。

---

### 🚀 所以，这一行的含义是：

1. **判断**：

   ```java
   queryParam.getId() != null
   ```

   * 如果 `getId()` 有值（不是 `null`），就需要查询 `id = 某个值`
   * 如果 `getId()` 是 `null`，则不加这个条件

2. **作用**：

   * **动态添加条件**，只有在 `id` 不为空时才拼接 `where id = ?`
   * 避免了手动 `if` 判断：

     ```java
     if (queryParam.getId() != null) {
         queryWrapper.eq("id", queryParam.getId());
     }
     ```
   * 让条件构造更简洁。

---

### 🌈 举个例子

假设 `queryParam.getId()`：

* 是 `10`：

  ```sql
  where id = 10
  ```
* 是 `null`：
  不会加任何 `id` 相关条件。

---

### 🛠️ 总结

这一行代码等价于：

✅ 如果 id 有值：

```java
queryWrapper.eq("id", queryParam.getId());
```

❌ 如果 id 没值：

```java
// 不做任何操作
```

**作用：**
动态拼接 SQL 条件，使查询更灵活。

---
