## 核心与组件注册

| 注解                     | 作用位置  | 核心说明与典型用法                                           |
| ------------------------ | --------- | ------------------------------------------------------------ |
| `@SpringBootApplication` | 启动类    | 复合注解，包含 `@Configuration`、`@EnableAutoConfiguration` 和 `@ComponentScan`。 |
| `@Component`             | 类        | 基础组件注解，标记类为 Spring 管理的 Bean。                  |
| `@Service`               | 类        | `@Component` 的特化，标记业务逻辑层（Service 层）。          |
| `@Repository`            | 类        | `@Component` 的特化，标记数据访问层（DAO 层），具备持久层异常转换功能。 |
| `@Controller`            | 类        | `@Component` 的特化，MVC 表现层组件，返回值通常解析为视图路径。 |
| `@RestController`        | 类        | `@Controller` + `@ResponseBody` 的组合，用于构建 RESTful API，默认返回 JSON/XML 文本。 |
| `@Configuration`         | 类        | 声明配置类，替代 XML 配置，类内部通常定义 `@Bean` 方法。     |
| `@Bean`                  | 方法      | 显式声明方法返回的对象为一个 Spring Bean，由容器托管。       |
| `@Scope`                 | 类 / 方法 | 声明 Bean 作用域，如 `"singleton"`（默认单例）、`"prototype"`（多例）。 |

---

## 依赖注入与装配

| 注解                       | 作用位置               | 核心说明与典型用法                                           |
| -------------------------- | ---------------------- | ------------------------------------------------------------ |
| `@Autowired`               | 字段 / 构造器 / Setter | Spring 原生注解，默认按类型（byType）自动装配依赖对象。      |
| `@Qualifier`               | 字段 / 参数            | 配合 `@Autowired` 使用，当存在多个同类型 Bean 时按名称（byName）指定注入。 |
| `@Resource`                | 字段 / Setter          | JDK 自带规范（JSR-250），默认按名称（byName）装配，找不到名称再按类型装配。 |
| `@Primary`                 | 类 / 方法              | 当存在多个相同候选 Bean 时，指定该 Bean 享有最高注入优先级。 |
| `@Value`                   | 字段 / 参数            | 注入配置属性或表达式，如 `@Value("${app.token.secret}")`。   |
| `@ConfigurationProperties` | 类 / 方法              | 批量将配置文件的外部属性绑定到 JavaBean，如 `@ConfigurationProperties(prefix = "db")`。 |

---

## Web 与请求映射 (Spring MVC)

| 注解              | 作用位置  | 核心说明与典型用法                                           |
| ----------------- | --------- | ------------------------------------------------------------ |
| `@RequestMapping` | 类 / 方法 | 映射 URL 到控制器类或方法。可在类上统一定义路径前缀。        |
| `@GetMapping`     | 方法      | `@RequestMapping(method = RequestMethod.GET)` 的简写。       |
| `@PostMapping`    | 方法      | `@RequestMapping(method = RequestMethod.POST)` 的简写。      |
| `@PutMapping`     | 方法      | `@RequestMapping(method = RequestMethod.PUT)` 的简写，通常用于资源完整更新。 |
| `@DeleteMapping`  | 方法      | `@RequestMapping(method = RequestMethod.DELETE)` 的简写，用于资源删除。 |
| `@PathVariable`   | 参数      | 获取 URL 路径变量，如 `/users/{id}` 中的 `id`。              |
| `@RequestParam`   | 参数      | 绑定 Query 参数或表单字段，如 `/search?keyword=abc`。        |
| `@RequestBody`    | 参数      | 将请求体（如 JSON/XML）通过 `HttpMessageConverter` 反序列化绑定为 Java 对象。 |
| `@ResponseBody`   | 方法 / 类 | 将方法返回值直接写入 HTTP 响应体，不经过视图解析器。         |
| `@RequestHeader`  | 参数      | 获取指定的 HTTP 请求头内容，如 `@RequestHeader("Authorization")`。 |
| `@CookieValue`    | 参数      | 获取客户端携带的指定 Cookie 值。                             |
| `@CrossOrigin`    | 类 / 方法 | 配置控制器或特定方法的跨域资源共享（CORS）规则。             |

---

## 全局异常处理与校验

| 注解                    | 作用位置         | 核心说明与典型用法                                           |
| ----------------------- | ---------------- | ------------------------------------------------------------ |
| `@ControllerAdvice`     | 类               | 全局增强类，用于统一处理 Controller 层抛出的异常或进行数据绑定增强。 |
| `@RestControllerAdvice` | 类               | `@ControllerAdvice` + `@ResponseBody` 的组合，统一处理异常并直接返回 JSON 格式结果。 |
| `@ExceptionHandler`     | 方法             | 定义在全局异常类或控制器中，用于捕获并处理特定类型的异常（如 `@ExceptionHandler(Exception.class)`）。 |
| `@ResponseStatus`       | 类 / 方法        | 指定方法执行后或特定异常被抛出时返回的 HTTP 状态码。         |
| `@Validated`            | 类 / 方法 / 参数 | Spring 提供的参数校验驱动注解，支持分组校验（Group Validation）。 |
| `@Valid`                | 参数 / 字段      | JSR-303/380 原生注解，触发对象属性的标准嵌套校验。           |

---

## 事务、切面与异步

| 注解                 | 作用位置  | 核心说明与典型用法                                           |
| -------------------- | --------- | ------------------------------------------------------------ |
| `@Transactional`     | 类 / 方法 | 声明事务管理，控制事务传播机制（`propagation`）、隔离级别（`isolation`）及回滚策略（`rollbackFor`）。 |
| `@Aspect`            | 类        | 声明此类为一个切面（AspectJ 注解），配合 Spring AOP 实现横切逻辑。 |
| `@Pointcut`          | 方法      | 定义切入点表达式，指定切面生效的目标类与方法范围。           |
| `@Around`            | 方法      | 环绕通知，围绕目标方法执行，可手动控制目标方法的调用过程（最强大的切面通知）。 |
| `@Before` / `@After` | 方法      | 前置通知 / 后置通知，分别在目标方法执行前或返回后执行逻辑。  |
| `@Async`             | 类 / 方法 | 标记异步调用，需在启动类或配置类上标注 `@EnableAsync` 激活。 |
| `@Scheduled`         | 方法      | 配置定时任务调度（支持 cron 表达式、固定间隔等），需配合 `@EnableScheduling` 激活。 |

---

## 条件装配与缓存

| 注解                        | 作用位置  | 核心说明与典型用法                                           |
| --------------------------- | --------- | ------------------------------------------------------------ |
| `@ConditionalOnProperty`    | 类 / 方法 | 根据指定的配置属性（Key/Value）是否存在来决定是否加载该 Bean。 |
| `@ConditionalOnClass`       | 类 / 方法 | 当类路径（Classpath）下存在指定的 Class 时才加载该 Bean。    |
| `@ConditionalOnMissingBean` | 类 / 方法 | 仅当容器中不存在指定类型的 Bean 时才加载（常用于 Spring Boot 自定义 Starter 默认配置）。 |
| `@Cacheable`                | 方法      | 开启方法返回值缓存，若缓存命中则直接返回缓存值，不执行方法体。 |
| `@CachePut`                 | 方法      | 执行方法并强制将返回值刷新到指定缓存中。                     |
| `@CacheEvict`               | 方法      | 清理指定缓存键或清空整个缓存空间（如更新操作后失效缓存）。   |

---

## Lombok 常用配合注解

| 注解                  | 作用位置 | 核心说明与典型用法                                           |
| --------------------- | -------- | ------------------------------------------------------------ |
| `@Data`               | 类       | 自动生成 `Getter`、`Setter`、`equals`、`hashCode`、`canEqual` 和 `toString` 方法。 |
| `@Slf4j`              | 类       | 自动在类中注入名为 `log` 的 SLF4J 日志实例，省去手动编写 logger 初始化代码。 |
| `@NoArgsConstructor`  | 类       | 生成无参构造器（框架反序列化通常必需）。                     |
| `@AllArgsConstructor` | 类       | 生成包含所有字段的全参构造器。                               |
| `@Builder`            | 类       | 基于 Builder 建造者模式生成流式链式构建代码。                |