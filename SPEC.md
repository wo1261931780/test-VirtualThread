# test-VirtualThread - 项目规格说明书

## 1. 项目概述

- **项目名称**: test-VirtualThread (spring-nextgen-showcase)
- **项目类型**: Spring Boot Maven 项目
- **Java 版本**: 17+ (推荐 21)
- **Spring Boot 版本**: 3.4.5

## 2. 技术栈

| 技术 | 版本 | 说明 |
|------|------|------|
| Spring Boot | 3.4.5 | 核心框架 |
| Spring Web | - | 传统 Web 栈 |
| Spring WebFlux | - | 响应式 Web |
| Spring Data JPA | - | 数据持久化 |
| H2 Database | - | 内存数据库 |
| Spring Security | - | 安全框架 |
| OAuth2 Authorization Server | - | 授权服务器 |
| Micrometer + Prometheus | - | 监控指标 |

## 3. 构建状态

### 3.1 Maven 编译
- **mvn compile**: ❌ 失败

### 3.2 错误信息
```
[ERROR] location: class java.util.concurrent.Executors
VirtualThreadDemo.java:[74,38] incompatible types: try-with-resources not applicable to variable type
  (java.util.concurrent.ExecutorService cannot be converted to java.lang.AutoCloseable)
VirtualThreadDemo.java:[116,38] incompatible types: try-with-resources not applicable to variable type

VirtualThreadDemo.java:[89,46] cannot find symbol: method getName()
VirtualThreadDemo.java:[106,29] cannot find symbol: method getName()
...
```

### 3.3 主要问题
1. **VirtualThreadDemo.java**: `ExecutorService` 不能用于 try-with-resources（Lombok 未正确处理）
2. **OrderService.java**: 找不到 `getName()`, `getId()` 方法（Lombok getter 未生成）
3. Lombok 注解处理器可能未正确运行

## 4. 项目结构

```
test-VirtualThread/
├── src/main/java/wo1261931780/
│   ├── config/              # 配置类
│   │   ├── AuthServerConfig.java
│   │   ├── WebClientConfig.java
│   │   └── VirtualThreadConfig.java
│   ├── controller/          # 控制器
│   ├── client/              # HTTP 客户端
│   │   ├── UserClient.java
│   │   └── StockServiceClient.java
│   ├── service/            # 服务层
│   ├── entity/             # 实体类
│   │   ├── User.java
│   │   ├── Order.java
│   │   └── Product.java
│   └── demo/               # 演示代码
│       └── VirtualThreadDemo.java
├── src/main/resources/
│   ├── application.yml
│   └── data.sql
└── pom.xml
```

## 5. .gitignore 检查

已配置忽略以下内容:
- `*.class` 编译文件
- `*.log` 日志文件
- `*.jar`, `*.war` 打包文件
- `hs_err_pid*`, `replay_pid*` JVM 错误日志
- `.DS_Store`

**注意**: `.gitignore` 配置较为简单，缺少 `target/` 目录的忽略配置

## 6. README 状态

- ✅ README.md 存在
- 内容: Spring Boot 3 新特性演示项目
- 包含: 项目简介、系统架构、核心演示特性（虚拟线程、声明式 HTTP 客户端、授权服务器）、快速开始、技术栈、性能对比等
- 内容非常详细（364 行）

## 7. 修复建议

1. **Lombok 处理问题**:
   - 确保 Maven 编译时 Lombok 注解处理器正确执行
   - 检查 IDE 是否正确配置了 Lombok 支持
   - 考虑在 pom.xml 中明确配置 Lombok 的 annotation processor paths

2. **VirtualThreadDemo.java 修复**:
   - `ExecutorService` 不是 `AutoCloseable`，需要手动调用 `shutdown()`
   - 或者使用 `Executors.newSingleThreadExecutor()` 包装

3. **OrderService.java 修复**:
   - 确保 `Product.java` 实体类上标注了 `@Data` 或 `@Getter/@Setter`
   - 重新编译使 Lombok 生成 getter/setter

4. **建议执行**:
   ```bash
   mvn clean compile -X  # 查看详细编译过程
   ```

## 8. 最后更新时间

2026-04-22