
---

# 📘 MyBatis + LEFT JOIN 字段“融合/覆盖”问题笔记

## 一、核心结论（先记住）

> **数据库层不会融合字段**
> **“看起来融合”只会发生在 MyBatis 映射阶段**
> **根因：SELECT 同名字段 + resultMap 映射到同一个 property**

---

## 二、JOIN 后字段到底会不会融合？

### ❌ 不会（SQL 层）

* 即使：

  * `ctp` 是 `utp` 的快照来源
  * 字段名、含义、值完全一致
* 在 SQL 看来，它们依然是 **两列**

例如：

```sql
ctp.plan_name
utp.plan_name
```

👉 永远是两列，不会自动合并

---

## 三、为什么“看起来像融合了”？（真正原因）

### 1️⃣ SQL 返回结果集阶段

```sql
SELECT
    ctp.plan_name,
    utp.plan_name
```

数据库返回的是：

| plan_name | plan_name |
| --------- | --------- |
| 模板名       | 用户名       |

数据库 **不关心你怎么用**

---

### 2️⃣ MyBatis 映射阶段（问题发生在这里）

```xml
<result column="plan_name" property="courseName"/>
```

* SQL 里有 **两个 plan_name**
* MyBatis 按列顺序映射
* **后面的覆盖前面的**

等价于：

```
ctp.plan_name → courseName
utp.plan_name → courseName（覆盖）
```

👉 这不是融合，是**覆盖**

---

## 四、正确做法（标准解法）

### 1️⃣ SQL 中必须起别名（区分来源）

```sql
SELECT
    ctp.plan_name AS ctp_plan_name,
    utp.plan_name AS utp_plan_name
```

---

### 2️⃣ resultMap 中明确映射

```xml
<result column="ctp_plan_name" property="templateCourseName"/>
<result column="utp_plan_name" property="courseName"/>
```

✅ 不歧义
✅ 不覆盖
✅ 语义清晰

---

## 五、为什么 WHERE / ON 不会有这个问题？

### 正确写法

```sql
WHERE ctp.plan_name = ?
```

* `表别名.字段名` 在 **SQL 编译期就确定**
* 不存在“用哪个字段”的问题

### 错误写法

```sql
WHERE plan_name = ?
```

👉 数据库会直接报错：

```
Column 'plan_name' is ambiguous
```

---

## 六、LEFT JOIN 的关键规则（顺便记）

### 1️⃣ 以谁为主？

```sql
FROM ctp
LEFT JOIN utp ON ...
```

👉 **ctp 是主表**

含义是：

* ctp 的行一定保留
* utp 匹配不到 → utp 字段为 NULL

---

### 2️⃣ JOIN 只拼行，不拼列

> JOIN 的作用：
> **扩展行，不合并字段**

字段永远属于原表：

* `ctp.xxx` 永远是 ctp 的
* `utp.xxx` 永远是 utp 的

---

## 七、快照表设计的正确理解（你的场景）

| 表   | 角色        |
| --- | --------- |
| ctp | 模板 / 原始定义 |
| utp | 用户态快照（可变） |

* 字段可以同名
* 语义上有关联
* **SQL / ORM 层面绝不自动融合**

---

## 八、一个“永不踩坑”的铁律 ⭐⭐⭐

> **只要 JOIN 且存在同名字段：**
>
> 1. SQL 必须起别名
> 2. VO 字段必须唯一
> 3. resultMap 必须显式 column → property
>
> 👉 否则一定会被 MyBatis 覆盖

---

## 九、一句话终极总结（背这个）

> **SQL 负责“算对”**
> **JOIN 负责“拼行”**
> **MyBatis 负责“接对象”**
> **问题 90% 出在映射，不在 JOIN**

---

如果你愿意，下一步我可以帮你把这套内容**整理成一份「JOIN 查询命名规范模板」**（直接可复制到项目 Wiki）。
