
## @ApiModelProperty 注解详解

### 完整代码示例
```java
@ApiModelProperty(value = "怀孕次数", example = "2")
private Integer pregnancyCount;
```

---

## 1. 注解解析

### `@ApiModelProperty`
- **来源**：Swagger 框架（现为 OpenAPI 规范）
- **包名**：`io.swagger.annotations.ApiModelProperty`
- **作用**：为 API 模型的属性提供元数据描述

### `value` 属性
- **作用**：属性的描述信息
- **显示位置**：Swagger UI 的字段说明部分
- **示例**：`"怀孕次数"` → 说明这个字段表示怀孕次数

### `example` 属性
- **作用**：提供字段的示例值
- **显示位置**：
  - Swagger UI 的 Example Value 区域
  - 接口文档的请求/响应示例
- **示例**：`"2"` → 显示数字 2 作为示例

---

## 2. 实际影响与作用

### 在 Swagger UI 中的显示效果

#### 模型定义部分
```json
{
  "pregnancyCount": "怀孕次数",
  "type": "integer",
  "example": 2
}
```

#### 请求示例显示
```json
{
  "pregnancyCount": 2,
  "otherField": "其他值"
}
```

#### 接口文档效果
```
参数名称: pregnancyCount
参数说明: 怀孕次数
数据类型: integer
示例值: 2
```

---

## 3. example 的重要性

### 1. **指导前端开发**
```java
// 清晰的示例让前端知道期望的数据格式
@ApiModelProperty(value = "用户邮箱", example = "user@example.com")
private String email;

@ApiModelProperty(value = "出生日期", example = "1990-01-15")
private LocalDate birthDate;

@ApiModelProperty(value = "是否已婚", example = "true")
private Boolean married;
```

### 2. **避免歧义**
```java
// 模糊的配置
@ApiModelProperty(value = "状态")
private Integer status; // 前端不知道传什么值

// 清晰的配置
@ApiModelProperty(value = "状态: 1-正常, 2-禁用", example = "1")
private Integer status;
```

### 3. **数据类型明确**
```java
// 字符串数字 vs 实际数字
@ApiModelProperty(value = "年龄", example = "25")      // 前端可能传 "25"（字符串）
private Integer age;

@ApiModelProperty(value = "年龄", example = "25")      // 明确应该是数字
private Integer age;
```

---

## 4. 完整使用示例

### 实体类配置
```java
@Data
@ApiModel("用户信息更新请求")
public class UserUpdateDTO {
    
    @ApiModelProperty(value = "用户姓名", example = "张三")
    private String name;
    
    @ApiModelProperty(value = "电子邮箱", example = "zhangsan@example.com")
    private String email;
    
    @ApiModelProperty(value = "年龄", example = "25")
    private Integer age;
    
    @ApiModelProperty(value = "怀孕次数", example = "2")
    private Integer pregnancyCount;
    
    @ApiModelProperty(value = "身高(米)", example = "1.65")
    private Double height;
    
    @ApiModelProperty(value = "是否已婚", example = "true")
    private Boolean married;
    
    @ApiModelProperty(value = "出生日期", example = "1990-05-15")
    private LocalDate birthDate;
    
    @ApiModelProperty(value = "标签列表", example = "[\"VIP\", \"新用户\"]")
    private List<String> tags;
}
```

### Controller 中使用
```java
@RestController
@Api(tags = "用户管理")
@RequestMapping("/api/users")
public class UserController {
    
    @PostMapping("/update")
    @ApiOperation("更新用户信息")
    public ResponseEntity<User> updateUser(
            @ApiParam(value = "用户ID", example = "123", required = true)
            @RequestParam Long userId,
            
            @RequestBody UserUpdateDTO updateDTO) {
        // 业务逻辑
        return ResponseEntity.ok(userService.updateUser(userId, updateDTO));
    }
}
```

---

## 5. 生成的 Swagger 文档效果

### Model Schema
```json
UserUpdateDTO {
  name*: string
  example: 张三
  email: string
  example: zhangsan@example.com
  age: integer($int32)
  example: 25
  pregnancyCount: integer($int32)
  example: 2
  height: number($double)
  example: 1.65
  married: boolean
  example: true
  birthDate: string($date)
  example: 1990-05-15
  tags: [string]
  example: ["VIP", "新用户"]
}
```

### 请求示例
```json
{
  "name": "张三",
  "email": "zhangsan@example.com",
  "age": 25,
  "pregnancyCount": 2,
  "height": 1.65,
  "married": true,
  "birthDate": "1990-05-15",
  "tags": ["VIP", "新用户"]
}
```

---

## 6. 最佳实践

### 1. **example 要与数据类型匹配**
```java
// 正确
@ApiModelProperty(value = "次数", example = "5")
private Integer count;

// 可能有问题 - 前端可能困惑是传字符串还是数字
@ApiModelProperty(value = "次数", example = "5")
private String count;
```

### 2. **枚举类型要明确**
```java
@ApiModelProperty(value = "性别: MALE-男, FEMALE-女", example = "MALE")
private String gender;

// 或者使用枚举
@ApiModelProperty(value = "性别", example = "MALE")
private GenderEnum gender;
```

### 3. **复杂对象示例**
```java
@ApiModelProperty(value = "地址信息", example = "{\"province\":\"广东省\",\"city\":\"深圳市\"}")
private String addressInfo;
```

### 4. **必填字段标记**
```java
@ApiModelProperty(value = "用户名", example = "zhangsan", required = true)
private String username;
```

---

## 7. 注意事项

### 对接口的实际影响
- **❌ 不影响**：业务逻辑验证、数据持久化、实际接口行为
- **✅ 影响**：接口文档展示、前端开发参考、测试数据准备

### 数据类型一致性
```java
// 可能产生误导
@ApiModelProperty(value = "怀孕次数", example = "两次")  // 示例是字符串
private Integer pregnancyCount;                         // 但字段是整型

// 推荐做法
@ApiModelProperty(value = "怀孕次数", example = "2")    // 示例与类型一致
private Integer pregnancyCount;
```

### 版本兼容性
- Swagger 2.x 使用 `@ApiModelProperty`
- OpenAPI 3.x 也可以使用 `@Schema` 注解
```java
// OpenAPI 3.x 的替代写法
@Schema(description = "怀孕次数", example = "2")
private Integer pregnancyCount;
```

---

## 总结

`@ApiModelProperty` 的 **example 属性非常重要**，它：

1. **提供清晰的接口使用指导**
2. **减少前后端沟通成本**
3. **提高接口文档的可用性**
4. **帮助生成准确的测试数据**

在你的例子中，`example = "2"` 明确告诉前端开发者：这个字段应该传递整数值 2，而不是字符串 "2" 或其他格式，大大提升了接口的易用性。
