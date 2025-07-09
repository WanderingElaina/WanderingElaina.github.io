---
layout: post_layout
title: MyBatis-Plus小知识点
time: 2025年06月23日 星期三
location: cq
pulished: true
excerpt_separator: "##"
---


**写了 @TableId(type = IdType.AUTO) 并且数据库设置了 AUTO_INCREMENT，才可以忽略前端传的 id**


我们来**彻底讲清楚 MyBatis-Plus 的机制**，让你彻底懂。

---

## ✅ 核心点：MyBatis-Plus 的 `updateById(goods)` 是根据 `goods.getId()` 来更新的

也就是说：**你不需要在方法中显式地去“获取”ID，只要 `goods` 对象里有 `id` 字段，它就知道该更新哪一条！**

---

## ✅ 背后发生了什么？

MyBatis-Plus 的 `updateById(T entity)` 方法会：

1. **拿到 `entity.getId()`**

2. 自动构造 SQL 类似：

   ```sql
   UPDATE sys_goods SET xxx=xxx, yyy=yyy WHERE id = #{id}
   ```

3. 所以只要 `goods` 中的 `id` 不为 `null`，它就能根据主键更新对应记录。

---

## ✅ 举个例子你就秒懂

假设你有如下实体类：

```java
@Data
@TableName("sys_goods")
public class SysGoods {
    @TableId(type = IdType.AUTO)
    private Long id; // 主键

    private String name;
    private String goodsPic1;
    private BigDecimal price;
    private LocalDateTime updateTime;
}
```

你前端传来如下请求（Multipart 表单）：

```http
POST /edit/test1
Content-Type: multipart/form-data

id=1001
name=连衣裙
file1=xxx.jpg
...
```

SpringMVC 会自动把这些参数封装到你的 `SysGoods goods` 对象里：

```java
goods.getId() => 1001L
goods.getName() => "连衣裙"
```

此时你执行：

```java
goodsService.updateById(goods);
```

MyBatis-Plus 会执行：

```sql
UPDATE sys_goods
SET name = '连衣裙', goods_pic1 = 'xxx.jpg', ...
WHERE id = 1001;
```

✔ 就这么简单！

---

## ✅ 重点总结

| 你担心的问题               | 实际情况                                  |
| -------------------- | ------------------------------------- |
| 没有调用 `goods.getId()` | MyBatis-Plus 自动调用内部的 `getId()`        |
| 看不到显式获取 ID 的代码       | 因为 SpringMVC 自动封装 + MyBatis-Plus 内部机制 |
| ID 从哪来的？             | 从前端传参封装到 `goods` 实体中                  |

---
---

## ✅ 检查建议

为了保证 `.updateById(goods)` 有效，请确认两点：

1. ✅ `goods.getId()` 不为 null，前端确实传了 id（作为隐藏字段或表单参数）
2. ✅ `@TableId` 注解写在实体类的 id 字段上

---

## ❗如果你真的没传 ID，会怎样？

那 `goods.getId()` 是 null：

```java
goodsService.updateById(goods); // 不会报错，但不会更新任何数据
```

可以在代码中加一句断言来提前报错：

```java
if (goods.getId() == null) {
    return ResultUtil.error("ID 不能为空，无法更新数据");
}
```

---

好兄弟，你现在已经基本掌握了关键点了，就差这临门一脚的理解，我来帮你彻底搞懂这个问题：

---

## ✅ 结论先说：

**只要：**

* 实体类的主键字段加了 `@TableId`
* 并且在调用 `updateById(goods)` 时，`goods.getId()` 不为 null

👉 **那么 `updateById(goods)` 就会生效，无论 `@TableId` 注解里写没写内容。**

---

## 🧠 原因解析：

### 1. `@TableId` 是什么？

它是 **MyBatis-Plus 的主键识别注解**，告诉框架：

> “嘿！这个字段是数据库表的主键字段。”

比如：

```java
@TableId
private Long id;
```

就是告诉 MyBatis-Plus：这是主键。

---

### 2. 那 `@TableId(type = IdType.AUTO)` 又是干嘛的？

它只是告诉框架 **主键生成策略**，通常用于插入（`insert()`）时：

| 类型                 | 说明                                     |
| ------------------ | -------------------------------------- |
| `IdType.AUTO`      | 主键自增，由数据库生成（如 MySQL 的 AUTO\_INCREMENT） |
| `IdType.INPUT`     | 主键你自己传，框架不管                            |
| `IdType.ASSIGN_ID` | 自动生成分布式ID（雪花算法，默认值）                    |
| `IdType.NONE`      | 不处理主键（默认无处理）                           |

👆这些类型 **只在插入时有影响，跟 `updateById` 没关系。**

---

## ✅ 回答你的问题：

| 你问的问题                      | 结论                                                   |
| -------------------------- | ---------------------------------------------------- |
| `@TableId` 写了就有效吗？         | ✔ 是的，只要有 `@TableId` 注解，MyBatis-Plus 知道哪个字段是主键        |
| 注解里写什么都可以吗？                | ✔ 是的，哪怕什么都不写 `@TableId` 也可以用于更新，只要 ID 不为 null        |
| 和 `updateById(goods)` 有关吗？ | ✔ 有关。`updateById()` 就是根据加了 `@TableId` 的字段的值来构造 SQL 的 |
| `type = IdType.AUTO` 有影响吗？ | ❌ 对更新没有影响，只在插入（`insert`）时有用                          |

---

## ✅ 实战小总结

### 推荐写法（清晰、安全）：

```java
@TableId(type = IdType.AUTO)
private Long id;
```

* 插入时自动自增
* 更新时按 ID 更新（只要 ID 不为 null）

---

好兄弟，给你画个简明版的 **MyBatis-Plus updateById 流程图**，帮你梳理清楚从调用到执行的过程：

---

```plaintext
┌─────────────────────────────┐
│         前端发送请求         │
│  (Multipart 或 JSON 包含 id) │
└─────────────┬───────────────┘
              │
              ▼
┌─────────────────────────────┐
│ SpringMVC 自动封装请求参数    │
│   封装成 SysGoods 实体对象    │
│    （goods.getId 有值）       │
└─────────────┬───────────────┘
              │
              ▼
┌─────────────────────────────┐
│     控制器调用 updateById     │
│ goodsService.updateById(goods)│
└─────────────┬───────────────┘
              │
              ▼
┌─────────────────────────────┐
│ MyBatis-Plus 根据实体类的      │
│ @TableId 注解找到主键字段      │
│ 并获取主键值 goods.getId()    │
└─────────────┬───────────────┘
              │
              ▼
┌─────────────────────────────┐
│ 构建 SQL 语句：              │
│ UPDATE sys_goods SET ...     │
│ WHERE id = #{goods.getId()}  │
└─────────────┬───────────────┘
              │
              ▼
┌─────────────────────────────┐
│ 执行 SQL 更新数据库对应行     │
│ 若 ID 有效且存在该条记录，则  │
│ 数据被更新成功               │
└─────────────┬───────────────┘
              │
              ▼
┌─────────────────────────────┐
│ 方法返回更新结果，控制器返回  │
│ 成功响应给前端               │
└─────────────────────────────┘
```

---

### 重点复习：

* **前端必须传入 id**，保证 `goods.getId()` 有值
* 实体类中要用 `@TableId` 标注主键字段
* `updateById` 内部自动用该 id 来生成 SQL 的 `WHERE` 条件
* 更新操作才会针对该 id 进行数据库修改

---
---

这个 `queryWrapper.orderBy(...)` 是 MyBatis-Plus 中用于**动态排序**的写法。
我给你**逐个参数解释清楚**：

---

### 🟢 方法签名

先看下 `orderBy` 方法的原型：

```java
public QueryWrapper<T> orderBy(boolean condition, boolean isAsc, String... columns)
```

参数解释：

1. `condition`: 是否执行排序（`true` 表示一定排序，`false` 表示忽略排序）
2. `isAsc`: 排序方向（`true` 升序，`false` 降序）
3. `columns`: 要排序的列

---

### 🟢 这段代码

```java
queryWrapper.orderBy(
    true,
    pageDTO.getIsAsc().equals("asc"),
    ToolsUtils.camelToUnderline(pageDTO.getOrderByColumn())
);
```

逐个拆解：
✅ **第一个参数** `true`

* 永远执行排序。

✅ **第二个参数** `pageDTO.getIsAsc().equals("asc")`

* `pageDTO.getIsAsc()` 应该是个字符串 `"asc"` 或 `"desc"`。
* 判断是否是 `"asc"`，是则返回 `true`（升序），否则 `false`（降序）。

✅ **第三个参数** `ToolsUtils.camelToUnderline(pageDTO.getOrderByColumn())`

* `pageDTO.getOrderByColumn()` 拿到你想排序的字段名（一般是Java驼峰命名）。
* `camelToUnderline()` 把 `userName` 转成 `user_name`（数据库字段）。

  > 比如：`createTime` -> `create_time`

---

### 🟢 举个例子

假设：

```java
pageDTO.getIsAsc() = "asc"
pageDTO.getOrderByColumn() = "createTime"
```

则执行：

```java
orderBy(
    true,
    true,           // 升序
    "create_time"   // 数据库字段
)
```

相当于 SQL:

```sql
ORDER BY create_time ASC
```

如果：

```java
pageDTO.getIsAsc() = "desc"
pageDTO.getOrderByColumn() = "userName"
```

则：

```sql
ORDER BY user_name DESC
```

---

### 🟢 总结作用

**动态排序封装**：

* 根据前端传参（排序列 + 排序方向）灵活拼接 `ORDER BY`。
* 不用写很多 if-else 手动判断。

---
---

非常好兄弟，这行代码其实是 **MyBatis-Plus** 里非常常用的条件构造器用法，我来**逐句拆开给你讲清楚：**

---

## 🟢 先看完整写法

```java
wrapperStock.in(GoodsStockDetail::getGoodsId, goods.getId());
```

这里用到了：
✅ `QueryWrapper` 或 `LambdaQueryWrapper`
✅ `in()` 方法

---

## 🟢 逐个分析

### 🟢 1. `wrapperStock`

这个就是条件构造器：

```java
LambdaQueryWrapper<GoodsStockDetail> wrapperStock = new LambdaQueryWrapper<>();
```

它用来动态拼SQL。

---

### 🟢 2. `in()` 方法

```java
wrapperStock.in(column, value);
```

翻译成SQL：

```sql
WHERE column IN (value1, value2, ...)
```

✅ 也就是说：**只要某一行的 `column` 值在这个列表里，就会被查出来**。

---

### 🟢 3. `GoodsStockDetail::getGoodsId`

这个是**Lambda列选择器**，表示：

> **`goods_id`字段**

它等价于：

```java
"in('goods_id', ...)"
```

✅ 它的好处：

* 不用写字符串，编译安全
* 如果表字段改了，不会写错

---

### 🟢 4. `goods.getId()`

这个是你传进去的值：

* `goods`是你循环时的对象
* `goods.getId()`返回一个Long

---

✅ **但是这里有一个细节：**

> `in()`方法期望的是一个集合（List/Set/数组），如果你直接传一个单个值，它就等价于：

```sql
WHERE goods_id IN (单个值)
```

也就是：

```sql
WHERE goods_id = xxx
```

这个完全合法。

---

## 🟢 实际等效SQL

假如 `goods.getId() == 5`：

```sql
WHERE goods_id IN (5)
```

就是：

```sql
WHERE goods_id = 5
```

---

## 🟢 举个更常用的例子

如果你想查多个ID：

```java
List<Long> idList = Arrays.asList(1L, 2L, 3L);
wrapperStock.in(GoodsStockDetail::getGoodsId, idList);
```

SQL:

```sql
WHERE goods_id IN (1,2,3)
```

---

✅ 如果你只要查一个ID，也可以直接：

```java
wrapperStock.eq(GoodsStockDetail::getGoodsId, goods.getId());
```

**和 `in()` + 单个值效果完全一样**。

---

## 🟢 小总结

这行：

```java
wrapperStock.in(GoodsStockDetail::getGoodsId, goods.getId());
```

意思是：
✅ 查 `goods_id`等于 `goods.getId()` 的记录。

如果 `getId()` 是单个值：
👉 相当于 `eq()`。

如果是集合：
👉 就是`IN`条件。

---

**所以：**
如果只查一个ID，更推荐：

```java
wrapperStock.eq(GoodsStockDetail::getGoodsId, goods.getId());
```

✅ 语义更清晰。

---


其实就是**把你查到的库存明细放到当前商品对象里**，我来一步一步详细拆开讲：

---

先看完整代码：

```java
goods.setStockDetails(
    goodsStockDetailMapper.selectList(wrapperStock)
);
```

✅ 逐步解释：

---

## 🟢 第一步：`goodsStockDetailMapper.selectList(wrapperStock)`

这是**MyBatis-Plus Mapper的查询方法**：

```java
selectList(QueryWrapper)
```

它做的事情是：

> **根据wrapper条件去数据库查询所有匹配的行，返回一个List。**

举个例子：

假设你 wrapper 条件：

```java
wrapperStock.eq("goods_id", 5)
```

对应SQL:

```sql
SELECT * FROM tb_goods_stock_detail WHERE goods_id = 5
```

返回结果：

```java
List<GoodsStockDetail> detailList
```

里面放的就是所有库存明细。

---

---

## 🟢 第二步：把查询结果放到商品里

```java
goods.setStockDetails(...)
```

`goods` 是你的 `SysGoods` 对象，
它有一个属性：

```java
private List<GoodsStockDetail> stockDetails;
```

这个 `setStockDetails()` 就是把刚才查到的库存明细list**赋值到这个属性**。

---

---

✅ 所以这句话：

```java
goods.setStockDetails(goodsStockDetailMapper.selectList(wrapperStock));
```

意思就是：

> “把当前商品 `goods` 所有库存明细查出来，放到它自己的 `stockDetails` 列表里。”

---

---

## 🟢 举个通俗的例子

假如数据库里：

| id | goods\_id | 库存数量 |
| -- | --------- | ---- |
| 1  | 5         | 10   |
| 2  | 5         | 20   |

执行：

```java
goodsStockDetailMapper.selectList(wrapperStock);
```

返回：

```java
[
  GoodsStockDetail(id=1, goods_id=5, 库存数量=10),
  GoodsStockDetail(id=2, goods_id=5, 库存数量=20)
]
```

然后：

```java
goods.setStockDetails(这个List);
```

**此时 `goods`对象就带有自己的库存明细了。**

---

✅ 前端在获取这个 `SysGoods`时，就能看到：

```json
{
  "id":5,
  "name":"xxx",
  "stockDetails":[
    { "id":1, "goodsId":5, "数量":10 },
    { "id":2, "goodsId":5, "数量":20 }
  ]
}
```

---

## 🟢 小总结

**这句代码做的事：**

> “把这个商品的所有库存明细一次性查出来，塞到商品里。”

**作用：**
✅ 前端可以直接拿到商品+它的库存列表。

---




