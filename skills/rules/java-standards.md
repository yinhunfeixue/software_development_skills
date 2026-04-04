---
name: java-standards
description: Java 开发规范，Java 项目适用，在 coding-standards 基础上补充
---

# Java 开发规范

> **适用范围**：所有 Java 项目
> **约束级别说明**：`[MUST]` 强制，`[MUST NOT]` 禁止，`[SHOULD]` 推荐，`[MAY]` 可选
> **通用规范见** `coding-standards`

---

## 1. 默认技术栈

无特殊要求时，**[MUST]** 使用以下默认技术栈：

| 组件 | 版本/选型 |
|------|----------|
| 语言 | Java 17 |
| 框架 | Spring Boot 3.x |
| 构建工具 | Maven |
| ORM | MyBatis-Plus |
| 数据库 | MySQL |
| 缓存 | Redis |

---

## 2. 命名约定

### 2.1 基础命名规则

| 类型 | 规则 | 示例 |
|------|------|------|
| 包 | `[MUST]` 全小写，反域名 | `com.example.user.service` |
| 类/接口/枚举 | `[MUST]` PascalCase | `UserService`、`OrderStatus` |
| 方法/变量 | `[MUST]` camelCase | `getUserById()`、`userName` |
| 常量 | `[MUST]` UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| 泛型参数 | `[MUST]` 单个大写字母 | `T`、`E`、`K`、`V` |
| 测试类 | `[MUST]` 后缀 `Test` 或 `IntegrationTest` | `UserServiceTest` |
| 数据库表 | `[MUST]` snake_case | `user_order` |
| 数据库字段 | `[MUST]` snake_case | `created_at` |

### 2.2 类命名后缀规范

`[MUST]` 按职责使用对应后缀，**[MUST NOT]** 随意混用：

| 后缀 | 职责 |
|------|------|
| `Service` | 业务逻辑层接口 |
| `ServiceImpl` | 业务逻辑层实现 |
| `Controller` | HTTP 控制器 |
| `Mapper` / `Repository` | 数据访问层 |
| `DTO` | 请求/响应数据传输对象 |
| `VO` | 视图对象（返回前端） |
| `Entity` / `PO` | 持久化对象（对应数据库表） |
| `Config` | 配置类 |
| `Interceptor` | 拦截器 |
| `Filter` | 过滤器 |
| `Exception` | 自定义异常 |
| `Converter` | 对象转换器 |
| `Utils` / `Helper` | 工具类 |

---

## 3. 项目分层结构

### 3.1 目录结构

```
src/main/java/com/example/
├── config/          # 配置类（Security、Redis、Swagger 等）
├── controller/      # 控制器，接收请求
├── service/         # 业务逻辑接口
│   └── impl/        # 业务逻辑实现
├── mapper/          # MyBatis Mapper 接口
├── entity/          # 数据库实体
├── dto/             # 请求/响应数据传输对象
├── vo/              # 视图对象（返回前端）
├── converter/       # Entity ↔ DTO ↔ VO 转换
├── common/
│   ├── exception/   # 自定义异常
│   ├── result/      # 统一响应封装
│   ├── enums/       # 公共枚举
│   └── constant/    # 公共常量
├── utils/           # 工具类
└── Application.java # 启动类
```

### 3.2 分层职责与依赖规则

```
Controller → Service → Mapper
    ↓           ↓
   DTO        Entity
    ↓
   VO（返回前端）
```

| 层 | 职责 | 约束 |
|----|------|------|
| Controller | 参数校验、调用 Service、封装返回 | `[MUST NOT]` 包含业务逻辑 |
| Service | 核心业务逻辑、事务控制 | `[MAY]` 调用其他 Service |
| Mapper | 数据访问 | `[MUST NOT]` 包含业务逻辑 |

**[MUST NOT]** 反向依赖：Service 不依赖 Controller，Mapper 不依赖 Service
**[MUST NOT]** 跨层调用：Controller 不直接调用 Mapper

---

## 4. 编码规范

### 4.1 类结构顺序

`[MUST]` 类内成员按以下顺序排列：

1. 静态常量
2. 静态变量
3. 实例变量
4. 构造方法
5. 业务方法（`public` → `protected` → `private`）
6. `toString` / `equals` / `hashCode`

### 4.2 方法规范

- `[MUST]` 单个方法不超过 80 行
- `[MUST]` 参数不超过 5 个，超出使用 DTO 封装
- `[MUST]` 方法只做一件事，职责单一
- `[MUST]` boolean 方法以 `is` / `has` / `can` 开头
- `[MUST]` 返回集合时返回空集合，**[MUST NOT]** 返回 `null`

### 4.3 字段与注入规范

`[MUST NOT]` 使用 `@Autowired` 字段注入，`[MUST]` 使用构造器注入：

```java
// ✗ MUST NOT - 字段注入
@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserMapper userMapper;
}

// ✓ MUST - 构造器注入（使用 Lombok）
@Service
@RequiredArgsConstructor
public class UserServiceImpl implements UserService {
    private final UserMapper userMapper;
}
```

- `[MUST]` 所有实例字段使用 `private`
- `[SHOULD]` 使用 Lombok `@Getter/@Setter` 替代手写 getter/setter

### 4.4 集合使用

```java
// ✓ 接口声明，实现类初始化
List<String> list = new ArrayList<>();
Map<String, Object> map = new HashMap<>(16); // SHOULD 指定初始容量

// ✓ Stream 链式操作
List<String> names = users.stream()
    .filter(u -> u.getAge() > 18)
    .map(User::getName)
    .collect(Collectors.toList());
```

### 4.5 Optional 使用

```java
// ✓ 方法返回值可能为空时用 Optional
public Optional<User> findById(Long id) { ... }

// ✓ 链式取值避免 NPE
String city = optional.map(User::getAddress).map(Address::getCity).orElse("未知");

// ✗ MUST NOT - 不用 Optional 作为参数或字段类型
public void process(Optional<User> user) { ... }
```

---

## 5. 异常处理

### 5.1 异常体系

```
RuntimeException
├── BusinessException     # 业务异常（可预期，用于正常业务流程控制）
│   ├── NotFoundException
│   ├── DuplicateException
│   └── UnauthorizedException
└── SystemException       # 系统异常（不可预期，需告警）
```

### 5.2 异常处理规则

- `[MUST NOT]` 空 catch 块，`[MUST]` 有明确处理或日志记录
- `[MUST NOT]` 捕获 `Exception` / `Throwable`，精确捕获具体类型
- `[MUST]` 业务异常继承 `BusinessException`，携带错误码和提示信息
- `[MUST]` 重新抛出时保留原始堆栈

```java
// ✗ MUST NOT
catch (Exception e) { }

// ✗ MUST NOT - 丢失堆栈
catch (IOException e) {
    throw new SystemException("IO 失败");
}

// ✓ MUST - 保留原始堆栈
catch (IOException e) {
    throw new SystemException("IO 失败", e);
}
```

### 5.3 全局异常处理器

`[MUST]` 使用 `@RestControllerAdvice` 统一处理，Service 层抛出，Handler 层捕获：

```java
@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {

    @ExceptionHandler(BusinessException.class)
    public Result<?> handleBusiness(BusinessException e) {
        log.warn("业务异常: code={}, msg={}", e.getCode(), e.getMessage());
        return Result.fail(e.getCode(), e.getMessage());
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public Result<?> handleValidation(MethodArgumentNotValidException e) {
        String msg = e.getBindingResult().getFieldErrors().stream()
            .map(FieldError::getDefaultMessage)
            .collect(Collectors.joining(", "));
        return Result.fail(ErrorCode.PARAM_INVALID, msg);
    }

    @ExceptionHandler(Exception.class)
    public Result<?> handleUnknown(Exception e) {
        log.error("系统异常", e);
        return Result.fail(ErrorCode.SYSTEM_ERROR, "系统繁忙，请稍后重试");
    }
}
```

### 5.4 错误码规范

- `[MUST]` 格式：`[模块]_[类型]_[序号]`，如 `USER_NOT_FOUND_001`
- `[MUST]` 使用枚举统一管理错误码
- `[MUST]` 前端可见信息使用用户友好文案，内部使用技术描述

```java
public enum ErrorCode {
    SUCCESS(200, "成功"),
    PARAM_INVALID(400, "参数错误"),
    UNAUTHORIZED(401, "未授权"),
    USER_NOT_FOUND_001(404001, "用户不存在"),
    SYSTEM_ERROR(500, "系统繁忙");

    private final int code;
    private final String message;
}
```

---

## 6. 统一响应封装

`[MUST]` 所有 Controller 方法统一返回 `Result<T>`：

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Result<T> {
    private int code;
    private String message;
    private T data;

    public static <T> Result<T> success(T data) {
        return new Result<>(200, "成功", data);
    }

    public static <T> Result<T> fail(ErrorCode errorCode, String message) {
        return new Result<>(errorCode.getCode(), message, null);
    }
}

// Controller 示例
@GetMapping("/{id}")
public Result<UserVO> getUser(@PathVariable Long id) {
    return Result.success(userService.getById(id));
}
```

---

## 7. 参数校验

`[MUST]` Controller 入参使用 JSR 303 注解，**[MUST NOT]** 在 Controller 中手写 if 校验：

```java
// ✓ DTO 上标注校验注解
@Data
public class CreateUserDTO {
    @NotBlank(message = "用户名不能为空")
    @Size(min = 2, max = 20, message = "用户名长度为 2-20 位")
    private String username;

    @NotBlank(message = "邮箱不能为空")
    @Email(message = "邮箱格式不正确")
    private String email;

    @NotNull(message = "年龄不能为空")
    @Min(value = 1, message = "年龄不合法")
    private Integer age;
}

// ✓ Controller 使用 @Valid 触发校验
@PostMapping
public Result<Void> create(@RequestBody @Valid CreateUserDTO dto) {
    userService.create(dto);
    return Result.success(null);
}
```

- `[SHOULD]` Service 层对业务约束做二次校验（如唯一性检查）
- `[SHOULD]` 复杂校验逻辑实现 `ConstraintValidator` 接口

---

## 8. 注释规范

### 8.1 注释原则

- `[MUST]` 注释说明"为什么"，不说"做什么"
- `[MUST NOT]` 写代码自解释的冗余注释
- `[MUST]` 注释与代码保持同步

### 8.2 必须注释的场景

| 场景 | 注释内容 |
|------|----------|
| 类 | 职责和使用场景 |
| 公共方法 | 用途、参数含义、返回值、可能抛出的异常 |
| 复杂算法 | 执行思路和关键步骤 |
| 业务规则 | 对应的产品需求或业务规则 |
| 临时方案 | 标注 TODO 并说明原因和计划 |
| 防御性代码 | 说明为什么需要这个检查 |

### 8.3 Javadoc 模板

`[MUST]` 公共方法使用 Javadoc：

```java
/**
 * 根据用户 ID 查询用户详情。
 *
 * @param userId 用户 ID，不允许为空
 * @return 用户详情 VO
 * @throws NotFoundException 当用户不存在时
 */
public UserVO getUserById(Long userId) { ... }
```

---

## 9. 日志规范

`[MUST]` 使用 SLF4J + Logback，通过 Lombok `@Slf4j` 注入：

### 9.1 日志级别

| 级别 | 使用场景 |
|------|----------|
| `ERROR` | 系统异常，需立即关注和处理 |
| `WARN` | 业务异常或非预期行为，不影响主流程 |
| `INFO` | 关键业务节点（请求入口、核心操作、第三方调用） |
| `DEBUG` | 开发调试信息，生产环境关闭 |

### 9.2 日志规范

```java
// ✓ MUST - 使用占位符
log.info("创建订单, userId={}, orderId={}", userId, orderId);

// ✗ MUST NOT - 字符串拼接
log.info("创建订单, userId=" + userId + ", orderId=" + orderId);

// ✗ MUST NOT - 在循环中打日志
for (Order order : orders) {
    log.info("处理订单: {}", order.getId()); // 可能日志爆炸
}

// ✗ MUST NOT - 打印完整大对象
log.info("用户列表: {}", users); // 大集合只打印摘要

// ✓ SHOULD - 大对象只打印摘要
log.info("用户列表共 {} 条", users.size());

// ✗ MUST NOT - 日志中含敏感信息
log.info("用户登录, password={}", password);
```

---

## 10. 数据库规范

### 10.1 SQL 规范

- `[MUST NOT]` 使用 `SELECT *`，**[MUST]** 明确列出所需字段
- `[MUST NOT]` 对索引字段使用函数操作
- `[MUST]` 大表查询必须带条件且有索引支持
- `[MUST]` 批量操作使用批量插入/更新，**[MUST NOT]** 循环单条操作

```java
// ✗ MUST NOT
SELECT * FROM user WHERE DATE(created_at) = '2024-01-01';

// ✓ MUST
SELECT id, name, email FROM user WHERE created_at >= '2024-01-01 00:00:00'
  AND created_at < '2024-01-02 00:00:00';
```

### 10.2 事务管理

- `[MUST]` `@Transactional` 只标注在 Service 的 `public` 方法上
- `[SHOULD]` 只读操作使用 `@Transactional(readOnly = true)`
- `[MUST NOT]` 事务方法中调用外部接口（HTTP、RPC）
- `[MUST]` 事务范围尽量小，只包含必要的数据库操作

```java
// ✓ MUST
@Transactional
public void createOrder(CreateOrderDTO dto) {
    Order order = orderMapper.insert(...);
    orderItemMapper.batchInsert(dto.getItems());
    // ✗ MUST NOT - 不要在这里调 HTTP 接口
}
```

### 10.3 表结构规范

- `[MUST]` 每个数据库表必须包含 `created_at` 和 `updated_at` 字段
- `[SHOULD]` 逻辑删除优先于物理删除，使用 `deleted` 字段标记
- `[MUST]` 数据库变更使用 Flyway/Liquibase 管理，**[MUST NOT]** 手动修改线上数据库

---

## 11. 安全最佳实践

### 11.1 输入校验

- `[MUST]` 所有外部输入（HTTP 参数、文件上传等）必须校验和清洗
- `[MUST NOT]` 信任前端校验
- `[MUST]` SQL 使用参数化查询（MyBatis `#{}`），**[MUST NOT]** 字符串拼接 SQL

```java
// ✗ MUST NOT - SQL 注入风险
@Select("SELECT * FROM user WHERE name = '${name}'")
User findByName(String name);

// ✓ MUST - 参数化查询
@Select("SELECT * FROM user WHERE name = #{name}")
User findByName(String name);
```

### 11.2 认证与授权

- `[MUST]` 密码使用 BCrypt 哈希存储，**[MUST NOT]** 明文或可逆加密
- `[MUST]` 敏感接口使用权限校验，基于 RBAC 模型
- `[MUST]` Token/JWT 设置合理过期时间，支持刷新机制
- `[SHOULD]` Session 信息存储在 Redis 中，支持主动失效

### 11.3 敏感数据保护

- `[MUST]` 身份证、手机号等敏感数据脱敏后再返回前端
- `[MUST]` 配置文件中的密码、密钥使用加密存储或环境变量
- `[MUST NOT]` 日志中打印密码、Token、密钥等敏感信息
- `[MUST]` 接口返回数据按需暴露，**[MUST NOT]** 返回多余字段

### 11.4 接口安全

- `[SHOULD]` 关键接口（支付、删除等）增加幂等校验，防重复提交
- `[SHOULD]` 高并发接口使用 Redis + 令牌桶限流
- `[MUST]` 输出到前端的内容进行 HTML 转义，防 XSS
- `[MUST]` CORS 严格限制允许的域名和方法

---

## 12. 性能优化

### 12.1 编码层面

- `[SHOULD]` 循环内避免创建对象，提前创建或复用
- `[SHOULD]` 字符串拼接使用 `StringBuilder`（非线程安全场景）
- `[SHOULD]` 使用 `EnumSet` / `EnumMap` 替代基于枚举的 HashMap
- `[SHOULD]` 缓存频繁使用的计算结果

### 12.2 数据库层面

- `[MUST]` 合理使用索引，遵循最左前缀匹配原则
- `[MUST NOT]` N+1 查询，`[MUST]` 使用 JOIN 或批量查询
- `[SHOULD]` 分页查询使用索引覆盖，避免 `COUNT(*)` 全表扫描
- `[SHOULD]` 大数据量使用游标或分批处理

### 12.3 缓存使用

- `[SHOULD]` 热点数据使用 Redis 缓存，设置合理过期时间
- `[SHOULD]` 缓存击穿：使用互斥锁或逻辑过期
- `[SHOULD]` 缓存穿透：对不存在的数据缓存空值或使用布隆过滤器
- `[SHOULD]` 缓存雪崩：过期时间加随机偏移，避免同时失效

### 12.4 异步处理

- `[SHOULD]` 非核心链路操作（发通知、写日志）使用异步
- `[SHOULD]` 使用 `@Async` 或消息队列实现异步
- `[MUST]` 异步任务需有失败重试和补偿机制

---

## 13. 测试规范

- `[MUST]` 单元测试覆盖 Service 层核心逻辑
- `[MUST]` 使用 JUnit 5 + Mockito
- `[MUST]` 测试方法命名：`should_预期行为_when_条件`
- `[SHOULD]` Controller 层使用 MockMvc 做集成测试
- `[MUST]` 关键业务路径测试覆盖率不低于 80%
- `[MUST]` 测试数据使用 `@BeforeEach` 初始化，**[MUST NOT]** 依赖执行顺序

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserMapper userMapper;

    @InjectMocks
    private UserServiceImpl userService;

    @Test
    void should_throwNotFoundException_when_userNotExists() {
        // given
        when(userMapper.selectById(999L)).thenReturn(null);

        // when & then
        assertThrows(NotFoundException.class, () -> userService.getById(999L));
    }
}
```

---

## 14. 第三方依赖管理

- `[MUST]` 依赖版本统一在 `dependencyManagement` 中管理
- `[MUST]` 引入新依赖前评估：社区活跃度、维护状态、安全漏洞
- `[SHOULD]` 优先使用 Spring Boot 官方管理的依赖版本
- `[MUST NOT]` 引入功能重叠的依赖库
