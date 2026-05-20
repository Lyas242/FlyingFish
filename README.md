# 🎫 FlyingFish-Pro

FlyingFish-Pro 是一个基于 Spring Cloud Alibaba 微服务架构的高并发在线售票系统，模拟大麦网核心业务场景。项目涵盖了从用户注册登录、节目管理、库存扣减、订单生成、支付结算到后台管理的完整业务流程，并针对高并发场景下的各类疑难问题提供了生产级落地方案。

## 🏗️ 技术栈

| 类别 | 技术 |
|------|------|
| **语言** | Java 17 |
| **核心框架** | Spring Boot 3.3.0、Spring Cloud 2023.0.2、Spring Cloud Alibaba 2023.0.1.0 |
| **ORM / 分库分表** | MyBatis-Plus 3.5.7、Apache ShardingSphere 5.3.2 |
| **数据库连接池** | Druid 1.1.10 |
| **消息队列** | Apache Kafka |
| **缓存 / 分布式协调** | Redis (Jedis)、Redisson 3.32.0 |
| **注册中心 / 配置中心** | Nacos 2.0.3 |
| **流量控制 / 熔断降级** | Sentinel、Hystrix |
| **分布式事务** | Seata (AT 模式) |
| **搜索引擎** | Elasticsearch |
| **API 网关** | Spring Cloud Gateway |
| **安全认证** | Sa-Token 1.43.0、Jasypt 加密 |
| **接口文档** | Knife4j 4.3.0 (OpenAPI 3.0) |
| **可观测性** | SkyWalking 9.4.0、Prometheus、Spring Boot Actuator |
| **验证码** | AJ Captcha |
| **前端** | Vue 3 + Element Plus + Vite + Pinia |
| **构建工具** | Maven |

## 📐 项目架构

```
FlyingFish-Pro
├── damai-common                          # 公共模块（枚举、常量、异常、工具类、JWT、ThreadLocal）
├── damai-redis-tool-framework            # Redis 工具框架（缓存、Stream 消息流）
├── damai-elasticsearch-framework         # Elasticsearch 工具框架
├── damai-id-generator-framework          # 分布式 ID 生成器（雪花算法）
├── damai-redisson-framework              # Redisson 框架（分布式锁、布隆过滤器、限流、延迟队列）
├── damai-thread-pool-framework           # 线程池框架
├── damai-captcha-manage-framework        # 验证码管理框架
├── damai-spring-cloud-framework          # Spring Cloud 公共服务框架（分片算法、灰度路由）
├── damai-server-client                   # Feign 远程调用客户端集合
│   ├── damai-user-client                 # 用户服务客户端
│   ├── damai-order-client                # 订单服务客户端
│   ├── damai-pay-client                  # 支付服务客户端
│   ├── damai-program-client              # 节目服务客户端
│   ├── damai-base-data-client            # 基础数据服务客户端
│   └── damai-customize-client            # 压测服务客户端
├── damai-server                          # 微服务模块集合
│   ├── damai-gateway-service             # API 网关服务（路由转发、限流、Kafka 数据采集）
│   ├── damai-user-service                # 用户服务（注册、登录、布隆过滤器去重）
│   ├── damai-program-service             # 节目服务（节目/座位/库存管理，v1/v2 版本）
│   ├── damai-order-service               # 订单服务（高并发下单 v1~v4，延迟队列，分布式事务）
│   ├── damai-pay-service                 # 支付服务（支付宝对接，订单结算）
│   ├── damai-base-data-service           # 基础数据服务（字典/区域等参考数据）
│   ├── damai-customize-service           # 动态压测服务
│   ├── damai-admin-service               # 后台管理服务（监控、查询、数据处理）
│   ├── damai-mybatis-plus-service        # 代码生成器服务
│   └── damai-migrate-service             # 数据迁移服务
├── sql/cloud/                            # 数据库初始化 SQL 脚本（分库分表结构）
└── vue3/                                 # 前端项目（Vue 3 + Element Plus）
```

### 🎯 分库分表方案

- **基因法均匀分布**：分库使用中高位 bit，分表使用低位 bit，两者独立分布，资源利用率从 50% 提升到 100%
- **虚拟分片路由扩容**：引入虚拟分片层（8 物理分片 → 1024 虚拟分片），支持分批平滑迁移、秒级切换和快速回滚

### 🔬 动态压测

内置动态压测功能，可对不同版本接口进行实时压测对比，直观展示各版本优化效果。

### 🖥️ 后台管理系统

实时的监控与查询面板，支持：
- 余票数量、座位状态、订单状态实时监控
- Redis 与数据库数据一致性校验
- Redis 扣减余票日志、MQ 消息收发日志
- 问题排查与时间线追溯

## 🚀 快速开始

### 环境要求

- JDK 17+
- Maven 3.8+
- MySQL 8.0+
- Redis 6.0+
- Kafka 3.0+
- Nacos 2.0.3
- Elasticsearch 7.x+
- Node.js 16+ (前端)

### 1. 克隆项目

```bash
git clone https://github.com/YOUR_USERNAME/FlyingFish-Pro.git
cd FlyingFish-Pro
```

### 2. 初始化数据库

执行 `sql/cloud/` 目录下的 SQL 脚本，创建分库分表结构：

```bash
# 按顺序执行
mysql -u root -p < sql/cloud/1_damai_cloud_create_database.sql
mysql -u root -p < sql/cloud/damai_user_0.sql
mysql -u root -p < sql/cloud/damai_user_1.sql
mysql -u root -p < sql/cloud/damai_order_0.sql
mysql -u root -p < sql/cloud/damai_order_1.sql
mysql -u root -p < sql/cloud/damai_program_0.sql
mysql -u root -p < sql/cloud/damai_program_1.sql
mysql -u root -p < sql/cloud/damai_pay_0.sql
mysql -u root -p < sql/cloud/damai_pay_1.sql
mysql -u root -p < sql/cloud/damai_base_data.sql
mysql -u root -p < sql/cloud/damai_customize.sql
```

### 3. 启动基础设施

确保以下服务已启动：

- Nacos：`127.0.0.1:8848`
- Redis：`127.0.0.1:6379`
- Kafka：`127.0.0.1:9092`
- Elasticsearch：`127.0.0.1:9200`

### 4. 编译构建

```bash
mvn clean install -Dmaven.test.skip=true
```

### 5. 启动微服务

推荐按以下顺序启动各个服务：

1. `damai-gateway-service` — 网关服务
2. `damai-user-service` — 用户服务
3. `damai-base-data-service` — 基础数据服务
4. `damai-program-service` — 节目服务
5. `damai-order-service` — 订单服务
6. `damai-pay-service` — 支付服务
7. `damai-admin-service` — 后台管理服务
8. `damai-customize-service` — 压测服务

每个服务在各自 `application.yml` 中通过 `spring.profiles.active` 切换 `local` / `pro` 环境。

### 6. 启动前端

```bash
cd vue3
npm install
npm run dev
```

## 📖 接口文档

启动网关服务后，访问 Knife4j 聚合文档：

```
http://localhost:8080/doc.html
```

## 📄 License

Copyright © 2024. All rights reserved.
