<p align="center">
  <img src="https://img.shields.io/badge/Minecraft-1.20.1+-green?style=for-the-badge&logo=minecraft" alt="Minecraft 1.20.1+"/>
  <img src="https://img.shields.io/badge/API-Spigot%20%7C%20Folia-orange?style=for-the-badge" alt="Spigot | Folia"/>
  <img src="https://img.shields.io/badge/Version-1.6.6--snapshot.5-blue?style=for-the-badge" alt="版本 1.6.6"/>
  <img src="https://img.shields.io/badge/License-GPL%20v3-red?style=for-the-badge" alt="GPL v3"/>
  <img src="https://img.shields.io/badge/Java-17%2B-brightgreen?style=for-the-badge" alt="Java 17+"/>
</p>

# GuildPlugin

一个功能完备的 Minecraft 公会/阵营系统，包含经济、关系、等级、全图形界面以及模块化 SDK 支持。同时支持 Spigot 和 Folia —— 完全免费且开源。

> 文档：[用户指南](./Wiki/README_CN.md) | [快速开始](./Wiki/README_EN.md) | [SDK 开发者指南](./Wiki/SDK%20Developer-Guide.md) | [公会世界](./Wiki/GuildWorld.md) ([EN](./Wiki/GuildWorld_EN.md)) | [公会战](./Wiki/GuildWar.md) ([EN](./Wiki/GuildWar_EN.md))

## 特性

- **公会管理** —— 创建、解散、成员管理、基于角色的权限系统
- **经济系统** —— 资金管理、存入/取出、Vault 集成
- **关系系统** —— 公会间的结盟、敌对、战争、停战
- **等级系统** —— 公会成长，随等级提升最大成员上限
- **全图形界面** —— 所有操作均提供直观的图形界面
- **多语言** —— 内置 **26** 种语言 (中文, English, Polski, Português (Brasil), Deutsch, Français, Español, 日本語, 한국어 等)
- **世界与公会战** —— 虚空竞技场、`.gws` 预设、固定地图团队 PVP (`/guildworld`, `/guildwar`)；跨服战目前仅为 **P3 代理框架** (尚未投入生产环境使用 —— 详见 [跨服战](./Wiki/CrossServer-War.md))
- **异步数据库** —— HikariCP 连接池，支持 MySQL/SQLite
- **模块化 SDK** —— 外部模块开发，提供全功能 API 覆盖（包含 6 个示例模块）
- **自定义 GUI 系统** —— 模块可注册/打开/注销自定义 GUI（热卸载清理需提供 **`moduleId`**）
- **事件总线 (EventBus)** —— 松耦合的模块间通信，支持按模块进行订阅追踪
- **服务容器 (ServiceContainer)** —— 模块通过依赖注入 (DI) 访问核心系统服务
- **模块热加载** —— 通过 `/guildmodule` 在运行时添加/移除模块，无需重启服务器
- **模块生命周期安全** —— 卸载时自动清理监听器、任务、命令、GUI 和占位符；Folia 兼容性检查；ClassLoader 泄漏检测
- **模块 GUI 增强** —— 图片模式、基岩版表单 以及通过 `gui-config.yml` 实现的服务器管理员配置覆盖

## 兼容性

| 软件 | 版本 |
|:--------:|:-------:|
| [Spigot](https://www.spigotmc.org) | 1.20.1+ |
| [PaperMC](https://papermc.io/downloads/paper) | 1.20.1+ |
| [Purpur](https://purpurmc.org) | 1.20.1+ |
| [Folia](https://papermc.io/software/folia) | 核心可在 Folia 上运行；**`/guildworld` NMS 桥接**仅支持白名单版本（见下文） |

Folia 多世界 (`gworld`) 白名单定义在 `ServerUtils.FOLIA_SUPPORTED_VERSIONS` 中（例如 1.19.4, 1.20.4, 1.20.6, 1.21.x, 26.1.x）。**特意未包含 1.20.1** —— 插件会正常加载，但在不受支持的 Folia 版本上，世界创建/加载功能将保持禁用状态。

## 集成

| 插件 | 类型 |
|:------:|:----:|
| [Vault](https://www.spigotmc.org/resources/vault.34315/) | 经济 |
| [PlaceholderAPI](https://www.spigotmc.org/resources/placeholderapi.6245/) | 占位符 |

## 安装

1. 从 [Releases](https://github.com/chenasyd/-GuildPlugin/releases) 下载最新版本
2. 将 `guild-plugin-{version}.jar` 放入服务器的 `plugins/` 文件夹中
3. 重启服务器
4. 根据需要配置 `plugins/GuildPlugin/config.yml`
5. 运行 `/guildadmin reload` 以应用配置更改（运行时配置 + 语言文件；**不会**重连数据库/Bungee）

> ⚠️ **升级插件时**：请务必先备份您的配置和数据。建议删除 `messages_*.yml` 文件并让插件重新生成它们 —— 这能确保包含所有新消息并避免显示错误。

## 从源码构建

**环境要求：** Java 17+, Maven 3.8+

```bash
git clone https://github.com/chenasyd/-GuildPlugin.git
cd -GuildPlugin
mvn clean package -pl guild-plugin
```

输出的 JAR 文件将位于 `guild-plugin/target/guild-plugin-*.jar`。

如需包含示例模块：

```bash
mvn clean package -pl guild-plugin -Pbuild-announcement-module
```

## 指令

> 别名：`/g` → `/guild`，`/ga` → `/guildadmin`。

### 玩家指令 (`/guild`)

| 指令 | 权限 | 描述 |
|:-------:|:----------:|:-----------:|
| `/guild` | `guild.use` | 打开主界面 |
| `/guild create <name>` | `guild.create` | 创建公会 |
| `/guild info` | `guild.use` | 查看公会信息 |
| `/guild members` | `guild.use` | 列出成员 |
| `/guild invite <player>` | `guild.invite` | 邀请玩家 |
| `/guild kick <player>` | `guild.kick` | 踢出成员 |
| `/guild promote <player>` | `guild.promote` | 晋升为官员 |
| `/guild demote <player>` | `guild.demote` | 降级官员 |
| `/guild accept <guild>` | `guild.use` | 接受邀请 |
| `/guild decline <guild>` | `guild.use` | 拒绝邀请 |
| `/guild leave` | `guild.use` | 离开公会 |
| `/guild delete` | `guild.delete` | 删除公会（打开确认界面） |
| `/guild delete confirm` | `guild.delete` | 确认删除公会 |
| `/guild delete cancel` | `guild.delete` | 取消删除公会 |
| `/guild sethome` | `guild.sethome` | 设置公会领地 |
| `/guild home` | `guild.home` | 传送到公会领地 |
| `/guild deposit <amount>` | `guild.deposit` | 存入资金 |
| `/guild withdraw <amount>` | `guild.withdraw` | 取出资金 |
| `/guild transfer <player> <amount>` | `guild.transfer` | 转账给玩家 |
| `/guild logs` | `guild.use` | 查看公会操作日志 |
| `/guild placeholder <player\|guild\|rank>` | `guild.use` | 获取占位符 |
| `/guild time` | `guild.use` | 查看公会成立时长 |
| `/guild help` | `guild.use` | 显示帮助 |

#### `/guild relation` —— 关系

| 指令 | 权限 | 描述 |
|:-------:|:----------:|:-----------:|
| `/guild relation list` | `guild.relation` | 列出所有关系 |
| `/guild relation create <guild> [type]` | `guild.relation` | 创建关系（默认：结盟） |
| `/guild relation accept <guild>` | `guild.relation` | 接受请求 |
| `/guild relation reject <guild>` | `guild.relation` | 拒绝请求 |
| `/guild relation delete <guild>` | `guild.relation` | 删除关系 |

关系类型：`neutral` (中立)、`ally` (结盟)、`enemy` (敌对)、`war` (战争)、`truce` (停战)

#### `/guild economy` —— 经济

| 指令 | 权限 | 描述 |
|:-------:|:----------:|:-----------:|
| `/guild economy info` | `guild.economy` | 查看经济信息 |
| `/guild economy deposit <amount>` | `guild.economy` | 存入资金 |
| `/guild economy withdraw <amount>` | `guild.economy` | 取出资金 |
| `/guild economy transfer <guild> <amount>` | `guild.economy` | 转账给其他公会 |

### 管理员指令 (`/guildadmin`)

| 指令 | 权限 | 描述 |
|:-------:|:----------:|:-----------:|
| `/guildadmin` | `guild.admin` | 管理面板 |
| `/guildadmin reload` | `guild.admin` | 重载运行时配置 + 语言文件（不包含数据库/Bungee） |
| `/guildadmin list` | `guild.admin` | 列出所有公会 |
| `/guildadmin info <guild>` | `guild.admin` | 公会详情 |
| `/guildadmin delete <guild>` | `guild.admin` | 强制删除公会 |
| `/guildadmin freeze <guild>` | `guild.admin` | 冻结公会 |
| `/guildadmin unfreeze <guild>` | `guild.admin` | 解冻公会 |
| `/guildadmin transfer <guild> <player>` | `guild.admin` | 转让会长 |
| `/guildadmin economy <guild> <set\|add\|remove> <amount>` | `guild.admin` | 管理公会经济 |
| `/guildadmin update` | `guild.admin` | 检查更新 |
| `/guildadmin update download` | `guild.admin.update` | 下载并安装更新 |
| `/guildadmin test <gui\|economy\|relation>` | `guild.admin` | 运行管理员测试 |
| `/guildadmin help` | `guild.admin` | 显示帮助 |

#### `/guildadmin relation`

| 指令 | 权限 | 描述 |
|:-------:|:----------:|:-----------:|
| `/guildadmin relation gui` | `guild.admin` | 打开关系管理界面 |
| `/guildadmin relation list` | `guild.admin` | 列出所有关系 |
| `/guildadmin relation create <g1> <g2> <type>` | `guild.admin` | 创建关系 |
| `/guildadmin relation delete <g1> <g2>` | `guild.admin` | 删除关系 |

### 模块管理 (`/guildmodule`)

| 指令 | 权限 | 描述 |
|:-------:|:----------:|:-----------:|
| `/guildmodule list` | `guild.admin.module` | 列出已加载的模块 |
| `/guildmodule load <file.jar>` | `guild.admin.module` | 加载模块 |
| `/guildmodule unload <moduleId>` | `guild.admin.module` | 卸载模块 |
| `/guildmodule reload <moduleId>` | `guild.admin.module` | 重载模块 |
| `/guildmodule info <moduleId>` | `guild.admin.module` | 模块详情 |
| `/guildmodule cloud` | `guild.admin.module` | 列出云端模块 |
| `/guildmodule cloud download <moduleId>` | `guild.admin.module` | 从云端下载 |

### 所有权限节点

| 权限 | 默认 | 描述 |
|:----------:|:-------:|:-----------:|
| `guild.use` | true | 使用公会系统 |
| `guild.create` | true | 创建公会 |
| `guild.invite` | true | 邀请玩家 |
| `guild.kick` | true | 踢出成员 |
| `guild.promote` | true | 晋升成员 |
| `guild.demote` | true | 降级成员 |
| `guild.delete` | op | 删除公会 |
| `guild.sethome` | true | 设置公会领地 |
| `guild.home` | true | 传送到公会领地 |
| `guild.relation` | true | 管理关系 |
| `guild.economy` | true | 管理经济 |
| `guild.deposit` | true | 存入资金 |
| `guild.withdraw` | true | 取出资金 |
| `guild.transfer` | true | 转账资金 |
| `guild.admin` | op | 管理员权限 |
| `guild.admin.module` | op | 模块管理 |
| `guild.admin.update` | op | 下载并安装更新 |

## 版本命名规则

| 类型 | 格式 | 示例 |
|:----:|:------:|:-------:|
| 正式版 | `x.x.x` | `1.6.5` |
| 正式预发布版 | `x.x.x-snapshot.N` | `1.6.6-snapshot.2` |
| 第三方分支版 | `x.x.x-forkname.N` | `1.6.4-elaria.1` |

`v` 前缀是可选的，会自动识别（`v1.6.5` = `1.6.5`）。第三方分支版必须使用自定义后缀（`x.x.x-yourname.N`）以与正式版区分。更新检查器会同时查询 GitHub 和 Modrinth，如果本地版本非官方维护，将会发出警告。

## 链接

- **GitHub**: [chenasyd/-GuildPlugin](https://github.com/chenasyd/-GuildPlugin)
- **Issues**: [提交 Bug](https://github.com/chenasyd/-GuildPlugin/issues)
- **Wiki**: [文档](https://github.com/chenasyd/-GuildPlugin/wiki)

## bStats

[![bStats](https://bstats.org/signatures/bukkit/Guild%20Plugin.svg)](https://bstats.org/plugin/bukkit/Guild%20Plugin/31803)

## 开源协议

本项目基于 [GNU GPL v3.0](LICENSE) 协议开源。
