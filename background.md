# Sextans 项目背景与全局规则

## 1. 项目简介 (Introduction)

**Sextans (六分仪)** 是一个现代化的、以插件化为核心的企业级业务文档管理系统。它旨在成为企业内部的知识枢纽，提供文档的存储、管理、版本控制、共享、协作和智能化应用的全生命周期解决方案。

项目采用"微内核 + 强插件"的设计哲学，将稳定的核心与灵活的功能解耦。这意味着系统的核心非常轻量，而所有具体业务功能，如对接 Gitea 的版本控制、对接 MinIO 的对象存储、对接 OnlyOffice 的在线编辑等，都作为独立的插件存在。这种架构保证了系统的稳定性、可维护性和高度的可扩展性。

## 2. 技术栈 (Technology Stack)

| 类别 | 技术 | 版本/说明 |
|---|---|---|
| **后端** | Java | 21 |
| | Spring Boot | 3.5.3 |
| | Spring Security | 6.x (集成在 Boot 中) |
| | Mybatis | - |
| | Gradle | 8.13 (项目构建) |
| | PF4J | 3.x (插件化框架) |
| | JGit | - (Git 操作库) |
| | Gitea API Client | - (Gitea 远程协作) |
| **前端** | Vue.js | 3.x |
| | TypeScript | 5.x |
| | Vite | 4.x (构建工具) |
| | Pinia | 2.x (状态管理) |
| | Vue Router | 4.x (路由) |
| | Element Plus | - (UI 组件库) |
| **默认数据库**| SQLite | - (通过 JDBC 驱动) |
| **默认存储** | 本地文件系统 | - |

## 3. 核心功能 (Core Features)

- **文件管理**: 提供网盘式的基本操作，包括文件/文件夹的创建、上传、下载、移动、重命名和删除。
- **版本控制**: 提供 Git 原生的版本管理能力。支持将项目内文件夹初始化为 Git 仓库，并对纯文本文档进行版本追踪、提交、查看历史。
- **模板系统**: 内置模板引擎，支持从预设模板快速创建文档、Git 仓库以及格式转换流程，实现标准化作业。
- **插件化生态**: 系统的一切皆可插件化。包括存储后端、用户认证、数据库支持、版本控制、在线预览/编辑、全文检索、格式转换、多协议支持等。
- **权限管理**: 提供基于角色的访问控制 (RBAC)，支持对文件、文件夹、仓库进行精细化的权限设置（所有者、编辑者、查看者）。
- **共享与协作**: 支持通过链接或指定用户的方式共享文件，为未来的实时协同编辑打下基础。

## 4. 目录结构 (Directory Structure)

```text
sextans/
├── api/
├── backend/
│   ├── .gitignore
│   ├── build.gradle
│   ├── compose.yaml
│   ├── gradle/
│   │   └── wrapper/
│   │       ├── gradle-wrapper.jar
│   │       └── gradle-wrapper.properties
│   ├── gradlew
│   ├── gradlew.bat
│   ├── HELP.md
│   ├── settings.gradle
│   └── src/
│       ├── main/
│       │   ├── java/
│       │   │   └── tslc/
│       │   │       └── beihai/
│       │   │           └── sextans/
│       │   │               ├── core/
│       │   │               ├── plugin/
│       │   │               │   └── api/
│       │   │               └── SextansApplication.java
│       │   └── resources/
│       │       ├── static/
│       │       ├── templates/
│       │       └── application.properties
│       └── test/
│           └── java/
│               └── tslc/
│                   └── beihai/
│                       └── sextans/
│                           └── SextansApplicationTests.java
├── deployment/
│   └── README.md
├── docs/
│   ├── development/
│   │   └── planning.md
│   ├── functionality/
│   └── installing/
├── frontend/
│   ├── index.html
│   ├── package.json
│   ├── pnpm-lock.yaml
│   ├── public/
│   │   └── vite.svg
│   ├── src/
│   │   ├── App.vue
│   │   ├── assets/
│   │   ├── components/
│   │   ├── main.ts
│   │   ├── style.css
│   │   └── vite-env.d.ts
│   ├── tsconfig.json
│   └── tsconfig.node.json
└── scripts/
```

## 5. 开发要求 (Development Requirements)

### 5.1. 版本控制

- **分支模型**: 严格遵循 **Git Flow**。`main` 分支用于发布，`develop` 是开发主线。
- **提交信息**: 必须遵循 **Conventional Commits** 规范 (`<type>(<scope>): <subject>`)，以便于生成清晰的变更日志。

### 5.2. 编码规范

- **后端 (Java)**:
  - 遵循 **Google Java Style Guide**。
  - 所有对外暴露的 Service 接口和 Controller 方法必须有完整的 JavaDoc。
  - 优先使用 Stream API 处理集合，代码力求简洁、可读。
  - 核心业务逻辑必须编写单元测试 (JUnit 5)。
- **前端 (Vue/TypeScript)**:
  - 使用 **ESLint** 和 **Prettier** 保证代码风格统一。
  - 遵循 Vue 3 `<script setup>` 的最佳实践，代码逻辑和模板分离。
  - 优先使用 TypeScript，对 API 数据、Store state 等关键部分提供明确的类型定义。

### 5.3. API 设计

- **风格**: 遵循 **RESTful** 风格。
- **规范**: 推荐在 `api/` 目录下使用 **OpenAPI 3.0** 对外提供 API 定义。
- **版本**: API URI 中应包含版本号，如 `/api/v1/...`。
- **响应**: JSON 响应体应统一格式，如 `{"code": 0, "message": "success", "data": {}}`。

### 5.4. 插件开发

- **强依赖接口**: 所有插件必须面向 `plugin-api` 中定义的接口进行开发，严禁插件之间直接调用。
- **自包含**: 插件应尽可能自包含其依赖。
- **文档**: 每个插件都必须包含一个 `README.md`，清晰说明其功能、配置项和使用方法。

### 5.5. "完成"的定义 (Definition of Done)

一个功能/任务被认为是"完成"的，必须满足以下所有条件：

1. 代码已通过 Code Review 并合并到 `develop` 分支。
2. 所有相关的自动化测试均已通过。
3. 相关的文档（API、用户手册、设计文档、插件说明）已同步更新。
4. 功能在测试环境中可被验证。
