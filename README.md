# test-VirtualThread - Spring NextGen Showcase

![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.4.5-brightgreen.svg)
![Java](https://img.shields.io/badge/Java-17%2B%20%2821%20Recommended%29-blue.svg)
![Virtual Threads](https://img.shields.io/badge/Virtual%20Threads-Project%20Loom-orange.svg)
![License](https://img.shields.io/badge/License-AGPL--3.0-blue.svg)

> 演示 Spring Boot 3 和现代 Java 的虚拟线程、声明式 HTTP 客户端、授权服务器等先进特性

## 📖 项目简介

test-VirtualThread 是一个演示应用程序,旨在展示 **Spring Boot 3** 和现代 Java 中可用的先进特性。它提供了一个参考架构,总结了如何集成 **虚拟线程 (Virtual Threads)**、**声明式 HTTP 客户端 (@HttpExchange)** 以及 **Spring 授权服务器 (Spring Authorization Server)** 等核心组件。

## 🏗️ 系统架构

```mermaid
graph TB
    subgraph Client[客户端层]
        WebClient[Web客户端]
        MobileApp[移动应用]
    end
    
    subgraph Gateway[网关层]
        Auth[认证授权<br/>OAuth2/OIDC]
        Router[路由转发]
    end
    
    subgraph Application[应用层 Spring Boot 3.4.5]
        VirtualThread[虚拟线程池<br/>Project Loom]
        HttpExchange[声明式HTTP客户端<br/>@HttpExchange]
        Reactive[响应式栈<br/>WebFlux]
        WebMVC[传统MVC栈<br/>Web]
    end
    
    subgraph Security[安全层]
        AuthServer[授权服务器<br/>Spring Authorization Server]
        ResourceServer[资源服务器]
        JWT[JWT令牌]
    end
    
    subgraph Data[数据层]
        H2[(H2数据库<br/>内存数据库)]
        JPA[Spring Data JPA]
    end
    
    subgraph Monitoring[监控层]
        Actuator[Spring Actuator]
        Prometheus[Prometheus指标]
    end
    
    Client --> Gateway
    Gateway --> Application
    Auth --> AuthServer
    AuthServer --> JWT
    Application --> VirtualThread
    Application --> HttpExchange
    Application --> Data
    Data --> H2
    Application --> Monitoring
    Monitoring --> Prometheus
    
    style Client fill:#e1f5ff
    style Gateway fill:#f0f9ff
    style Application fill:#fff4e6
    style Security fill:#ffe6f0
    style Data fill:#f3f9ff
    style Monitoring fill:#f0fff4
```

## 🌟 核心演示特性

### 1. 🧵 虚拟线程 (Project Loom)
展示了如何使用现代 Java 虚拟线程来实现高并发处理能力。

**关联代码**: `VirtualThreadDemo.java`

**亮点**:
- 使用 `Thread.ofVirtual()` 创建虚拟线程
- 使用 `Executors.newVirtualThreadPerTaskExecutor()` 为每个任务分配虚拟线程
- 大幅提升并发处理能力,降低线程开销

**示例代码**:
```java
// 创建虚拟线程
Thread virtualThread = Thread.ofVirtual().start(() -> {
    System.out.println("Running in virtual thread");
});

// 使用虚拟线程执行器
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    executor.submit(() -> {
        // 任务逻辑
    });
}
```

### 2. 🌐 声明式 HTTP 客户端
演示了 Spring 6 中新增的声明式 HTTP 客户端定义方式,用来替代传统的 `RestTemplate`。

**关联代码**: `UserClient.java`, `StockServiceClient.java`

**亮点**:
- 使用 `@HttpExchange`, `@GetExchange` 等注解
- 与 `WebClient` 和 `HttpServiceProxyFactory` 深度集成
- 简化 HTTP 客户端代码,提升可维护性

**示例代码**:
```java
@HttpExchange
public interface UserClient {
    @GetExchange("/users/{id}")
    User getUser(@PathVariable Long id);
    
    @PostExchange("/users")
    User createUser(@RequestBody User user);
}

// 配置
@Configuration
public class WebClientConfig {
    @Bean
    public UserClient userClient() {
        WebClient webClient = WebClient.create("https://api.example.com");
        HttpServiceProxyFactory factory = HttpServiceProxyFactory
            .builder(WebClientAdapter.forClient(webClient))
            .build();
        return factory.createClient(UserClient.class);
    }
}
```

### 3. 🛡️ Spring Authorization Server
在 Spring 生态系统中直接构建完整的 OAuth2 / OIDC 授权服务器。

**关联代码**: `AuthServerConfig.java`

**亮点**:
- OIDC / OAuth2 协议端点配置
- 使用 `RegisteredClientRepository` 进行客户端管理
- 基于内存的 `UserDetailsService` 身份验证
- RSA 密钥对生成 (`JWKSource`)

**配置示例**:
```java
@Configuration
public class AuthServerConfig {
    @Bean
    public RegisteredClientRepository registeredClientRepository() {
        RegisteredClient client = RegisteredClient.withId(UUID.randomUUID().toString())
            .clientId("client")
            .clientSecret("{noop}secret")
            .authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE)
            .redirectUri("http://localhost:8080/login/oauth2/code/client")
            .scope("read")
            .scope("write")
            .build();
        return new InMemoryRegisteredClientRepository(client);
    }
}
```

### 4. 🗄️ 持久化与 Web 支持
- **Web & 响应式**: 同时引入了 `spring-boot-starter-web` (传统的同步阻塞栈) 和 `spring-boot-starter-webflux` (响应式非阻塞栈)
- **数据访问**: 利用 `spring-boot-starter-data-jpa` 并在内存中搭配 `H2` 数据库
- **实体类**: `User`, `Order`, `Product` 等实体类

### 5. 📊 可观测性与监控
借助 `spring-boot-starter-actuator` 和 `micrometer-registry-prometheus` 自动暴露兼容 Prometheus 格式的应用程序运行指标。

**访问地址**: `http://localhost:8080/actuator/prometheus`

**监控指标**:
- JVM 内存使用情况
- HTTP 请求统计
- 数据库连接池状态
- 虚拟线程状态

## 🛠️ 技术栈

| 技术 | 版本 | 说明 |
|------|------|------|
| **Spring Boot** | 3.4.5 | 核心框架 |
| **Java** | 17+ (21推荐) | 开发语言 |
| **Virtual Threads** | Java 21 | 虚拟线程 |
| **Spring Authorization Server** | - | 授权服务器 |
| **Spring Data JPA** | - | 数据持久化 |
| **H2 Database** | - | 内存数据库 |
| **Spring WebFlux** | - | 响应式Web |
| **Spring Actuator** | - | 应用监控 |
| **Prometheus** | - | 指标收集 |

## 🚀 快速开始

### 环境依赖
- **Java 21+** (建议使用 JDK 21 或更高版本以完整体验虚拟线程特性,最低要求 JDK 17)
- **Maven** (已包含 Maven Wrapper 脚本)

### 运行应用程序

```bash
# 克隆项目
git clone https://github.com/your-username/test-VirtualThread.git
cd test-VirtualThread

# 使用 Maven 运行
mvn clean install
mvn spring-boot:run

# 或使用 Maven Wrapper
./mvnw spring-boot:run
```

### 访问服务

- **应用地址**: http://localhost:8080
- **Actuator端点**: http://localhost:8080/actuator
- **Prometheus指标**: http://localhost:8080/actuator/prometheus
- **健康检查**: http://localhost:8080/actuator/health

## 📁 项目结构

```
test-VirtualThread/
├── src/main/java/
│   └── wo1261931780/
│       ├── config/              # 配置类
│       │   ├── AuthServerConfig.java    # 授权服务器配置
│       │   ├── WebClientConfig.java     # HTTP客户端配置
│       │   └── VirtualThreadConfig.java # 虚拟线程配置
│       ├── controller/          # 控制器
│       ├── client/              # HTTP客户端
│       │   ├── UserClient.java          # 用户客户端
│       │   └── StockServiceClient.java  # 股票服务客户端
│       ├── service/             # 服务层
│       ├── entity/              # 实体类
│       │   ├── User.java
│       │   ├── Order.java
│       │   └── Product.java
│       ├── demo/                # 演示代码
│       │   └── VirtualThreadDemo.java   # 虚拟线程演示
│       └── Application.java     # 主类
├── src/main/resources/
│   ├── application.yml          # 配置文件
│   └── data.sql                 # 初始化数据
└── pom.xml
```

## 🔧 核心配置

### 虚拟线程配置
```yaml
spring:
  threads:
    virtual:
      enabled: true  # 启用虚拟线程
```

### 授权服务器配置
```yaml
spring:
  security:
    oauth2:
      authorizationserver:
        client:
          client:
            registration:
              client-id: client
              client-secret: secret
              authorization-grant-types: authorization_code
              scopes: read,write
```

### Actuator配置
```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,prometheus
  metrics:
    export:
      prometheus:
        enabled: true
```

## 📊 性能对比

### 虚拟线程 vs 平台线程

| 指标 | 平台线程 | 虚拟线程 |
|------|----------|----------|
| 内存占用 | ~1MB | ~1KB |
| 创建开销 | 高 | 极低 |
| 上下文切换 | 重 | 轻 |
| 并发数量 | 受限于线程数 | 大幅提升 |

## 🔍 测试演示

### 1. 虚拟线程测试
```bash
curl http://localhost:8080/virtual-thread/demo
```

### 2. HTTP客户端测试
```bash
curl http://localhost:8080/api/users/1
```

### 3. OAuth2授权测试
```bash
# 获取授权码
http://localhost:8080/oauth2/authorize?response_type=code&client_id=client&scope=read

# 获取访问令牌
curl -X POST http://localhost:8080/oauth2/token \
  -d "grant_type=authorization_code" \
  -d "code=<authorization_code>" \
  -u client:secret
```

## 📝 学习要点

### 虚拟线程优势
1. **轻量级**: 内存占用极低,可创建数百万个虚拟线程
2. **高效**: 减少线程切换开销
3. **简化编程**: 编写同步代码获得异步性能

### 声明式HTTP客户端优势
1. **代码简洁**: 使用注解定义HTTP接口
2. **类型安全**: 编译时检查接口定义
3. **易于维护**: 接口与实现分离

### Spring Authorization Server优势
1. **标准化**: 完整支持OAuth2/OIDC协议
2. **集成性**: 与Spring生态无缝集成
3. **灵活性**: 支持自定义认证流程

## 📖 参考资料

- [Project Loom](https://openjdk.org/projects/loom/)
- [Spring Authorization Server](https://spring.io/projects/spring-authorization-server)
- [Spring Framework 6 Documentation](https://docs.spring.io/spring-framework/reference/)

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request!

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情

## 📮 联系方式

- 作者: junw
- Email: wo1261931780@gmail.com
- GitHub: [@wo1261931780](https://github.com/wo1261931780)

## 🙏 致谢

感谢 Spring 团队和 Oracle 为 Java 生态带来的创新特性!

---

**说明**: 本项目主要用于学习和演示 Spring Boot 3 和现代 Java 的新特性。建议使用 JDK 21 以获得最佳体验。
