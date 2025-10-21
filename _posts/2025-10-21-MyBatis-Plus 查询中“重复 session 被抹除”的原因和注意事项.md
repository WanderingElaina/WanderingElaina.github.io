 **MyBatis-Plus 查询中“重复 session 被抹除”的原因和注意事项**

---

# 📝 MyBatis-Plus 查询重复记录处理笔记模板

## 1️⃣ 场景描述

* 查询课程计划下所有课节的训练（session），可能存在：

  * `section_sessions` 表中 **同一个 `section_id` 对应多个 `session_id`**
  * 同一个 `session_id` 在多条记录中重复出现

* 示例代码：

```java
List<SectionSessions> relations = sectionSessionsMapper.selectList(
    Wrappers.<SectionSessions>lambdaQuery()
        .eq(SectionSessions::getSectionId, section.getId())
        .orderByAsc(SectionSessions::getSessionOrder)
);
List<Long> sessionIds = relations.stream().map(SectionSessions::getSessionId).toList();
List<CourseSessions> sessions = courseSessionsMapper.selectBatchIds(sessionIds);
```

---

## 2️⃣ 现象

* 即使 `section_sessions` 中 `session_id` 有重复，最终返回的 `sessions` **重复的 session 消失**
* 数据库查询结果只保留每个 `session_id` 对应的唯一记录

---

## 3️⃣ 原因分析

| 步骤                                     | 是否去重  | 原因                           |
| -------------------------------------- | ----- | ---------------------------- |
| `relations.stream().map(...).toList()` | ❌ 不去重 | 只是映射成 `List<Long>`，重复值保留     |
| `selectBatchIds(sessionIds)`           | ✅ 去重  | SQL `IN` 查询天然去重：同一个主键只返回一条记录 |
| `sessions.stream().map(...)`           | ✅     | 因为输入结果已经去重，所以最终 VO 也无重复      |

### 🔹 重点

* SQL `IN` 查询不会返回重复主键行
* `selectBatchIds` 的结果与传入的列表长度可能不一致

---

## 4️⃣ 解决方案 / 注意事项

### 4.1 保留重复 session

* **方法 1**：逐条查询（避免 `selectBatchIds` 自动去重）

```java
List<CourseSessions> sessions = new ArrayList<>();
for (Long sid : sessionIds) {
    CourseSessions s = courseSessionsMapper.selectById(sid);
    if (s != null) sessions.add(s);
}
```

* **方法 2**：在 SQL 层使用自定义查询，不使用主键 IN 查询

### 4.2 去重（如果想手动控制）

* 在 Java 层：

```java
List<Long> distinctIds = sessionIds.stream().distinct().toList();
```

* 或使用 Set：

```java
Set<Long> distinctIds = new HashSet<>(sessionIds);
```

### 4.3 保持顺序

* SQL IN 查询结果不保证顺序，如果顺序重要，需要：

  * 手动排序
  * 或使用 `ORDER BY FIELD(id, …)`（MySQL 特性）

---

## 5️⃣ 小结

* **MyBatis-Plus `selectBatchIds` + SQL IN 查询** → 会导致重复主键被自动抹除
* **Stream 映射 `.map()`** → 不去重
* **保留重复** → 不要使用 `selectBatchIds`，逐条查询或自定义 SQL
* **手动去重** → 可用 `distinct()` 或 `Set`

---

