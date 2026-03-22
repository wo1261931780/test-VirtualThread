# Spring NextGen Showcase

![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.4.5-brightgreen.svg)
![Java](https://img.shields.io/badge/Java-17%2B%20%2821%20Recommended%29-blue.svg)

本项目是一个演示应用程序，旨在展示 **Spring Boot 3** 和现代 Java 中可用的先进、“下一代”特性。

它提供了一个参考架构，总结了如何集成 **虚拟线程 (Virtual Threads)**、**声明式 HTTP 客户端 (@HttpExchange)** 以及 **Spring 授权服务器 (Spring Authorization Server)** 等核心组件。

## 🌟 核心演示特性

### 1. 🧵 虚拟线程 (Project Loom)
展示了如何使用现代 Java 虚拟线程来实现高并发处理能力。
- **关联代码**: `VirtualThreadDemo.java`
- 亮点:
  - 使用 `Thread.ofVirtual()` 创建虚拟线程
  - 使用 `Executors.newVirtualThreadPerTaskExecutor()` 为每个任务分配虚拟线程

### 2. 🌐 声明式 HTTP 客户端
演示了 Spring 6 中新增的声明式 HTTP 客户端定义方式，用来替代传统的 `RestTemplate`。
- **关联代码**: `UserClient.java`, `StockServiceClient.java`
- 亮点:
  - 使用 `@HttpExchange`, `@GetExchange` 等注解
  - 与 `WebClient` 和 `HttpServiceProxyFactory` 深度集成

### 3. 🛡️ Spring Authorization Server
在 Spring 生态系统中直接构建完整的 OAuth2 / OIDC 授权服务器。
- **关联代码**: `AuthServerConfig.java`
- 亮点:
  - OIDC / OAuth2 协议端点配置
  - 使用 `RegisteredClientRepository` 进行客户端管理
  - 基于内存的 `UserDetailsService` 身份验证以及 RSA 密钥对生成 (`JWKSource`)

### 4. 🗄️ 持久化与 Web 支持
- **Web & 响应式**: 同时引入了 `spring-boot-starter-web` (传统的同步阻塞栈) 和 `spring-boot-starter-webflux` (响应式非阻塞栈)。
- **数据访问**: 利用 `spring-boot-starter-data-jpa` 并在内存中搭配 `H2` 数据库，展示简单、零配置的数据持久化功能。包括 `User`, `Order`, `Product` 等实体类。

### 5. 📊 可观测性与监控
- 借助 `spring-boot-starter-actuator` 和 `micrometer-registry-prometheus` 自动暴露兼容 Prometheus 格式的应用程序运行指标。
- 访问地址: `http://localhost:8080/actuator/prometheus`

---

## 🚀 快速开始

### 环境依赖
- **Java 21+** (建议使用 JDK 21 或更高版本以完整体验虚拟线程特性，最低要求 JDK 17)。
- **Maven** (已包含 Maven Wrapper 脚本)。

### 运行应用程序

1. **克隆项目仓库:**
   ```bash
   git clone <repository-url>
   cd spring-nextgen-showcase
   ```

2. **使用 Maven Wrapper 运行:**
   ```bash
   ./mvnw spring-boot:run
   ```

3. **验证应用状态:**
   - 应用程序将在 `8080` 端口上启动。
   - 访问健康检查端点: `http://localhost:8080/actuator/health`

### 测试授权服务器
你可以通过在浏览器中访问以下授权端点来启动一次 OAuth2 登录流程：
```
http://localhost:8080/oauth2/authorize?response_type=code&client_id=oidc-client&scope=openid&redirect_uri=http://localhost:3000/callback
```
*(默认账号: `user` / 密码: `password`)*

---

## 📝 许可证 (License)
有关详细信息，请参阅 `LICENSE` 文件。