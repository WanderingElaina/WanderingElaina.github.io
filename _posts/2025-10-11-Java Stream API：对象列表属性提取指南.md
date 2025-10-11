# Java Stream API：对象列表属性提取指南

## 📋 核心概念
**Stream API** 是 Java 8 引入的函数式编程特性，用于高效处理集合数据。

## 🎯 核心代码示例
```java
List<Long> ids = users.stream()           // 创建流
        .map(User::getId)                 // 映射转换
        .collect(Collectors.toList());    // 收集结果
```

## 🔄 代码执行流程

### 三步操作流程：
```
List<User> → Stream<User> → Stream<Long> → List<Long>
     ↓             ↓             ↓           ↓
  原始列表    →   创建流    →   提取ID   →   收集结果
```

### 详细步骤解析：
1. **`.stream()`** - 将集合转换为顺序流
2. **`.map(User::getId)`** - 提取每个User对象的id属性
   - 方法引用 `User::getId` 等价于 `user -> user.getId()`
3. **`.collect(Collectors.toList())`** - 将流元素收集到新List中

## 💡 等价写法对比

### Stream方式（推荐）：
```java
List<Long> ids = users.stream()
        .map(User::getId)
        .collect(Collectors.toList());
```

### 传统for循环方式：
```java
List<Long> ids = new ArrayList<>();
for (User user : users) {
    ids.add(user.getId());
}
```

### Lambda表达式方式：
```java
List<Long> ids = users.stream()
        .map(user -> user.getId())  // 显式Lambda
        .collect(Collectors.toList());
```

## ⚡ Stream API优势

### 1. **代码简洁性**
- 函数式编程风格
- 链式调用，逻辑清晰
- 减少样板代码

### 2. **强大的链式操作**
```java
List<Long> activeUserIds = users.stream()
        .filter(user -> "ACTIVE".equals(user.getStatus()))  // 过滤
        .map(User::getId)                                   // 转换
        .sorted()                                           // 排序
        .distinct()                                         // 去重
        .collect(Collectors.toList());                      // 收集
```

### 3. **空值安全处理**
```java
List<Long> safeIds = users.stream()
        .filter(Objects::nonNull)           // 过滤null对象
        .map(User::getId)
        .filter(Objects::nonNull)           // 过滤null ID
        .collect(Collectors.toList());
```

## 🛠️ 扩展应用场景

### 提取到不同数据结构
```java
// 到Set（自动去重）
Set<Long> idSet = users.stream()
        .map(User::getId)
        .collect(Collectors.toSet());

// 到数组
Long[] idArray = users.stream()
        .map(User::getId)
        .toArray(Long[]::new);

// 到Map
Map<Long, User> userMap = users.stream()
        .collect(Collectors.toMap(User::getId, Function.identity()));
```

### 并行处理优化
```java
// 大数据量时使用并行流
List<Long> ids = users.parallelStream()
        .map(User::getId)
        .collect(Collectors.toList());
```

### 复杂条件提取
```java
// 多条件筛选后提取
List<Long> qualifiedIds = users.stream()
        .filter(user -> user.getAge() > 18)
        .filter(user -> "ACTIVE".equals(user.getStatus()))
        .map(User::getId)
        .collect(Collectors.toList());
```

## 📝 最佳实践建议

1. **优先使用方法引用** - `User::getId` 比 `user -> user.getId()` 更简洁
2. **注意空值处理** - 使用 `filter` 提前过滤null值
3. **考虑性能影响** - 小数据集用顺序流，大数据集考虑并行流
4. **保持可读性** - 复杂的链式操作可以适当换行和注释

## 🎯 总结要点

- **Stream API** 提供声明式的集合处理方式
- **`map`** 操作用于数据转换和属性提取
- **方法引用** 让代码更简洁易读
- **链式操作** 支持复杂的数据处理流程
- 适合替换传统的for循环，提升代码质量和可维护性

---

*此笔记记录时间：2024年 | Java Stream API 属性提取技巧*
