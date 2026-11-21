---
sidebar_position: 2
title: CLI Command Reference
description: Complete ReactPress CLI reference — init, doctor, logs, stop, content, and Monorepo pnpm script crosswalk.
keywords: [reactpress, cli, commands, init, doctor, logs, content, agent, pnpm]
---

# CLI Command Reference

ReactPress has two command surfaces: the **global CLI** (end users) and **Monorepo pnpm scripts** (contributors).

## Global CLI (`@reactpressjs/reactpress`)

5.0 streamlines end-user commands; legacy commands such as `dev`, `theme`, and `plugin` are removed — use Admin or Monorepo scripts instead.

### `reactpress init [directory]`

Initialize a site and **auto-start** the full stack.

| Option        | Description                                 |
| ------------- | ------------------------------------------- |
| `-f, --force` | Force re-initialization (clears local data) |

```bash
mkdir my-site && cd my-site
reactpress init
```

### `reactpress doctor [directory]`

Diagnose Node version, port usage, database connection, process status.

| Option        | Description                             |
| ------------- | --------------------------------------- |
| `--show-logs` | Attach recent logs when diagnosis fails |

Subcommand `reactpress doctor check` is the default behavior.

### `reactpress logs [directory]`

View structured API logs.

| Option             | Description                    |
| ------------------ | ------------------------------ |
| `-f, --follow`     | Follow continuously            |
| `--tail <n>`       | Show last n lines (default 50) |
| `--source <name>`  | Filter by source               |
| `--grep <pattern>` | Regex filter                   |
| `--list`           | List available log sources     |

Alias: `reactpress doctor logs`.

### `reactpress stop [directory]`

Stop API and theme processes for the current project.

### `reactpress content`

Headless content tools for agents and scripts: create API keys, create and publish articles from Markdown, and upload media — without opening Admin.

#### `reactpress content key`

Log in as admin and create an API key.

| Option                  | Description                                                                      |
| ----------------------- | -------------------------------------------------------------------------------- |
| `--create`              | Create a new key (required; needs admin login)                                   |
| `--save`                | Write the key to the project `.env` (`REACTPRESS_API_KEY`, `REACTPRESS_API_URL`) |
| `--name <name>`         | Key display name (default `agent`)                                               |
| `--scopes <scopes>`     | Comma-separated scopes (default `read,write`)                                    |
| `--username <username>` | Admin username (default `admin` or `REACTPRESS_ADMIN_USER`)                      |
| `--password <password>` | Admin password (default `admin` or `REACTPRESS_ADMIN_PASSWORD`)                  |
| `--api-url <url>`       | API base URL (default from site config, or `REACTPRESS_API_URL`)                 |

```bash
reactpress content key --create --save
```

#### `reactpress content article create`

Create an article from a Markdown file (frontmatter + body). Defaults to a draft.

| Option            | Description                                      |
| ----------------- | ------------------------------------------------ |
| `--file <path>`   | Path to the Markdown file (required)             |
| `--publish`       | Publish immediately instead of creating a draft  |
| `--api-url <url>` | API base URL                                     |
| `--api-key <key>` | API key (default `REACTPRESS_API_KEY` or `.env`) |

Frontmatter must include `title` and the body must be non-empty; optional fields are `summary`, `slug`, `seoKeywords`, `seoDescription`, `cover`, `tags`, and `category` (missing tags/categories are created automatically).

```bash
reactpress content article create --file ./post.md
reactpress content article create --file ./post.md --publish
```

#### `reactpress content article publish`

Publish an existing article by id.

| Option            | Description           |
| ----------------- | --------------------- |
| `--id <id>`       | Article id (required) |
| `--api-url <url>` | API base URL          |
| `--api-key <key>` | API key               |

```bash
reactpress content article publish --id <id>
```

#### `reactpress content media`

Upload a media file.

| Option            | Description                            |
| ----------------- | -------------------------------------- |
| `--upload`        | Upload to the media library (required) |
| `--file <path>`   | Local file path (required)             |
| `--api-url <url>` | API base URL                           |
| `--api-key <key>` | API key                                |

```bash
reactpress content media --upload --file ./cover.png
```

:::info Credentials and API address
The `article` / `media` subcommands need an API key: `--api-key` wins, then `REACTPRESS_API_KEY` from the environment or the project `.env`, or `REACTPRESS_TOKEN` (Admin JWT). If none is set, the CLI tells you to run `reactpress content key --create --save` first. The API base defaults to the site's `SERVER_SITE_URL` + `/api`, overridable via `--api-url` or `REACTPRESS_API_URL`.
:::

External agents (Cursor, etc.) can install the portable skill first: `npx skills add reactpressjs/reactpress --skill blog-ai`. See the repo [skills/blog-ai/SKILL.md](https://github.com/reactpressjs/reactpress/blob/main/skills/blog-ai/SKILL.md) and the [Headless API guide](./headless-api.md) for an end-to-end example.

### Help and version

```bash
reactpress           # banner + help
reactpress --help
reactpress --version
```

## Monorepo scripts (repository root)

For contributors and deployments needing full lifecycle management:

| Script                    | Description                          |
| ------------------------- | ------------------------------------ |
| `pnpm dev`                | Full-stack dev (API + Admin + Theme) |
| `pnpm dev:api`            | API only                             |
| `pnpm dev:web`            | Admin + API                          |
| `pnpm dev:client`         | Visitor theme only                   |
| `pnpm dev:desktop`        | Electron desktop dev                 |
| `pnpm dev:docs`           | Docusaurus docs site                 |
| `pnpm build`              | Production build                     |
| `pnpm start` / `pnpm pm2` | Production start                     |
| `pnpm build:plugins`      | Compile official plugins             |
| `pnpm build:desktop`      | Package desktop installer            |
| `pnpm status`             | Service status                       |
| `pnpm run init`           | Prepare config only, do not start    |

### Docker-related (optional MySQL)

| Script                 | Description          |
| ---------------------- | -------------------- |
| `pnpm docker:dev`      | Start embedded MySQL |
| `pnpm docker:dev:stop` | Stop Docker database |

## Environment variables

| Variable                      | Description                       | Default                                      |
| ----------------------------- | --------------------------------- | -------------------------------------------- |
| `REACTPRESS_LANG`             | CLI / Admin language `en` \| `zh` | `en`                                         |
| `DB_TYPE`                     | `sqlite` \| mysql config          | `sqlite`                                     |
| `SERVER_PORT`                 | API port                          | `3002`                                       |
| `CLIENT_SITE_URL`             | Public visitor site URL           | `http://localhost:3001`                      |
| `ADMIN_USER` / `ADMIN_PASSWD` | First bootstrap admin             | `admin` / `admin`                            |
| `REACTPRESS_API_URL`          | API base for `content` commands   | Site `SERVER_SITE_URL` + `/api`              |
| `REACTPRESS_API_KEY`          | API key for `content` commands    | None (set by `content key --save` in `.env`) |

Full list: [Configuration](../tutorial-extras/config-intro.md) and [Glossary](../reference/glossary.md).

## Deprecated commands

These fail in the 5.0 global CLI with a hint to use `init`:

`start` · `dev` · `docker` · `nginx` · `server` · `build` · `status` · `publish` · `theme` · `plugin` · `db` · `desktop` · `client`

Monorepo `pnpm dev` etc. still invoke the full implementation via internal CLI entry.

## Related docs

- [Installation & requirements](../getting-started/installation.md)
- [Monorepo local development](./local-development.md)
- [Troubleshooting](../reference/troubleshooting.md)
