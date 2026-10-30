# Java 面试

## 建议复习顺序

1. Java 基础、集合：先掌握语言和常用容器。
2. Spring、MyBatis、MySQL、Redis：这是初级 Java 后端的核心。
3. 并发、JVM：先掌握概念、区别和常见问题，不急着钻源码。
4. 安全鉴权、缓存事务、预测链路：结合 AI ModelHub 源码逐个讲清楚。
5. AOP、限流、审计、文件存储、测试：理解项目为什么需要它们，以及出问题如何排查。

## 第一部分：通用 Java 后端基础题

以下保留原题库的 10 章组织与 96 道题目。


以原《Java面试汇总.pdf》为基础，面向初级 Java 后端岗位整理

这份题库不是要求一次背完。第一轮先掌握 Java 基础、集合、并发基础、Spring、MyBatis、MySQL、Redis。

每题的“面试回答”按口头表达组织，可以先理解再复述，不建议逐字死背。

原面经中存在旧版本结论或绝对化表述的地方，已按常见现代 Java / JDK 8+ / Spring Boot 开发语境修正；遇到依赖具体版本的内容，会在答案中注明。

项目场景题用于帮助你把概念讲清楚。只描述自己实际实现和理解的功能，不要把题库中的示例当成自己的项目经历。

本版以知识问答为主，刻意减少代码题，适合暂时不想写代码时进行口头复习。

第 1 轮：第 1—2 章（Java 基础、集合），先把语言和常用容器说清楚。

第 2 轮：第 5—8 章（Spring、MyBatis、MySQL、Redis），这是普通 Java 后端岗位的核心。

第 3 轮：第 3—4 章（并发、JVM），先掌握概念、区别和常见问题，不急着钻源码。

第 4 轮：第 9 章（项目场景），练习把知识点映射到自己的项目调用链。

第 10 章只需建立概念，除非目标岗位明确要求，否则暂时不投入大量时间。

## 第一章 Java 基础


### 1. Java 有哪些主要特点？

面试回答：Java 是一门面向对象、静态类型的编程语言。它有比较完善的类库和异常处理机制，支持多线程；Java 源码通常编译成字节码，再由 JVM 执行，因此具备跨平台能力。实际开发中，我认为它比较重要的特点是面向对象、生态成熟、可维护性较好。

### 2. 面向对象和面向过程有什么区别？

面试回答：面向过程更关注把任务拆成一系列步骤，再按顺序执行；面向对象更关注把系统中的事物抽象成对象，由对象承担数据和行为。面向对象常见的优点是封装、复用和扩展更方便，但不能简单说它一定比面向过程慢，性能取决于具体设计和实现。

### 3. Java 的基本数据类型有哪些？

面试回答：Java 有 8 种基本类型：byte、short、int、long、float、double、char、boolean。前七种有明确的存储宽度，boolean 的具体存储表示由 JVM 实现决定，不能笼统地说它在所有场景固定占 4 字节。对应包装类分别是 Byte、Short、Integer、Long、Float、Double、Character、Boolean。

### 4. 基本类型和包装类型有什么区别？

面试回答：基本类型直接表示数值或字符等基本数据；包装类型是对象，可以为 null，也能使用对象的方法，并能用于泛型集合。例如 int 默认值是 0，成员变量 Integer 默认值是 null。数据库字段可能为空时，实体类中使用包装类型通常更合适，但要注意自动拆箱时如果值为 null，会触发 NullPointerException。

### 5. 什么是自动装箱和拆箱？

面试回答：自动装箱是把基本类型自动转换成包装类型，例如 int 转 Integer；自动拆箱是把包装类型转换回基本类型。底层通常会调用 valueOf 和 xxxValue 等方法。要注意包装类型可能为 null，拆箱会产生空指针；也不要用 == 判断两个包装对象的数值是否相等，通常使用 equals 或先转成基本类型比较。

### 6. == 和 equals 有什么区别？

面试回答：== 对基本类型比较数值；对引用类型比较是否为同一个对象。equals 是方法，默认继承自 Object 时行为与 == 类似，但很多类会重写它来比较内容，例如 String。比较对象内容时通常使用 equals；如果对象可能为 null，要先判空，或者使用 Objects.equals(a, b)。

### 7. 为什么重写 equals 通常也要重写 hashCode？

面试回答：因为 HashMap、HashSet 等哈希集合会先根据 hashCode 定位，再通过 equals 判断是否相等。Java 约定：如果两个对象 equals 相等，它们的 hashCode 必须相等；反过来不成立，哈希值相同的对象也可能不相等。如果只重写 equals 不重写 hashCode，哈希集合可能出现逻辑错误。hashCode 不是简单地由对象内存地址换算出来的。

### 8. String、StringBuilder、StringBuffer 有什么区别？

面试回答：String 是不可变对象，修改字符串通常会产生新的字符串对象；StringBuilder 是可变字符序列，适合单线程下频繁拼接；StringBuffer 的常用方法带同步控制，线程安全，但通常有额外开销。现代 OpenJDK 中 String 的底层实现不是老资料所说的固定 char[]，JDK 9 之后采用 byte[] 配合编码标识实现紧凑字符串。

### 9. 重载和重写有什么区别？

面试回答：重载发生在同一个类中，方法名相同、参数列表不同，与返回值类型无关；重写发生在子类对父类可继承方法的重新实现，方法名和参数列表相同，返回类型需要相同或协变，访问权限不能更严格。重写时还要遵守检查异常的限制。static 方法属于隐藏，不是通常意义上的实例方法重写。

### 10. final、finally、finalize 有什么区别？

面试回答：final 是修饰符：修饰变量表示只能赋值一次，修饰方法表示不能被子类重写，修饰类表示不能被继承。finally 是异常处理结构中的代码块，通常用于释放资源，但遇到 System.exit、进程终止等情况不保证执行。finalize 是历史上的对象终结机制，已被弃用，不应该用它管理资源。

### 11. static 关键字有什么作用？

面试回答：static 修饰的成员属于类，而不是某个对象，所有实例共享一份类变量；静态方法可以通过类名调用，不能直接访问实例成员，也不能使用 this。常见场景包括常量、工具方法和共享计数等。静态变量生命周期通常与类的加载和卸载相关。

### 12. try、catch、finally 的执行规则是什么？

面试回答：try 中发生异常后，会寻找匹配的 catch；无论是否发生异常，finally 通常都会执行，常用于释放资源。即使 try 或 catch 中有 return，finally 通常仍会在方法真正返回前执行。但如果 finally 自己也 return，会覆盖原来的返回结果，因此不建议在 finally 中写 return。资源释放更推荐 try-with-resources。

### 13. Exception 和 Error 有什么区别？

面试回答：它们都继承自 Throwable。Exception 表示程序可以考虑处理的异常，包含受检异常和运行时异常；Error 表示 JVM 或系统层面的严重问题，应用通常不应该尝试恢复。RuntimeException 及其子类属于运行时异常，编译器不强制捕获；受检异常则需要捕获或声明抛出。

### 14. 什么是泛型？有什么好处？

面试回答：泛型允许在定义类、接口或方法时使用类型参数，例如 List<String>。它能在编译期提供类型检查，减少强制类型转换，让代码复用性更好。Java 泛型主要通过类型擦除实现，运行时通常不会保留普通泛型参数的完整类型信息。

### 15. 什么是反射？有什么应用和风险？

面试回答：反射是在运行时获取类的信息，并动态创建对象、访问成员或调用方法。Spring 的依赖注入、部分 ORM 框架和通用工具会使用反射。它提高了框架的灵活性，但也会增加理解和维护成本，部分操作可能有性能开销，并受到 Java 模块访问规则限制。

### 16. 深拷贝和浅拷贝有什么区别？

面试回答：浅拷贝只复制对象本身，内部引用字段仍可能指向同一对象；深拷贝会把对象内部引用的对象也复制一份，使两份对象尽量独立。具体是否深拷贝取决于实现方式，不能只看是否调用了 clone。

## 第二章 集合框架


### 17. List、Set、Map 有什么区别？

面试回答：List 是有序集合，允许重复元素，可以按索引访问；Set 用于保存不重复元素，具体顺序由实现类决定；Map 保存键值对，key 不重复，value 可以重复。常见实现有 ArrayList、LinkedList、HashSet、TreeSet、HashMap、TreeMap。

### 18. ArrayList 和 LinkedList 有什么区别？

面试回答：ArrayList 底层是动态数组，按索引读取通常很快，尾部追加通常效率较高，中间插入或删除可能需要移动元素。LinkedList 是双向链表，已知节点位置时插入删除方便，但按索引查找需要遍历。不能简单认为 LinkedList 的增删在所有场景都更快，实际还要考虑查找位置和内存局部性。

### 19. HashMap 的底层结构是什么？

面试回答：以常见的 JDK 8 实现为例，HashMap 底层是数组加链表，链表过长时在满足条件的情况下会树化为红黑树。key 的 hash 用于计算数组下标；发生哈希冲突时，多个键会落在同一个桶中，再通过链表或红黑树组织。

### 20. HashMap 如何解决哈希冲突？

面试回答：HashMap 使用数组桶存储元素。不同 key 计算后可能落到同一个桶，这就是哈希冲突。JDK 8 中桶内通常使用链表，达到树化条件后可能转成红黑树；查找时先比较哈希，再通过 == 或 equals 判断 key 是否相同。

### 21. HashMap 为什么容量通常是 2 的幂？

面试回答：容量为 2 的幂时，可以通过位运算快速计算桶下标，分布计算更高效。HashMap 扩容时通常会把容量扩大为原来的两倍，并根据 hash 的相关位决定节点留在原位置还是移动到新位置。

### 22. HashMap 是线程安全的吗？

面试回答：不是。多个线程同时修改普通 HashMap，可能出现数据覆盖、可见性或结构一致性问题。并发场景可以考虑 ConcurrentHashMap，或者在合适的范围使用同步控制。不要把旧版本并发问题简单描述成“必然死锁”。

### 23. HashMap 和 Hashtable 有什么区别？

面试回答：HashMap 通常允许一个 null key 和多个 null value，且本身不是线程安全的；Hashtable 不允许 null key/value，方法带同步控制，是较老的类。现在并发场景一般优先考虑 ConcurrentHashMap，而不是 Hashtable。

### 24. ConcurrentHashMap 如何保证线程安全？

面试回答：JDK 8 的 ConcurrentHashMap 主要结合 CAS 和 synchronized 等机制，对桶或节点进行并发控制，而不是早期版本的 Segment 分段锁实现。它允许较高程度的并发访问，但不代表所有复合操作天然就是业务原子操作；需要时应使用原子方法或额外同步。

### 25. fail-fast 是什么？

面试回答：fail-fast 是一种快速失败机制：在迭代过程中，如果集合结构被非迭代器方式修改，迭代器可能抛出 ConcurrentModificationException，用来尽早暴露并发修改问题。它是尽力检测机制，不是线程安全保证，也不是所有集合迭代器都采用相同策略。ConcurrentHashMap 的迭代器是弱一致性的。

## 第三章 多线程与并发


### 26. 进程和线程有什么区别？

面试回答：进程是操作系统分配资源和进行隔离的基本单位之一；线程是 CPU 调度执行的基本单位。一个进程通常包含多个线程，同一进程内线程共享进程的大部分资源，但每个线程有自己的栈、程序计数器等执行状态。

### 27. Java 线程有哪些常见状态？

面试回答：Thread.State 包含 NEW、RUNNABLE、BLOCKED、WAITING、TIMED_WAITING、TERMINATED。RUNNABLE 在 Java 层面包含就绪和运行等状态；BLOCKED 通常表示等待进入 synchronized 监视器锁；WAITING 和 TIMED_WAITING 表示等待某个条件或时间。

### 28. 创建线程有哪些常见方式？

面试回答：常见方式包括继承 Thread、实现 Runnable、实现 Callable 并通过 FutureTask 或线程池提交任务。实际项目中更推荐把任务提交给线程池管理，而不是每次任务都手动创建线程。

### 29. start() 和 run() 有什么区别？

面试回答：start() 会启动一个新的线程，并由 JVM 调度执行 run()；直接调用 run() 只是普通方法调用，仍在当前线程中执行，不会创建新线程。一个 Thread 实例不能重复调用 start()。

### 30. synchronized 有什么作用？

面试回答：synchronized 用于实现互斥访问，并保证进入和退出同一监视器时的内存可见性。它可以修饰实例方法、静态方法或代码块；实例同步通常锁当前对象，静态同步锁对应的 Class 对象。锁的范围越大，竞争可能越明显，因此要合理控制同步粒度。

### 31. volatile 有什么作用？能保证原子性吗？

面试回答：volatile 主要保证变量的可见性，并通过内存语义约束相关指令重排序。它不能保证复合操作的原子性，例如 count++ 仍然包含读取、修改和写回多个步骤。需要原子更新时可以使用 AtomicInteger 等原子类或锁。

### 32. sleep() 和 wait() 有什么区别？

面试回答：sleep() 是 Thread 的静态方法，暂停当前线程，不会主动释放已经持有的锁；wait() 是 Object 的方法，必须在持有该对象监视器时调用，调用后会释放该监视器，直到被通知、中断或超时等情况后重新竞争锁。

### 33. notify() 和 notifyAll() 有什么区别？

面试回答：notify() 唤醒在该对象监视器上等待的一个线程，具体唤醒哪个由 JVM 调度决定；notifyAll() 唤醒所有等待线程，让它们重新竞争锁。被唤醒不代表立刻执行，也不代表条件一定成立，因此通常要在循环中检查等待条件。

### 34. 什么是线程池？为什么使用线程池？

面试回答：线程池统一管理线程的创建、复用和销毁，减少频繁创建线程的开销，并能限制并发数量、管理任务队列和拒绝策略。使用线程池还能让系统更容易控制资源，避免无限制创建线程导致资源耗尽。

### 35. 线程池有哪些核心参数？

面试回答：ThreadPoolExecutor 常见核心参数包括 corePoolSize、maximumPoolSize、keepAliveTime、unit、workQueue、threadFactory、handler。它们分别控制核心线程数、最大线程数、非核心线程空闲存活时间、任务队列、线程创建方式和拒绝策略。

### 36. execute() 和 submit() 有什么区别？

面试回答：execute() 接收 Runnable，主要用于执行任务，不返回任务结果；submit() 可以提交 Runnable 或 Callable，并返回 Future，可以获取结果、判断完成状态或取消任务。通过 submit 提交任务时，异常通常会保存在 Future 中，需要通过 get 等方式感知。

### 37. 如何优雅地停止线程？

面试回答：通常通过中断机制协作停止：调用 interrupt() 设置中断标记，任务代码定期检查中断状态，并在合适位置结束执行或清理资源。不推荐使用已经废弃的 stop() 强制终止线程，因为它可能在对象状态不一致时直接停止。

## 第四章 JVM


### 38. JVM 运行时数据区有哪些？

面试回答：常见划分包括程序计数器、Java 虚拟机栈、本地方法栈、堆和方法区。程序计数器、虚拟机栈、本地方法栈通常是线程私有的；堆和方法区通常由多个线程共享。需要注意，JVM 运行时数据区与 Java 内存模型（JMM）不是同一个概念。

### 39. 堆和栈有什么区别？

面试回答：堆主要用于存放对象实例和数组，是线程共享区域，由垃圾收集器管理；虚拟机栈是线程私有的，保存方法调用相关的栈帧，例如局部变量表、操作数栈和动态链接等。栈内存不足可能出现 StackOverflowError，堆无法分配对象可能出现 OutOfMemoryError。

### 40. JDK 8 之后方法区有什么变化？

面试回答：方法区是 JVM 规范中的概念。HotSpot 在 JDK 8 移除了永久代，类元数据等信息主要放到本地内存中的 Metaspace（元空间）；运行时常量池等概念仍然存在。不要把方法区和元空间完全当成同一个层次的术语。

### 41. 什么是垃圾回收？对象一定会被回收吗？

面试回答：垃圾回收器负责回收不再被程序使用的对象所占用的内存。常见判断思路是可达性分析：从 GC Roots 出发，如果对象不可达，它可能成为可回收对象。但不可达不代表马上回收，具体时机由垃圾收集器决定。

### 42. 常见垃圾回收算法有哪些？

面试回答：常见基础算法包括标记-清除、标记-复制、标记-整理。标记-清除实现相对简单但可能产生内存碎片；复制算法适合存活对象较少的区域；标记-整理会移动存活对象，减少碎片，但整理过程有额外开销。现代垃圾收集器通常会组合使用多种思路。

### 43. Minor GC、Major GC、Full GC 怎么理解？

面试回答：这些术语在不同垃圾收集器和资料中的使用口径可能不同。通常 Minor GC 指年轻代相关回收，Full GC 通常表示对整个堆或更大范围进行回收，可能伴随类元数据区域处理。面试时应说明具体 JVM 和收集器背景，不要把所有 GC 名称说成跨实现完全统一。

### 44. 什么情况下可能出现内存溢出？

面试回答：当 JVM 无法为对象分配所需内存，且无法通过回收获得足够空间时，可能出现 OutOfMemoryError。常见原因包括对象持续增长、缓存没有上限、大量对象被集合长期引用、线程创建过多、元空间使用异常等。排查时要结合异常信息、GC 日志和 heap dump，而不是只盲目调大内存。

### 45. 什么是类加载双亲委派？

面试回答：类加载器收到类加载请求时，通常先把请求交给父加载器处理，父加载器无法完成时再由当前加载器尝试。这样可以避免核心类被随意替换，也能减少重复加载。它是常见的默认模型，但 Java 允许特定场景采用自定义加载机制，并非所有加载都必须严格遵循单一路径。

## 第五章 Spring / Spring Boot


### 46. 什么是 IoC 和 DI？

面试回答：IoC 是控制反转，指对象的创建和依赖关系管理不再完全由业务代码自己控制，而交给 Spring 容器管理；DI 是依赖注入，是实现 IoC 的常见方式。比如 Controller 需要 Service，可以由 Spring 创建并注入 Service，减少对象之间的硬编码依赖。

### 47. 什么是 AOP？

面试回答：AOP 是面向切面编程，用于把日志、事务、权限校验等横切关注点从业务逻辑中抽离出来。Spring AOP 通常通过代理对象实现，在目标方法执行前后织入增强逻辑。常见代理方式包括 JDK 动态代理和 CGLIB。

### 48. Spring Bean 的默认作用域是什么？

面试回答：Spring Bean 默认是 singleton，即在一个 Spring IoC 容器中通常共享一个 Bean 实例。还有 prototype，以及 Web 环境下常见的 request、session、application 等作用域。单例 Bean 被多个请求线程共享，因此不要随意在其中保存请求级可变状态。

### 49. Spring Bean 生命周期大致是什么？

面试回答：大致过程是：实例化、属性依赖注入、Aware 回调、BeanPostProcessor 前置处理、初始化回调、BeanPostProcessor 后置处理，之后 Bean 可以被使用，容器关闭时执行销毁回调。实际细节会受到配置、接口和后置处理器影响，面试时先说清主流程即可。

### 50. Spring 中常见的依赖注入方式有哪些？

面试回答：常见有构造器注入、Setter 注入和字段注入。实际项目通常更推荐构造器注入，因为依赖关系明确，便于测试，也能让必要依赖在对象创建时就确定。字段注入写起来简单，但不利于纯 Java 单元测试和依赖显式表达。

### 51. @Autowired 和 @Resource 有什么区别？

面试回答：两者都可以用于依赖注入。@Autowired 是 Spring 提供的注解，默认主要按类型匹配，存在多个候选 Bean 时可以结合 @Qualifier 指定；@Resource 是 Jakarta/Java 相关规范中的注解，常见语义是先按名称匹配，再按类型等规则处理。具体行为与版本和配置有关。

### 52. Spring MVC 请求处理流程是什么？

面试回答：请求先到 DispatcherServlet，它作为前端控制器接收请求；再通过 HandlerMapping 找到处理器，通过 HandlerAdapter 调用 Controller；Controller 处理后返回结果，Spring MVC 再根据返回类型完成视图渲染或消息转换，最终把 HTTP 响应返回客户端。REST 接口通常通过消息转换器把对象序列化成 JSON。

### 53. Spring Boot 解决了什么问题？

面试回答：Spring Boot 在 Spring 基础上提供自动配置、Starter 依赖和内嵌 Web 服务器等能力，减少项目初始搭建和配置工作，让应用可以更方便地以可执行 JAR 运行。它不是替代 Spring，而是帮助开发者更快地使用 Spring 生态。

### 54. 什么是自动配置？

面试回答：Spring Boot 根据类路径中的依赖、已有 Bean、配置属性等条件，判断是否需要自动创建某些 Bean。自动配置通常由条件注解控制，开发者也可以通过配置覆盖默认行为，或者自己声明 Bean。

### 55. Spring Boot 如何统一处理异常？

面试回答：常见做法是使用 @RestControllerAdvice 或 @ControllerAdvice 配合 @ExceptionHandler，集中处理 Controller 层抛出的异常，并返回统一响应结构。业务异常可以自定义异常类型和错误码；同时要记录必要日志，不能把数据库密码、堆栈等敏感信息直接返回给客户端。

### 56. Spring 事务的作用是什么？

面试回答：事务用于保证一组数据库操作满足原子性、一致性、隔离性和持久性。Spring 常通过 @Transactional 声明事务边界，由事务管理器和 AOP 代理控制提交或回滚。默认情况下，运行时异常和 Error 通常触发回滚；受检异常默认不一定回滚，可以通过 rollbackFor 配置。

### 57. @Transactional 为什么有时不生效？

面试回答：常见原因包括：同一个类内部通过 this 调用方法，没有经过 Spring 代理；方法或类的代理条件不满足；异常被内部捕获后没有继续抛出；异常类型不符合默认回滚规则；数据库引擎或事务管理器配置不正确。排查时先确认调用是否经过代理，再检查异常和事务配置。

## 第六章 MyBatis


### 58. MyBatis 是什么？

面试回答：MyBatis 是一个持久层框架，负责把 Java 方法调用与 SQL 执行关联起来，并把查询结果映射成 Java 对象。开发者可以自己编写和控制 SQL，适合需要灵活编写 SQL 的业务场景。

### 59. MyBatis 的 #{} 和 ${} 有什么区别？

面试回答：#{} 通常会生成预编译 SQL 的参数占位符，通过 PreparedStatement 绑定参数，能有效避免 SQL 注入；${} 是字符串直接替换，常用于动态表名、列名等不能通过普通参数绑定的位置，但必须严格校验白名单，不能直接拼接用户输入。

### 60. Mapper 接口为什么不需要自己写实现类？

面试回答：MyBatis 会根据 Mapper 接口和映射配置创建代理对象。调用接口方法时，代理对象根据命名空间和方法名找到对应 SQL，执行后再把结果映射成返回对象。注解 SQL 和 XML SQL 都可以作为映射来源。

### 61. MyBatis 如何进行分页？

面试回答：分页可以通过 SQL 的 LIMIT/OFFSET 实现，也可以使用分页插件。常见做法是先查询总数，再查询当前页数据；数据量很大、偏移量很深时，OFFSET 分页可能变慢，可以评估基于索引字段的游标/Keyset 分页。分页参数要校验，避免非法页码和过大的 pageSize。

### 62. MyBatis 动态 SQL 有哪些常见标签？

面试回答：常见标签有 if、where、set、choose/when/otherwise、foreach、trim。它们用于按条件拼接 SQL，例如根据查询条件决定是否追加 where 条件，或使用 foreach 生成 IN 条件和批量语句。动态 SQL 要特别注意空集合和参数边界。

### 63. MyBatis 一级缓存和二级缓存是什么？

面试回答：一级缓存是 SqlSession 级别的本地缓存，默认开启；同一个 SqlSession 中相同查询在缓存条件满足时可能复用结果。二级缓存是 Mapper namespace 级别，需要配置开启，并且对事务、更新和对象状态有额外要求。实际项目是否启用二级缓存要结合数据更新频率和一致性需求，不是开了就一定更好。

## 第七章 MySQL


### 64. 什么是数据库事务？ACID 是什么？

面试回答：事务是一组数据库操作的逻辑单元。ACID 分别是原子性、一致性、隔离性、持久性：原子性表示操作要么都成功要么都失败；一致性表示事务前后数据满足约束；隔离性控制并发事务之间的影响；持久性表示提交后的结果能够持久保存。

### 65. MySQL 常见存储引擎 InnoDB 和 MyISAM 有什么区别？

面试回答：InnoDB 支持事务、行级锁、外键，并通过 MVCC 等机制支持并发一致性读，是现代 MySQL 业务表常用的存储引擎。MyISAM 不支持事务，主要使用表级锁。新业务通常优先选择 InnoDB。

### 66. 什么是索引？为什么能提高查询速度？

面试回答：索引是帮助数据库快速定位数据的数据结构。InnoDB 常用 B+Tree 索引，树的层级较低，适合范围查询和有序查找。索引能减少扫描数据量，但会占用磁盘空间，并增加插入、更新和删除时维护索引的成本。

### 67. 什么是聚簇索引和二级索引？

面试回答：InnoDB 的聚簇索引叶子节点保存整行数据，通常由主键组织；二级索引叶子节点保存索引列和对应主键值。通过二级索引查到主键后，如果还需要索引中没有的列，可能再回到聚簇索引查整行，这个过程通常称为回表。

### 68. 什么情况下索引可能失效或无法充分利用？

面试回答：常见情况包括对索引列使用函数或表达式、隐式类型转换、模糊查询以 % 开头、联合索引不符合最左前缀规则、使用 OR 导致优化器选择其他计划等。但是否使用索引最终由优化器根据统计信息和成本决定，应使用 EXPLAIN 验证，不能只背绝对化结论。

### 69. 什么是联合索引的最左前缀原则？

面试回答：例如建立 (a,b,c) 联合索引，查询条件从最左边的列开始匹配时更容易利用索引，如 a、a+b、a+b+c。跳过最左列通常不能按预期利用该联合索引。实际能使用多少索引列，还取决于等值、范围条件和优化器执行计划。

### 70. MySQL 事务隔离级别有哪些？

面试回答：SQL 标准常见四种隔离级别：读未提交、读已提交、可重复读、串行化。隔离级别越高，并发控制通常越严格，但可能降低并发能力。MySQL InnoDB 默认隔离级别通常是可重复读，具体以实例配置为准。

### 71. 脏读、不可重复读、幻读分别是什么？

面试回答：脏读是读到其他事务尚未提交的数据；不可重复读是同一事务两次读取同一行，结果不同；幻读通常指同一范围查询再次执行时，出现了之前没有的符合条件的行。InnoDB 通过 MVCC 和锁等机制处理并发读写问题，不能只用一句“可重复读彻底没有幻读”概括所有场景。

### 72. 什么是 MVCC？

面试回答：MVCC 是多版本并发控制。InnoDB 会利用记录版本信息和 Read View 等机制，让符合条件的普通一致性读读取某个时间点可见的数据版本，从而减少读写之间的阻塞。具体可见性规则与隔离级别、事务启动和读视图生成时机有关。

### 73. DELETE、TRUNCATE、DROP 有什么区别？

面试回答：DELETE 删除表中的行，可以带 WHERE 条件，属于 DML；TRUNCATE 通常快速清空整张表，不能按条件删除，属于 DDL 语义；DROP 删除整个表对象及其定义。它们在事务回滚、触发器、权限和自增值处理方面存在差异，具体还要看 MySQL 版本和执行环境。

### 74. 如何排查和优化慢 SQL？

面试回答：先确认慢 SQL 和业务场景，再查看执行计划 EXPLAIN，关注访问类型、使用的索引、扫描行数、排序和临时表等信息；然后检查索引设计、查询条件、返回字段、分页方式和数据量。优化后要用真实数据验证效果，避免为了单条 SQL 盲目增加大量索引。

## 第八章 Redis


### 75. Redis 是什么？为什么常用？

面试回答：Redis 是一个以内存数据结构为核心的数据库，支持字符串、哈希、列表、集合、有序集合等结构，也支持持久化、过期和多种数据操作。常见用途包括缓存、计数、排行榜、分布式协调等。

### 76. Redis 常见数据类型和场景有哪些？

面试回答：String 可用于缓存、计数和简单令牌；Hash 适合存储对象的多个字段；List 可用于简单队列或列表；Set 适合去重和集合运算；ZSet 适合排行榜和按分数排序。Redis 还提供 Stream、Bitmap、HyperLogLog、Geo 等能力。

### 77. Redis 为什么快？

面试回答：主要原因包括数据主要存放在内存中、数据结构和命令实现经过优化、网络与事件处理模型高效，以及减少了传统磁盘数据库的随机 I/O。不能只回答“因为单线程”：现代 Redis 的网络 I/O 等部分能力也可能使用多线程，具体要结合 Redis 版本和配置。

### 78. Redis 持久化方式有哪些？

面试回答：常见方式是 RDB 和 AOF。RDB 通过生成数据快照保存某个时刻的数据，恢复速度通常较快，但两次快照之间的数据可能丢失；AOF 记录写命令，数据恢复时重放命令，数据安全性和文件大小取决于刷盘策略。两者可以按需求组合使用。

### 79. 什么是缓存穿透、击穿和雪崩？

面试回答：缓存穿透是查询一个缓存和数据库都不存在的数据，导致请求持续打到数据库；缓存击穿是某个热点 key 过期瞬间大量请求同时访问数据库；缓存雪崩是大量 key 同时失效或缓存服务整体不可用，导致数据库压力骤增。

### 80. 如何应对缓存穿透、击穿和雪崩？

面试回答：穿透可以使用参数校验、缓存空值或布隆过滤器；击穿可以使用互斥锁、逻辑过期或热点数据不过期并主动刷新；雪崩可以给过期时间加随机值、做多级缓存、限流降级，并提升缓存集群可用性。方案要结合业务一致性和延迟要求选择。

### 81. Redis 如何设置过期时间？数据过期后如何删除？

面试回答：可以通过 EXPIRE、PEXPIRE 或 SET 时设置过期时间。Redis 通常结合惰性删除和定期删除处理过期 key：访问时发现过期会删除，后台也会周期性抽查清理。过期不意味着到点那一刻一定立刻从内存中删除。

### 82. Redis 和数据库如何保证缓存一致性？

面试回答：常见做法是 Cache Aside：读时先查缓存，未命中再查数据库并回填；更新时通常先更新数据库，再删除缓存。删除缓存失败时可以通过重试、消息队列或延迟双删等机制降低不一致风险。不存在适合所有业务的绝对完美方案，需要根据一致性要求设计。

## 第九章 项目与开发场景


### 83. 一个典型 Spring Boot 接口的请求流程是什么？

面试回答：请求到达 Controller 后，Controller 负责接收参数、做基础校验和组织响应；Service 承担业务规则和事务边界；Mapper 负责调用 MyBatis 执行 SQL；数据库返回结果后，数据逐层返回并由 Controller 转成统一响应。分层的目的是让接口、业务和数据访问职责清晰。

### 84. 为什么要分 Controller、Service、Mapper？

面试回答：Controller 处理 HTTP 请求和响应，不应该堆积复杂业务；Service 组织业务流程、事务和多个数据操作；Mapper 专注数据库访问和 SQL 映射。分层有利于维护、测试和复用，也能避免 Controller 直接操作数据库造成耦合。

### 85. JWT 登录鉴权的大致流程是什么？

面试回答：用户提交账号密码，后端验证身份后签发 Token；客户端保存 Token，并在后续请求中携带；后端过滤器或拦截器解析并校验 Token，确认用户身份和有效期，再把用户信息放入当前请求上下文；权限控制再判断该用户是否能访问对应接口。JWT 本身不是加密后的用户数据，敏感信息不应直接放入载荷。

### 86. 为什么不能只在前端隐藏管理员按钮？

面试回答：前端隐藏按钮只是交互层面的限制，用户仍可能直接构造 HTTP 请求访问接口。真正的权限校验必须在后端执行，例如在过滤器、拦截器或方法权限层判断用户角色和资源权限。

### 87. 什么是事务传播行为？

面试回答：事务传播行为定义一个事务方法被另一个事务方法调用时，应该加入现有事务、创建新事务还是以其他方式运行。常见 REQUIRED 表示有事务就加入，没有就新建；REQUIRES_NEW 表示挂起当前事务并新建事务。初级面试先掌握这两个最常见的即可。

### 88. 为什么分页查询通常需要总数查询和列表查询？

面试回答：分页列表查询负责取当前页的数据，总数查询负责计算符合条件的记录数，前端才能展示总页数和分页信息。两条 SQL 的过滤条件必须保持一致；数据量很大时 count 也可能成为性能瓶颈，需要结合索引和产品分页需求优化。

### 89. 项目中发现接口返回 500，通常怎么排查？

面试回答：先根据请求时间、接口路径和 traceId 等信息定位日志；查看异常堆栈，确认错误发生在 Controller、Service、Mapper 还是外部依赖；再检查请求参数、数据库连接、SQL、事务和配置。修复后补充必要测试，避免只根据前端报错猜原因。

### 90. 如何向面试官介绍自己负责的一个功能？

面试回答：建议按“业务目的—接口入口—核心业务流程—数据表和 SQL—关键技术点—异常与边界处理—自己负责的部分”来讲。只讲自己实际做过、理解过的内容；如果某处是参考或借助工具完成，要能解释关键设计和调用链，不要把没有实现的功能说成项目成果。

## 第十章 进阶选读（第一轮只需了解）


### 91. 什么是微服务？

面试回答：微服务是一种架构风格，把系统按业务能力拆分成多个相对独立、可独立部署的服务。优点是团队和服务可以独立演进，缺点是引入网络调用、服务治理、分布式事务、监控和部署复杂度。小项目不一定需要微服务。

### 92. 什么是服务注册与发现？

面试回答：服务实例启动后把自己的地址注册到注册中心；调用方从注册中心获取可用实例，再通过负载均衡选择目标实例。它解决动态实例地址管理问题，但还需要健康检查、故障剔除和网络容错。

### 93. 什么是消息队列？

面试回答：消息队列用于在生产者和消费者之间异步传递消息，常用于削峰、异步处理和系统解耦。需要关注消息可靠投递、重复消费、顺序性、积压和死信处理。消息队列不能简单保证业务只执行一次，消费者通常还要做幂等。

### 94. 什么是幂等？

面试回答：幂等是指同一个操作执行一次和执行多次，对业务最终结果的影响相同。常见实现方式包括唯一业务流水号、数据库唯一约束、幂等记录、状态机校验等。支付、订单和消息消费场景尤其需要考虑重复请求。

### 95. Linux 查看日志常用哪些命令？

面试回答：常见有 tail -f 实时追踪日志、grep 搜索关键字、less 分页查看、head 查看文件开头、cat 输出文件内容。排查线上问题时通常结合时间范围、关键字、进程信息和应用日志上下文，不建议直接把超大日志文件全部输出。

### 96. Kafka、Elasticsearch 需要现在深入背吗？

面试回答：对于以 Java 后端初级岗位为目标、目前项目主要使用 Spring Boot、MyBatis、MySQL 和 Redis 的情况，第一轮不建议深入背 Kafka 集群、Elasticsearch 分片选举等细节。先知道它们解决什么问题、基本概念和典型场景；等目标岗位明确要求，再专项准备。

## 原题库提醒：背面经时最容易踩的 8 个坑

不要把 boolean 的内存占用说成所有场景固定 4 字节或 1 字节。

不要说 == 比较两个对象的内容；引用类型的 == 比较是否为同一对象。

不要说 hashCode 就是对象内存地址；只需记住 equals 相等必须保证 hashCode 相等。

不要把 JDK 8 的 ConcurrentHashMap 说成 Segment 分段锁实现。

不要把 String 底层永远说成 char[]；现代 JDK 实现已经变化。

不要把 JVM 运行时数据区和 JMM 混为一谈；JDK 8 HotSpot 已移除永久代。

不要把 Redis 快说成“因为单线程”；要从内存、数据结构、事件处理等角度回答。

不要把 @Transactional 理解成加上注解就一定生效；代理调用、异常类型、事务边界都会影响结果。

## 原题库：优先准备的 6 条项目讲解链路

登录鉴权：登录接口 → 用户校验 → Token 签发 → 请求携带 Token → 过滤器解析 → 角色权限判断。

模型查询：Controller 接收参数 → Service 组织业务 → Mapper 接口/XML → SQL 查询 → 结果封装返回。

事务：业务方法 → Spring 代理 → 事务开启 → 多条 SQL 执行 → 提交或回滚。

缓存：请求先查缓存 → 未命中查数据库 → 回填缓存；更新时讨论数据库与缓存的一致性。

分页：参数校验 → count 查询 → limit/offset 查询 → 统一分页结果；注意深分页问题。

异常处理：业务层抛出异常 → 全局异常处理器捕获 → 记录日志 → 返回统一错误码和消息。

> 第一轮目标：能用自己的话讲清楚，不追求背出教材原句。

## 第十一章 AI ModelHub 项目专项面试题


### Model 模型管理


#### 97. 为什么 Model 模块要区分在线、下架和逻辑删除状态？

**面试回答：** 这几个状态表达的是不同业务含义：在线模型可以向普通用户展示和使用；下架模型暂时不对普通用户开放，但仍保留记录；逻辑删除表示业务上不再使用该记录，但数据库行仍然存在。这样可以保留历史关联数据，也便于审计或后续恢复。是否允许恢复、恢复后设为什么状态，需要结合模型文件、配置和服务是否仍可用来判断。

**项目源码定位：** `src/main/java/com/aimodelhub/model/`


#### 98. 普通用户和管理员查询同一个模型 ID，为什么可能得到不同结果？

**面试回答：** 因为查询结果不仅取决于数据库是否存在这条记录，还取决于当前用户身份和模型状态。普通用户通常只能访问在线模型；管理员可能需要查看下架或已删除记录以便管理。权限判断必须在后端完成，不能依赖前端隐藏按钮。具体规则要以 ModelController 的状态判断和管理员判断代码为准。

**项目源码定位：** `src/main/java/com/aimodelhub/model/controller/ModelController.java；src/main/java/com/aimodelhub/security/`


#### 99. 为什么逻辑删除通常使用 UPDATE，而不是 DELETE？

**面试回答：** 逻辑删除通过更新状态字段标记记录不可用，不直接删除行。优点是保留历史数据、关联关系和审计线索；缺点是查询时必须正确过滤已删除数据，唯一索引和恢复逻辑也要考虑状态。逻辑删除不是所有场景的最佳选择，涉及隐私删除或合规要求时还要考虑真正的数据清除。

**项目源码定位：** `src/main/java/com/aimodelhub/model/service/ModelService.java；src/main/resources/mapper/ModelInfoMapper.xml`


#### 100. 分页为什么通常需要 count 查询和列表查询？两条 SQL 要注意什么？

**面试回答：** 列表查询负责取当前页数据，count 查询负责计算符合条件的总记录数，前端才能计算总页数。两条 SQL 的过滤条件必须保持一致，否则总数和列表会对不上。项目使用 LIMIT/OFFSET 时还要校验 page、pageSize，限制过大的 pageSize；深分页场景可能需要评估基于索引字段的 Keyset 分页。

**项目源码定位：** `src/main/java/com/aimodelhub/model/mapper/ModelInfoMapper.java；src/main/resources/mapper/ModelInfoMapper.xml`


#### 101. 修改模型信息后，为什么要考虑清理多个缓存？

**面试回答：** 同一条模型数据可能同时出现在模型列表、在线模型列表和模型详情缓存中。只清理详情缓存，列表仍可能展示旧名称或旧状态；只清理列表缓存，详情页仍可能读到旧数据。因此需要根据写操作影响的数据范围清理相关 key。具体清理哪些 key，要看每个缓存方法的 cacheNames/key 和写操作的 evict 配置。

**项目源码定位：** `src/main/java/com/aimodelhub/model/service/ModelService.java；src/main/java/com/aimodelhub/config/CacheConfig.java`


### 登录与安全鉴权


#### 102. 登录接口从接收账号密码到返回 Token，大致经历什么流程？

**面试回答：** 客户端向登录接口提交账号密码；Controller 接收参数并调用用户服务；服务层查询用户、校验账号状态和密码；验证成功后由 JWT 工具生成 Token，并把必要的用户标识、角色等信息写入声明；最后返回登录结果。密码不能明文保存或写入 Token。具体字段和校验顺序以 AuthController、UserService、JwtUtil 实现为准。

**项目源码定位：** `src/main/java/com/aimodelhub/auth/controller/AuthController.java；src/main/java/com/aimodelhub/security/JwtUtil.java`


#### 103. JWT 认证和权限授权有什么区别？

**面试回答：** 认证回答‘你是谁’，例如校验 Token 并识别 userId、username、role；授权回答‘你能做什么’，例如判断当前用户是否具有管理员权限。认证成功不代表可以访问所有接口。项目中 JWT 过滤器负责建立身份上下文，后续安全配置或 Controller 业务判断再执行权限控制。

**项目源码定位：** `src/main/java/com/aimodelhub/security/JwtAuthFilter.java；src/main/java/com/aimodelhub/security/SecurityConfig.java；src/main/java/com/aimodelhub/model/controller/ModelController.java`


#### 104. 为什么 JWT 过滤器通常继承 OncePerRequestFilter？

**面试回答：** 它是 Spring Web 提供的过滤器基类，设计目标是在一次请求分派过程中避免同一个过滤器被重复执行。JWT 解析属于请求级认证工作，通常希望每次请求建立一次认证上下文。它并不意味着过滤器在所有异步、错误分派场景下都绝对只执行一次，仍要结合具体分派类型和配置理解。

**项目源码定位：** `src/main/java/com/aimodelhub/security/JwtAuthFilter.java`


#### 105. 为什么 JWT 过滤器要加入 Spring Security 过滤器链，而不是写在 Controller 里？

**面试回答：** 过滤器链位于 Controller 之前，可以统一处理不同接口的身份认证，不需要每个 Controller 重复解析 Token。它还能与 Spring Security 的认证上下文、异常处理和授权规则协作。若把认证逻辑散落在 Controller 中，容易遗漏接口并造成重复代码。

**项目源码定位：** `src/main/java/com/aimodelhub/security/SecurityConfig.java；src/main/java/com/aimodelhub/security/JwtAuthFilter.java`


#### 106. permitAll、authenticated、hasRole 有什么区别？

**面试回答：** permitAll 表示该请求不要求登录；authenticated 表示必须存在已认证身份；hasRole 用于要求指定角色，Spring Security 通常会按约定添加 ROLE_ 前缀，因此 hasRole("ADMIN") 通常对应 ROLE_ADMIN 权限。具体匹配规则要结合项目授予的 GrantedAuthority 内容。

**项目源码定位：** `src/main/java/com/aimodelhub/security/SecurityConfig.java`


#### 107. 401 和 403 有什么区别？

**面试回答：** 401 表示当前请求没有通过身份认证，例如缺少、无效或过期的 Token；403 表示身份已识别，但没有访问该资源的权限。实际项目中也可能为了避免泄露资源是否存在而采用特定响应策略，不能只凭状态码推断所有业务细节。

**项目源码定位：** `src/main/java/com/aimodelhub/security/SecurityConfig.java`


#### 108. JWT 是无状态的，为什么退出登录还需要 Token 黑名单？

**面试回答：** JWT 签发后，在过期前通常可以独立验证，服务端不保存传统 Session 状态。因此用户退出登录时，单纯删除前端 Token 并不能阻止被复制的 Token 继续使用。黑名单通过记录已撤销 Token 的标识，并在后续请求中检查，达到提前失效的效果。要考虑黑名单 TTL、Redis 可用性和 Token 唯一标识设计。

**项目源码定位：** `src/main/java/com/aimodelhub/security/TokenBlacklistService.java；src/main/java/com/aimodelhub/security/JwtAuthFilter.java`


### 缓存与事务


#### 109. @Cacheable 命中缓存后，Service 方法还会执行吗？

**面试回答：** 在常规 Spring Cache 代理调用且缓存命中的情况下，代理会直接返回缓存值，目标方法体通常不会执行，因此 Mapper 查询也会被跳过。若发生同类内部 this 自调用，调用可能绕过代理，缓存注解就可能不生效。还要检查缓存 key、cacheNames、序列化和缓存管理器配置。

**项目源码定位：** `src/main/java/com/aimodelhub/model/service/ModelService.java；src/main/java/com/aimodelhub/config/CacheConfig.java`


#### 110. @CacheEvict、@CachePut 和 @Caching 有什么区别？

**面试回答：** @CacheEvict 用于清理缓存；@CachePut 会执行目标方法，并把返回结果更新到缓存；@Caching 可以把多个缓存操作组合在一个方法上，例如一次写操作需要清理多个列表或详情缓存。使用时要明确 key 的计算方式以及操作发生在方法执行前还是执行后。

**项目源码定位：** `src/main/java/com/aimodelhub/model/service/ModelService.java`


#### 111. 数据库事务回滚了，但缓存已经被删除，会有什么影响？

**面试回答：** 数据库回滚只回滚事务管理器管理的数据库操作，不会自动恢复已经执行的 Redis 删除操作。结果可能是数据库数据没变，但缓存被清空；通常这会导致后续请求重新查库并回填，更多是缓存命中率下降。如果缓存写入了错误的新值，则可能产生更严重的不一致。需要关注缓存操作时序，必要时在事务提交后再执行缓存处理。

**项目源码定位：** `src/main/java/com/aimodelhub/model/service/ModelService.java；src/main/java/com/aimodelhub/config/CacheConfig.java`


#### 112. Redis 不可用时，业务应该继续执行还是失败？

**面试回答：** 没有统一答案，要按功能重要性和安全要求区分。普通查询缓存不可用时，可以考虑降级到数据库，但必须评估数据库承载能力；Token 黑名单检查如果失败放行，会带来已撤销 Token 仍可用的安全风险，失败关闭更安全但会影响可用性。要结合项目代码确认当前采用的是 fail-open 还是 fail-closed，不能凭经验代替源码结论。

**项目源码定位：** `src/main/java/com/aimodelhub/security/TokenBlacklistService.java；src/main/java/com/aimodelhub/config/CacheConfig.java`


#### 113. 缓存穿透、击穿、雪崩有什么区别？结合模型缓存怎么理解？

**面试回答：** 穿透是请求一个缓存和数据库都不存在的模型 ID；击穿是某个热门模型详情 key 过期时大量请求同时查数据库；雪崩是大量模型缓存同时过期或 Redis 整体不可用，导致数据库压力集中上升。可以结合参数校验、空值缓存、互斥重建、过期时间随机化和限流等方案处理，具体方案取决于一致性和可用性要求。

**项目源码定位：** `src/main/java/com/aimodelhub/model/service/ModelService.java；src/main/java/com/aimodelhub/config/CacheConfig.java`


### 模型预测与服务间调用


#### 114. 一次模型预测请求从前端到模型服务大致经历什么？

**面试回答：** 通常由前端调用预测接口，Java Controller 接收请求和文件/参数，Service 完成用户、模型和业务规则校验，再调用模型网关访问 Python 推理服务；拿到结果后，Java 层进行结果转换、保存预测记录并返回统一响应。实际执行顺序、文件先存储还是先推理、失败时是否保存记录，要以当前 PredictController 和相关 Service 实现为准。

**项目源码定位：** `src/main/java/com/aimodelhub/predict/controller/PredictController.java；src/main/java/com/aimodelhub/predict/service/；src/main/java/com/aimodelhub/predict/mapper/PredictRecordMapper.java`


#### 115. 为什么 Java 后端可以通过 HTTP 调用 Python FastAPI，而不是直接执行模型？

**面试回答：** 模型推理服务可能依赖 Python 生态中的 TensorFlow、PyTorch、OpenCV 等库。把推理能力封装为独立 HTTP 服务，可以让 Java 负责用户、权限、业务流程和数据持久化，Python 负责模型加载与推理，降低语言和依赖耦合。代价是引入网络延迟、服务可用性和接口契约管理问题。

**项目源码定位：** `src/main/java/com/aimodelhub/predict/service/；src/main/java/com/aimodelhub/config/RestTemplateConfig.java`


#### 116. 如果 FastAPI 服务没有启动，Java 后端应该如何处理？

**面试回答：** 调用可能出现连接拒绝、连接超时或读取超时。Java 应捕获并区分上游调用异常，记录必要日志，向客户端返回稳定且不泄露内部地址或堆栈的错误响应。还应设置合理的连接和读取超时；生产场景可以进一步考虑重试边界、熔断、限流和健康检查。不能无限重试导致请求堆积。

**项目源码定位：** `src/main/java/com/aimodelhub/predict/service/；src/main/java/com/aimodelhub/config/RestTemplateConfig.java；src/main/java/com/aimodelhub/common/exception/GlobalExceptionHandler.java`


#### 117. 为什么预测结果通常还要保存到数据库？

**面试回答：** 保存预测记录可以支持用户查看历史记录、管理员审计、统计分析和问题追踪。记录字段应围绕业务需要设计，例如用户、模型、原始文件名、文件地址、预测结果、耗时和创建时间。需要注意敏感数据、文件生命周期和记录保留策略。

**项目源码定位：** `src/main/java/com/aimodelhub/predict/entity/PredictRecord.java；src/main/java/com/aimodelhub/predict/mapper/PredictRecordMapper.java；src/main/resources/mapper/PredictRecordMapper.xml`


#### 118. 为什么预测接口需要考虑登录认证和用户数据隔离？

**面试回答：** 预测可能消耗计算资源，也可能产生与用户相关的文件和历史记录。认证可以识别请求用户，授权和数据范围校验则确保用户只能访问允许的数据，不能仅依赖前端传入 userId。管理员是否拥有额外权限也应由后端明确控制。

**项目源码定位：** `src/main/java/com/aimodelhub/predict/controller/PredictController.java；src/main/java/com/aimodelhub/security/`


#### 119. Java 后端调用模型服务时，为什么要做结果映射？

**面试回答：** Python 服务返回的数据结构不一定适合直接暴露给前端。Java 可以把上游响应转换为统一 VO，屏蔽内部字段差异、规范错误信息和数据类型，也便于未来替换模型服务。映射时要处理缺失字段、异常响应和版本兼容，避免把上游原始响应不加判断地透传。

**项目源码定位：** `src/main/java/com/aimodelhub/predict/service/PredictResultMapper.java；src/main/java/com/aimodelhub/predict/vo/PredictResultVO.java`


### AOP、限流与审计


#### 120. AOP 中切面、切点、通知分别是什么？

**面试回答：** 切面是横切逻辑的组织单元；切点用于匹配哪些连接点需要增强；通知定义在匹配位置执行什么逻辑，例如前置、后置、返回、异常或环绕通知。Spring AOP 通常通过代理对象在方法调用边界织入增强逻辑。

**项目源码定位：** `src/main/java/com/aimodelhub/ratelimit/RateLimitAspect.java；src/main/java/com/aimodelhub/admin/audit/AuditAspect.java`


#### 121. @Around 环绕通知的执行流程是什么？

**面试回答：** 环绕通知接收 ProceedingJoinPoint，可以在调用目标方法前后执行逻辑；调用 proceed() 才会继续执行目标方法，也可以根据需要捕获异常、记录耗时或决定是否继续调用。若忘记调用 proceed()，目标方法可能不会执行；若错误地多次调用，目标方法可能被执行多次。

**项目源码定位：** `src/main/java/com/aimodelhub/ratelimit/RateLimitAspect.java；src/main/java/com/aimodelhub/admin/audit/AuditAspect.java`


#### 122. 固定窗口和滑动窗口限流有什么区别？

**面试回答：** 固定窗口按固定时间段计数，简单但窗口边界可能出现短时间突发流量；滑动窗口按当前时刻向前滚动的时间范围统计请求，能更平滑地限制单位时间请求数，但实现和存储开销通常更高。项目采用何种窗口、计数粒度和并发控制方式，应以 SlidingWindowRateLimiter 的代码为准。

**项目源码定位：** `src/main/java/com/aimodelhub/ratelimit/SlidingWindowRateLimiter.java；src/main/java/com/aimodelhub/ratelimit/RateLimitAspect.java`


#### 123. 为什么限流触发后常返回 HTTP 429？

**面试回答：** 429 表示客户端在一定时间内发送了过多请求，语义比通用 500 更准确。响应可以包含重试提示，但是否提供 Retry-After 要看具体实现。限流规则需要考虑用户、IP、接口或业务维度，避免误伤正常请求。

**项目源码定位：** `src/main/java/com/aimodelhub/ratelimit/RateLimitAspect.java；src/main/java/com/aimodelhub/common/exception/RateLimitException.java`


#### 124. 管理员操作为什么需要审计日志？

**面试回答：** 审计日志用于追踪谁在什么时间对什么资源执行了什么操作，以及操作结果。它有助于问题排查、安全审计和责任追溯。日志应避免记录密码、Token 等敏感信息；记录失败操作也可能有价值。审计记录是否与业务事务绑定，需要根据可靠性要求设计。

**项目源码定位：** `src/main/java/com/aimodelhub/admin/audit/AuditAspect.java；src/main/java/com/aimodelhub/admin/audit/AuditLog.java`


### 文件存储、异常与测试


#### 125. MinIO 属于什么类型的存储？为什么项目使用对象存储？

**面试回答：** MinIO 是兼容 S3 API 的对象存储系统，适合保存图片、模型文件等非结构化文件。数据库更适合保存文件元数据和业务关系，文件本体放对象存储可以避免业务表直接承载大块二进制内容。需要设计访问权限、桶策略、文件命名和生命周期。

**项目源码定位：** `src/main/java/com/aimodelhub/config/MinioConfig.java；src/main/java/com/aimodelhub/service/FileStorageService.java`


#### 126. 文件上传为什么要限制大小并校验类型？

**面试回答：** 限制大小可以避免单个请求占用过多内存、磁盘和网络资源；类型校验可以降低上传不符合业务要求文件的风险。但不能只相信客户端传来的 Content-Type 或文件扩展名，必要时还要检查文件内容特征，并使用随机文件名、权限隔离和安全扫描等措施。

**项目源码定位：** `src/main/java/com/aimodelhub/service/FileStorageService.java；src/main/java/com/aimodelhub/controller/FileProxyController.java；配置文件中的 multipart 限制`


#### 127. DTO、Entity、VO 分别是什么？为什么不直接把 Entity 返回给前端？

**面试回答：** DTO 用于接口输入或服务间传输数据；Entity 通常对应持久化数据模型；VO 用于组织返回给调用方的数据。分开使用可以避免把数据库内部字段、密码哈希、内部状态等意外暴露，也能让接口结构不被表结构变化直接牵动。实际项目命名可能不完全统一，要看各层职责。

**项目源码定位：** `src/main/java/com/aimodelhub/auth/dto/；src/main/java/com/aimodelhub/predict/entity/；src/main/java/com/aimodelhub/predict/vo/`


#### 128. 为什么过滤器中的异常不一定由 GlobalExceptionHandler 处理？

**面试回答：** GlobalExceptionHandler 通常通过 Spring MVC 异常解析机制处理进入 DispatcherServlet 后的异常；Spring Security 过滤器链位于 MVC Controller 调用之前，认证失败和权限拒绝通常由 AuthenticationEntryPoint、AccessDeniedHandler 等安全组件处理。因此需要区分过滤器链异常和 Controller/Service 业务异常。

**项目源码定位：** `src/main/java/com/aimodelhub/security/SecurityConfig.java；src/main/java/com/aimodelhub/common/exception/GlobalExceptionHandler.java`


#### 129. 单元测试和集成测试有什么区别？

**面试回答：** 单元测试尽量隔离被测对象，验证单个类或方法的行为，通常会用 Mock 隔离依赖；集成测试会让多个真实组件协作，例如 Spring 容器、Mapper、数据库或 Redis，以验证组件连接和配置是否正确。测试范围越大，环境准备和执行成本通常也越高。

**项目源码定位：** `src/test/java/com/aimodelhub/model/ModelServiceIntegrationTest.java；src/test/java/com/aimodelhub/security/TokenBlacklistServiceTest.java`


#### 130. 接口返回 500 时，如何结合项目分层排查？

**面试回答：** 先记录请求路径、时间、用户和 traceId（如果项目有）；再查看后端异常堆栈，定位发生在 Controller 参数处理、Service 业务逻辑、Mapper SQL、Redis、MinIO 还是 Python 上游调用。随后核对请求参数、数据库状态、配置和依赖服务，最后通过对应测试或接口复现验证修复。不要只根据前端提示猜测。

**项目源码定位：** `src/main/java/com/aimodelhub/common/exception/GlobalExceptionHandler.java；相关 Controller、Service、Mapper 与配置类`


## 第十二章 通用知识补充（建议掌握）

### 131. DTO、Entity、VO 的区别是什么？
面试回答：DTO 用于接口输入或服务间传输数据；Entity 通常表示持久化实体；VO 用于组织返回给调用方的数据。分层使用可以避免数据库字段直接暴露给前端，也能减少接口结构与表结构的耦合。项目中的具体命名可能不同，关键是看职责。

### 132. Spring Security 过滤器链中的认证失败和 MVC 全局异常处理有什么区别？
面试回答：Spring Security 过滤器链通常在 DispatcherServlet 和 Controller 处理之前执行。认证失败、权限拒绝一般由 AuthenticationEntryPoint、AccessDeniedHandler 等安全组件处理；Controller、Service 调用过程中抛出的异常，通常由 @RestControllerAdvice 配合 @ExceptionHandler 处理。不能认为所有异常都会进入 GlobalExceptionHandler。

### 133. HTTP 400、401、403、404、413、429、500、502 分别表示什么？
面试回答：400 通常表示请求参数或格式不正确；401 表示未通过身份认证；403 表示已识别身份但无权访问；404 表示资源不存在或按业务策略不可见；413 表示请求体过大；429 表示请求过于频繁；500 表示服务端内部错误；502 常用于网关或上游服务返回异常响应。实际项目应保持状态码和业务错误码语义一致。

### 134. 什么是幂等？普通 CRUD 接口都需要幂等吗？
面试回答：幂等表示同一操作执行一次或多次，业务最终结果相同。查询通常天然幂等；设置某个资源状态为指定值通常容易做到幂等；创建订单、扣减库存等操作则要防止重复提交。可以通过唯一业务号、数据库唯一约束、幂等记录和状态机等方式实现。

### 135. 为什么要设置 HTTP 客户端连接超时和读取超时？
面试回答：连接超时限制建立连接所等待的时间，读取超时限制连接建立后等待响应数据的时间。没有合理超时，外部服务卡住时可能长期占用请求线程和连接资源。超时设置要结合业务响应时间、线程池和上游服务能力，不能简单设得越短越好。

### 136. 为什么不能对所有失败请求都无限重试？
面试回答：无限重试会放大故障、占用线程和连接，甚至形成请求风暴。重试应限制次数、设置退避策略，并判断操作是否幂等；对于不确定是否成功的写操作，盲目重试可能造成重复数据。必要时结合熔断、限流和降级。

## 第十三章 面试表达与复习方法

### 项目问题统一回答框架

建议按以下顺序回答项目题：

1. **业务目的：** 这个功能解决什么问题？
2. **请求入口：** 前端请求哪个接口，进入哪个 Controller？
3. **业务流程：** Service 做了哪些判断和处理？
4. **数据访问：** 调用了哪个 Mapper，涉及哪些表或 SQL？
5. **关键技术：** 为什么使用 Redis、JWT、AOP、MinIO 或 HTTP 调用？
6. **异常与边界：** 参数错误、数据不存在、依赖服务故障时怎么处理？
7. **个人职责：** 自己实际负责、修改、测试和理解了哪些部分？

### 掌握程度建议

- **必须掌握：** Java 基础、集合、Spring IoC/AOP/事务、MyBatis、MySQL 索引与事务、Redis 缓存、JWT 基本流程、项目核心调用链。
- **理解即可：** JVM GC 细节、并发容器内部实现、MinIO 细节、限流算法的复杂实现。
- **暂时选读：** 微服务注册发现、Kafka 集群、Elasticsearch 分片选举等与目标岗位和项目无关的深层内容。

### 第一轮自测标准

一题达到以下标准，就可以暂时进入下一题：

- 能不看答案说出核心定义；
- 能解释为什么需要它，而不只是背概念；
- 能说出一个项目中的使用场景；
- 能回答一个常见追问；
- 不确定的实现细节会明确说明需要回到源码确认，而不是编造。

> 第一轮目标：能用自己的话讲清楚，不追求背出教材原句。项目经历只讲自己实际做过、验证过并能解释的内容。
