---
sidebar_position: 2
title: CLI 命令参考
description: ReactPress CLI 完整命令参考 — init、doctor、logs、stop、content 及 Monorepo pnpm 脚本对照表。
keywords: [reactpress, cli, commands, init, doctor, logs, content, agent, pnpm]
---

# CLI 命令参考

ReactPress 有两套命令界面：**全局 CLI**（终端用户）与 **Monorepo pnpm 脚本**（贡献者）。

## 全局 CLI（@reactpressjs/reactpress）

5.0 面向终端用户精简为以下命令；`dev`、`theme`、`plugin` 等旧命令已移除，请使用 Admin 或 Monorepo 脚本。

### `reactpress init [directory]`

初始化站点并**自动启动**全栈服务。

| 选项          | 说明                           |
| ------------- | ------------------------------ |
| `-f, --force` | 强制重新初始化（清除本地数据） |

```bash
mkdir my-site && cd my-site
reactpress init
```

### `reactpress doctor [directory]`

诊断 Node 版本、端口占用、数据库连接、进程状态。

| 选项          | 说明                   |
| ------------- | ---------------------- |
| `--show-logs` | 诊断失败时附加最近日志 |

子命令 `reactpress doctor check` 为默认行为。

### `reactpress logs [directory]`

查看 API 结构化日志。

| 选项               | 说明                     |
| ------------------ | ------------------------ |
| `-f, --follow`     | 持续跟踪                 |
| `--tail <n>`       | 显示最近 n 行（默认 50） |
| `--source <name>`  | 按来源过滤               |
| `--grep <pattern>` | 正则过滤                 |
| `--list`           | 列出可用日志源           |

别名：`reactpress doctor logs`。

### `reactpress stop [directory]`

停止当前项目的 API 与主题进程。

### `reactpress content`

面向 Agent / 脚本的 Headless 内容工具：创建 API Key、从 Markdown 创建与发布文章、上传媒体，无需打开 Admin。

#### `reactpress content key`

以管理员身份登录并创建 API Key。

| 选项                    | 说明                                                                 |
| ----------------------- | -------------------------------------------------------------------- |
| `--create`              | 创建新 Key（必填，需要管理员登录）                                   |
| `--save`                | 将 Key 写入项目 `.env`（`REACTPRESS_API_KEY`、`REACTPRESS_API_URL`） |
| `--name <name>`         | Key 名称（默认 `agent`）                                             |
| `--scopes <scopes>`     | 逗号分隔权限（默认 `read,write`）                                    |
| `--username <username>` | 管理员账号（默认 `admin` 或 `REACTPRESS_ADMIN_USER`）                |
| `--password <password>` | 管理员密码（默认 `admin` 或 `REACTPRESS_ADMIN_PASSWORD`）            |
| `--api-url <url>`       | API 基地址（默认取站点配置，或 `REACTPRESS_API_URL`）                |

```bash
reactpress content key --create --save
```

#### `reactpress content article create`

从 Markdown 文件（frontmatter + 正文）创建文章，默认为草稿。

| 选项              | 说明                                             |
| ----------------- | ------------------------------------------------ |
| `--file <path>`   | Markdown 文件路径（必填）                        |
| `--publish`       | 创建后立即发布（否则为草稿）                     |
| `--api-url <url>` | API 基地址                                       |
| `--api-key <key>` | API Key（默认读 `REACTPRESS_API_KEY` 或 `.env`） |

frontmatter 必须包含 `title`，正文不能为空；可选 `summary`、`slug`、`seoKeywords`、`seoDescription`、`cover`、`tags`、`category`（标签 / 分类不存在时自动创建）。

```bash
reactpress content article create --file ./post.md
reactpress content article create --file ./post.md --publish
```

#### `reactpress content article publish`

按 id 发布已有文章。

| 选项              | 说明            |
| ----------------- | --------------- |
| `--id <id>`       | 文章 id（必填） |
| `--api-url <url>` | API 基地址      |
| `--api-key <key>` | API Key         |

```bash
reactpress content article publish --id <id>
```

#### `reactpress content media`

上传媒体文件。

| 选项              | 说明                 |
| ----------------- | -------------------- |
| `--upload`        | 上传到媒体库（必填） |
| `--file <path>`   | 本地文件路径（必填） |
| `--api-url <url>` | API 基地址           |
| `--api-key <key>` | API Key              |

```bash
reactpress content media --upload --file ./cover.png
```

:::info 凭据与 API 地址
`article` / `media` 子命令需要 API Key：优先 `--api-key`，其次环境变量或项目 `.env` 中的 `REACTPRESS_API_KEY`，也可用 `REACTPRESS_TOKEN`（Admin JWT）。均未设置时会提示先运行 `reactpress content key --create --save`。API 基地址默认为站点 `SERVER_SITE_URL` + `/api`，可用 `--api-url` 或 `REACTPRESS_API_URL` 覆盖。
:::

外部 Agent（Cursor 等）可先安装便携 Skill：`npx skills add reactpressjs/reactpress --skill blog-ai`，端到端示例见仓库 [skills/blog-ai/SKILL.md](https://github.com/reactpressjs/reactpress/blob/main/skills/blog-ai/SKILL.md) 与 [Headless API 指南](./headless-api.md)。

### 帮助与版本

```bash
reactpress           # 显示 banner + 帮助
reactpress --help
reactpress --version
```

## Monorepo 脚本（仓库根目录）

贡献者与需要完整生命周期管理的部署场景：

| 脚本                      | 说明                            |
| ------------------------- | ------------------------------- |
| `pnpm dev`                | 全栈开发（API + Admin + Theme） |
| `pnpm dev:api`            | 仅 API                          |
| `pnpm dev:web`            | 仅 Admin + API                  |
| `pnpm dev:client`         | 仅访客主题                      |
| `pnpm dev:desktop`        | Electron 桌面开发               |
| `pnpm dev:docs`           | 文档站 Docusaurus               |
| `pnpm build`              | 生产构建                        |
| `pnpm start` / `pnpm pm2` | 生产启动                        |
| `pnpm build:plugins`      | 编译官方插件                    |
| `pnpm build:desktop`      | 打包桌面安装程序                |
| `pnpm status`             | 服务状态                        |
| `pnpm run init`           | 仅准备配置，不启动              |

### Docker 相关（可选 MySQL）

| 脚本                   | 说明               |
| ---------------------- | ------------------ |
| `pnpm docker:dev`      | 启动嵌入式 MySQL   |
| `pnpm docker:dev:stop` | 停止 Docker 数据库 |

## 环境变量

| 变量                          | 说明                          | 默认                                   |
| ----------------------------- | ----------------------------- | -------------------------------------- |
| `REACTPRESS_LANG`             | CLI / Admin 语言 `en` \| `zh` | `en`                                   |
| `DB_TYPE`                     | `sqlite` \| mysql 配置        | `sqlite`                               |
| `SERVER_PORT`                 | API 端口                      | `3002`                                 |
| `CLIENT_SITE_URL`             | 访客站公网 URL                | `http://localhost:3001`                |
| `ADMIN_USER` / `ADMIN_PASSWD` | 首次 bootstrap 管理员         | `admin` / `admin`                      |
| `REACTPRESS_API_URL`          | `content` 命令的 API 基地址   | 站点 `SERVER_SITE_URL` + `/api`        |
| `REACTPRESS_API_KEY`          | `content` 命令的 API Key      | 无（`content key --save` 写入 `.env`） |

完整列表见 [项目配置项](../tutorial-extras/config-intro.md) 与 [术语表](../reference/glossary.md)。

## 已废弃命令

以下命令在 5.0 全局 CLI 中会报错并提示使用 `init`：

`start` · `dev` · `docker` · `nginx` · `server` · `build` · `status` · `publish` · `theme` · `plugin` · `db` · `desktop` · `client`

Monorepo 内 `pnpm dev` 等仍通过内部 CLI 入口调用完整实现。

## 相关文档

- [安装与环境要求](../getting-started/installation.md)
- [Monorepo 本地开发](./local-development.md)
- [故障排查](../reference/troubleshooting.md)
