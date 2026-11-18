# econ-parent

Econ parent project, relying on version management in a unified way.

## 依赖架构

### 基础环境

- JDK版本：25（Spring Boot 3.5.x 官方兼容范围为 Java 17 \~ 25）
- Maven要求：3.6.3+
- Maven编译配置：UTF-8编码

### 核心框架

| 依赖                   | 版本         | 说明                                          |
| -------------------- | ---------- | ------------------------------------------- |
| Spring Boot          | 3.5.16     | 基础框架（Spring Framework 6.2.x / Jakarta 命名空间） |
| Spring Cloud         | 2025.0.3   | 微服务框架，Northfields 列车，对应 Spring Boot 3.5.x   |
| Spring Cloud Alibaba | 2025.0.0.0 | 阿里巴巴微服务生态，基于 Spring Cloud 2025.0 列车         |

> 注意：Spring Cloud 2025.0.x 的开源支持已于 2026-06-30 结束（2025.0.3 为该列车最后一个 OSS 版本）；Spring Boot 3.5.x 仍在维护期内。若后续需要继续获得 Spring Cloud 开源更新，需评估升级到 Boot 4 + Cloud 2025.1 的时间点（届时 Dubbo 需等待官方 Boot 4 适配版本发布）。

### 核心业务依赖

| 依赖                 | 版本       | 构件                                      | 说明                                                  |
| ------------------ | -------- | --------------------------------------- | --------------------------------------------------- |
| Dubbo              | 3.3.7    | dubbo-spring-boot-starter               | 分布式服务框架，3.3.x 兼容 Spring Boot 1.x \~ 3.x（不支持 Boot 4） |
| MyBatis-Plus       | 3.5.18.1 | mybatis-plus-spring-boot3-starter       | ORM框架，Boot 3 专用构件                                   |
| Dynamic Datasource | 4.6.0    | dynamic-datasource-spring-boot3-starter | 动态数据源，Boot 3 专用构件                                   |

### 工具类依赖

| 依赖            | 版本      | 说明                                              |
| ------------- | ------- | ----------------------------------------------- |
| Commons Pool2 | 2.12.0  | 连接池                                             |
| MapStruct     | 1.6.3   | Bean映射工具                                        |
| Lombok        | 1.18.46 | 代码简化工具（与 Spring Boot 3.5.16 官方管理版本一致，支持 JDK 25） |

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

### 部署 profile 与版本路由

版本路由由 Maven 与 central-publishing-maven-plugin（0.7.0+）原生按版本号后缀自动完成，无需人工选择目标仓库：

- **快照版本**（version 以 `-SNAPSHOT` 结尾）：执行 `mvn deploy`，自动直传 Central Portal 快照仓库，不做校验、不要求 GPG 签名
- **正式版本**（纯数字版本，如 `1.0.0`）：执行 `mvn clean deploy -Ppublish`，publish profile 激活 GPG 签名，central-publishing 插件将 bundle 上传 Portal 校验并自动发布到 Maven Central
- **GitHub Packages**：执行 `mvn deploy -Pgithub`，github profile 将 distributionManagement 整体覆盖到 GitHub Packages（快照与正式版均支持，无需 GPG）；不要与 publish profile 在同一条命令中激活
- 注意：`2.0.0-RC1` 等非 SNAPSHOT 限定版本按 Maven 原生语义同样走正式仓库通道

### CI/CD 自动发布（.github/workflows/publish.yml）

- 触发：push 到 `release` 分支自动执行，也支持 Actions 页面 `workflow_dispatch` 手动触发
- 工作流自动从 pom.xml 提取版本号并分流：
  - SNAPSHOT：部署 Central Portal 快照仓库 + GitHub Packages，不打 tag、不签名
  - 纯数字 release：先自动创建并推送 `v版本号` git tag（已存在则跳过），再 GPG 签名发布 Maven Central，最后同步 GitHub Packages
- 需要在仓库 Settings → Secrets and variables → Actions 中配置以下 secrets：

| Secret 名                  | 用途                                                                     |
| ------------------------- | ---------------------------------------------------------------------- |
| `CENTRAL_PORTAL_USERNAME` | Central Portal User Token 的 username                                   |
| `CENTRAL_PORTAL_TOKEN`    | Central Portal User Token 的 password（snapshot 与 release 端点共用）          |
| `GPG_PRIVATE_KEY`         | GPG 私钥（ASCII armor，`gpg --armor --export-secret-keys` 导出），仅 release 需要 |
| `GPG_PASSPHRASE`          | GPG 私钥密码，maven-gpg-plugin 通过同名环境变量读取                                   |
| `GITHUB_TOKEN`            | 无需配置，Actions 运行时自动提供，用于 GitHub Packages 认证与推送 tag                      |

## 构建配置

### 核心插件

- Spring Boot Maven Plugin
- Maven Source Plugin
- Maven Javadoc Plugin
- Central Publishing Maven Plugin（常驻 build，按版本自动路由快照/正式发布）
- Maven GPG Plugin（仅 publish profile 激活，release 签名）

### 插件管理

- Spring Boot Maven Plugin
- Maven Assembly Plugin
- Maven Compiler Plugin
- Maven Shade Plugin
- Maven Source Plugin
- Maven Javadoc Plugin
- Maven Release Plugin
- Maven GPG Plugin

## 仓库配置

### 远程仓库（repositories，依赖解析用，可多个共存）

- 阿里云仓库：<https://maven.aliyun.com/repository/public>
- Central Portal 快照仓库：<https://central.sonatype.com/repository/maven-snapshots/>
- GitHub Packages（id=github）：<https://maven.pkg.github.com/kouleen/econ-parent（releases> 与 snapshots 均启用）

### 分发管理（distributionManagement）

- 默认（顶层配置）
  - 快照仓库（id=snapshot）：<https://central.sonatype.com/repository/maven-snapshots/>
  - 发布仓库（id=release）：<https://central.sonatype.com/repository/maven-releases/>
- 激活 `-Pgithub` 后被整体覆盖为 GitHub Packages（repository 与 snapshotRepository 均指向 <https://maven.pkg.github.com/kouleen/econ-parent）>

> POM 规范中 distributionManagement 只允许一个 release 类型的 `<repository>`，因此 GitHub Packages 不能作为第二个 repository 与 Central 并列，只能放进独立 profile 按需覆盖；URL 中的 owner/repo 由属性 `github.owner`、`github.repo` 统一管理。

###

