

来看你这段代码：

```java
if (StringUtils.hasText(param.getKeyword())) {
    wrapper.and(w -> w.like(Devices::getDeviceName, param.getKeyword())
                      .or()
                      .like(Devices::getDeviceCode, param.getKeyword()));
}
```

---

### ✅ 实际 SQL 生成逻辑

假设前面已经有别的条件，比如：

```java
wrapper.eq(Devices::getStatus, 1);
```

那么这段 `.and(...)` 生成的 SQL 大致如下：

```sql
WHERE status = 1 
  AND (device_name LIKE '%xxx%' OR device_code LIKE '%xxx%')
```

也就是说，`.and(w -> ...)` 会**自动加上括号**，
生成的结果相当于：

> `AND (A OR B)`

而不是：

> `AND A OR B`

---

### ⚠️ 如果你不用 `.and()` 而直接写：

```java
wrapper.like(Devices::getDeviceName, param.getKeyword())
        .or()
        .like(Devices::getDeviceCode, param.getKeyword());
```

那生成的 SQL 是：

```sql
WHERE device_name LIKE '%xxx%' OR device_code LIKE '%xxx%'
```

👉 **这就会导致 or 提前“断开前面条件”**，
比如：

```java
wrapper.eq(Devices::getStatus, 1)
        .like(Devices::getDeviceName, param.getKeyword())
        .or()
        .like(Devices::getDeviceCode, param.getKeyword());
```

会变成：

```sql
WHERE (status = 1 AND device_name LIKE '%xxx%') OR device_code LIKE '%xxx%'
```

这样就错了 —— 因为它会让 `device_code` 匹配的结果**无视 status 条件**！

---

### ✅ 所以你的写法是对的

```java
wrapper.and(w -> w.like(Devices::getDeviceName, param.getKeyword())
                  .or()
                  .like(Devices::getDeviceCode, param.getKeyword()));
```

✔️ 自动生成带括号的 `(A OR B)`，
✔️ 不会破坏前面的 AND 条件逻辑。

---

### ✅ 补充建议

如果你希望语义更清晰一点，也可以改写成：

```java
wrapper.and(q -> q
    .nested(n -> n.like(Devices::getDeviceName, param.getKeyword())
                  .or()
                  .like(Devices::getDeviceCode, param.getKeyword()))
);
```

效果一样，只是显式地告诉 MyBatis-Plus：“这一段是一个子条件块”。

---

**总结一句话：**
✅ `.and(w -> w.like(...).or().like(...))` —— ✅ 正确，括号保护逻辑。
❌ `.or().like(...)` 在主链路上使用 —— ❌ 容易导致条件提前断开。
