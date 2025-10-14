---
title: Homepage
description: 
published: true
date: 2025-10-14T05:01:42.902Z
tags: 
editor: markdown
dateCreated: 2025-10-11T10:44:58.400Z
---

# 🐉 Berstra Knowledge Base

欢迎来到 **Berstra 内部知识库**。这里是我们沉淀流程、项目、运维、研发与业务知识的单一入口（Single Source of Truth）。

# Berstra Knowledge Base

> 欢迎来到 Berstra 内部知识库。这里是流程、项目、运维、研发与业务知识的一站式入口。

## 目录（页内）
[[toc]]

## 📚 站点结构（全站）
[[pagetree path="/" depth=2 sort=title]]

## 🧭 业务分区：Business
[[pagetree path="/business" depth=2 sort=title]]

## 🏷️ 按标签浏览（示例）
### SOP
[[pagelist tag="SOP" limit=20 sort=title]]

### DevOps
[[pagelist tag="DevOps" limit=20 sort=title]]

## 🆕 最近更新
[[recent path="/" limit=15]]



> 建议在左侧导航固定本页为 ⭐「主页」，并把常用页面加入收藏。

---

## 🚀 快速入口

- 📊 **系统状态**： [Status](https://status.berstra.com)
- 🧰 **App 低代码平台**： [Appsmith](https://app.berstra.com)
- ☁️ **文件与协作**： [Nextcloud](https://cloud.berstra.com)
- 🤖 **自动化工作流**： [n8n](https://n8n.berstra.com)
- 💬 **内部沟通**： [Rocket.Chat](https://chat.berstra.com)
- 🧾 **ERP**： [ERPNext](https://erp.berstra.com)
- 📚 **知识库 Git 同步**：参见「运营手册 / Wiki 同步」

---

## 🗂 知识结构（建议）

- **SOP / 运营手册**
  - 入职清单，IT 设备发放
  - 邮箱与双因素策略（2FA）
  - 备份与恢复流程
- **运维 / DevOps**
  - Traefik 反向代理与证书
  - Docker Compose 编排与约定
  - 监控报警（Uptime Kuma）
- **自动化 / Integrations**
  - n8n 工作流（Gmail → Rocket.Chat）
  - Webhook 规范与示例
- **应用 / Apps**
  - ERPNext 权限与角色
  - Nextcloud 应用与策略
  - Appsmith 数据源规范
- **硬件 / 维修笔记**
  - PCB 诊断流程、常用工具
  - 常见故障案例
- **项目 / Projects**
  - 立项卡、里程碑、归档模板
- **模板 / Templates**
  - 会议纪要、变更申请、事故复盘
- **术语 / Glossary**
  - 内部缩写与名词解释

> 小技巧：每个目录下都新建 `README` 作为该目录首页，放统一规范和索引。

---

## 🧭 写作与命名规范

- **文件名**：`YYYY-MM-DD-主题-版本.md`（例：`2025-10-11-n8n-gmail-rocket-chat-v1.md`）
- **页面标题**：清晰动宾结构；首屏放结论与操作入口。
- **标签（Tags）**：`#SOP` `#n8n` `#ERPNext` `#Incident` 等，便于检索。
- **变更记录**：每页底部维护 `Changelog`（日期 / 作者 / 变化）。

---

## 🛠 运维约定（重要）

- **Compose 根目录**：`~/stack/`
- **网络约定**：`stack_web`（对外），`stack_internal`（内网）
- **证书**：Traefik 自动签发（Let's Encrypt，邮件 `${LETSENCRYPT_EMAIL}`）
- **备份策略**：
  - 数据库：`pg_dump` 每日冷备 + 周全量，存 Nextcloud `/Backups/PG/`
  - App 数据卷：每晚 tar.gz 到 Nextcloud `/Backups/Volumes/`

> 详细见：`运维 / 备份与恢复`

---

## 🔔 事件与通知

- **监控**：Uptime Kuma 上报异常 → Rocket.Chat `#alerts`
- **自动化**：n8n 轮询邮箱 `[BERSTRA_APPT]` → 解析 → 发送预约提醒
- **变更公告**：所有生产变更需提交《变更单》，并通知 `#changelog`

---

## 📋 常用模板

- **会议纪要**
  - 会议：{项目/系统名} / 时间：{YYYY-MM-DD HH:mm}
  - 参会：{名单}
  - 结论：{三条以内}
  - 待办事项（Owner/DDL）
- **事故复盘（Postmortem）**
  - TL;DR（50 字内）
  - 时间线 / 影响面 / 根因 / 处置 / 预防

> 模板都在「**模板 / Templates**」目录。

---

## 🔎 检索指南

- **精准搜**：使用标签 + 关键词（例：`tag:SOP n8n webhook`）
- **结构化**：目录先导读 README，再深入子页
- **代码块**：所有命令行必须可复制；给出前置条件与回滚步骤

---

## 🧪 示例：Mermaid 流程图

```mermaid
flowchart LR
  Gmail[[Gmail: [BERSTRA_APPT]]] -->|n8n Poll| n8n[n8n Workflow]
  n8n -->|Parse .ics| Calendar[Nextcloud Calendar]
  n8n -->|Webhook| Chat>Rocket.Chat #appointments]
  Chat --> Phone((Mobile Push))