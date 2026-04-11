# econ-parent
Econ parent project, relying on version management in a unified way.

## 依赖架构

### 基础环境
- JDK版本：25
- Maven编译配置：UTF-8编码

### 核心框架
| 依赖 | 版本 | 说明 |
|------|------|------|
| Spring Boot | 4.0.5 | 基础框架 |
| Spring Cloud | 2025.1.1 | 微服务框架 |
| Spring Cloud Alibaba | 2025.1.0.0 | 阿里巴巴微服务生态 |

### 核心业务依赖
| 依赖 | 版本 | 说明 |
|------|------|------|
| Dubbo | 3.2.15 | 分布式服务框架 |
| MyBatis-Plus | 3.5.12 | ORM框架 |
| Dynamic Datasource | 4.3.0 | 动态数据源 |

### 工具类依赖
| 依赖 | 版本 | 说明 |
|------|------|------|
| Commons Pool2 | 2.12.0 | 连接池 |
| MapStruct | 1.6.3 | Bean映射工具 |
| Lombok | 1.18.36 | 代码简化工具 |

### 公共依赖
- Lombok (optional)
- Spring Cloud Starter Bootstrap
- Spring Boot Starter Test (test scope)

## 环境配置

### 环境 profile
- **dev**：开发环境（默认）
- **test**：测试环境
- **uat**：预发布环境
- **pre**：准生产环境
- **prod**：生产环境

### 部署 profile
- **snapshot**：快照版本部署
- **release**：正式版本部署

## 构建配置

### 核心插件
- Spring Boot Maven Plugin
- Maven Source Plugin
- Maven Javadoc Plugin
- Maven GPG Plugin

### 插件管理
- Spring Boot Maven Plugin
- Maven Assembly Plugin
- Maven Compiler Plugin
- Maven Shade Plugin
- Maven Source Plugin
- Maven Javadoc Plugin
- Docker Maven Plugin
- ClassFinal Maven Plugin
- Maven GPG Plugin

## 仓库配置

### 远程仓库
- 阿里云仓库：https://maven.aliyun.com/repository/public

### 分发管理
- 快照仓库：https://s01.oss.sonatype.org/service/local/staging/deploy/maven2/
- 发布仓库：https://s01.oss.sonatype.org/service/local/staging/deploy/maven2/
