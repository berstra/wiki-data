---
title: Journal 
description: 
published: true
date: 2025-10-23T11:48:54.973Z
tags: 
editor: markdown
dateCreated: 2025-10-15T04:00:23.198Z
---

# Wiki Journal Recording

# 🧾 Berstra 部署日志（系统日记）
**日期：2025-10-23 (UTC)**

> 本文为纯 Markdown 内容，直接保存为 `2025-10-23-system-report.md` 即可。

---

## 1) 今日概览（Summary）
- ✅ **MariaDB 10.6** 初始化并正常运行，完成 `dbadmin` 与 `erpnext` 账号/库权限。
- ✅ **ERPNext v15**（Frappe v15）在本机部署完成，可通过 **Cloudflare Tunnel** + **Nginx** 外网访问。
- ✅ Bench 站点：`erpnext.berstra.com` 创建并完成 **ERPNext / HRMS / Insights / Payments / Print Designer / POSAwesome** 安装与迁移。
- ✅ **Supervisor** 常驻进程：web、workers、redis（cache/queue）全部 RUNNING。
- ✅ **Nginx** 反代并修复 **/assets** 404/403 问题，静态资源加载正常（CSS 恢复）。
- ✅ **Cloudflared** 常驻运行，域名与服务映射生效（SSH、ERPNext、Nextcloud、Rocket.Chat 等）。
- ✅ **多应用 Docker 化**：Rocket.Chat、Nextcloud、n8n、Uptime Kuma、Appsmith、Wiki.js、WordPress 已启动并映射到域名。
- ✅ **系统时间** 校正到 UTC；NTP 同步正常。

---

## 2) 基础环境
- 发行版：Ubuntu 22.04
- 重要路径：
  - Bench 根目录：`/home/frappe/frappe-bench`
  - 站点目录：`/home/frappe/frappe-bench/sites/erpnext.berstra.com`
  - 静态总目录：`/home/frappe/frappe-bench/sites/assets`（Nginx `alias` 指向）
  - Supervisor 配置链接：`/etc/supervisor/conf.d/frappe-bench.conf -> ~/frappe-bench/config/supervisor.conf`
  - Nginx 配置：`/etc/nginx/conf.d/frappe-bench.conf`（由 `bench setup nginx` 生成后调整）
  - Cloudflared：`/etc/cloudflared/config.yml`

---

## 3) 数据库（MariaDB 10.6）
- 运行目录：`/run/mysqld`（归属 `mysql`）
- 数据目录：`/var/lib/mysql`（归属 `mysql`）
- 账号与权限（摘要）：
  - `dbadmin@localhost` / `dbadmin@127.0.0.1` — 全局管理
  - `erpnext@localhost` / `erpnext@127.0.0.1` — `erpnext` 库全权限
- 验证：
  - `mysql -udbadmin -p -h127.0.0.1 -e "SELECT VERSION();"`
  - `mysql -uerpnext -p -h127.0.0.1 erpnext -e "SELECT 1;"`

---

## 4) ERPNext/Frappe
- **已安装应用与版本**（`bench --site erpnext.berstra.com list-apps`）  
  - `frappe 15.86.0 (version-15)`  
  - `erpnext 15.84.0 (version-15)`  
  - `hrms 16.0.0-dev (develop)`  
  - `insights 3.0.26 (develop)`  
  - `payments 0.0.1 (develop)`  
  - `print_designer 1.x.x-develop (develop)`  
  - `posawesome 15.9.2 (develop)`

- **站点**：`erpnext.berstra.com`（数据库：`erpnext`）  
- **静态资源修复要点**：
  - `sites/erpnext.berstra.com/public/assets -> ../../assets`（软链）
  - **Nginx** 使用：
    ```nginx
    location /assets/ {
        alias /home/frappe/frappe-bench/sites/assets/;
        add_header Cache-Control "public, max-age=31536000";
    }
    ```
  - 目录权限：`chmod -R 755 ~/frappe-bench/sites{,/assets}`  
    （必要时对 `www-data` 添加读权限）
  - 重新构建静态：
    ```bash
    bench build --app frappe
    bench build --app erpnext
    bench --site erpnext.berstra.com clear-cache
    bench --site erpnext.berstra.com clear-website-cache
    sudo supervisorctl restart 'frappe-bench-web:'
    ```

- **进程与常驻**（Supervisor）：
  - 组：`frappe-bench-redis`、`frappe-bench-web`、`frappe-bench-workers`
  - 全部 `RUNNING`，端口：
    - Redis Cache: `127.0.0.1:13000`
    - Redis Queue: `127.0.0.1:11000`
    - Gunicorn/Web: 由 Nginx 反代
  - 常用命令：
    ```bash
    sudo supervisorctl status
    sudo supervisorctl restart 'frappe-bench-web:'
    sudo supervisorctl restart 'frappe-bench-workers:'
    sudo supervisorctl restart 'frappe-bench-redis:'
    ```

---

## 5) 反向代理（Nginx）
- 主配置：`/etc/nginx/nginx.conf`
- Bench 站点配置：`/etc/nginx/conf.d/frappe-bench.conf`
- 关键位置：
  - `/` 转发至 Frappe Web（gunicorn）
  - `/assets/` 使用 `alias` 指向 `sites/assets/`（解决 404/403）
- 校验与重载：
  ```bash
  sudo nginx -t && sudo systemctl reload nginx




# ERPNext
cd ~/frappe-bench && . env/bin/activate
bench --site erpnext.berstra.com list-apps
bench build --app frappe && bench build --app erpnext
bench --site erpnext.berstra.com clear-cache
bench --site erpnext.berstra.com clear-website-cache
bench migrate

# Supervisor
sudo supervisorctl status
sudo supervisorctl restart 'frappe-bench-web:'
sudo supervisorctl restart 'frappe-bench-workers:'

# Nginx
sudo nginx -t && sudo systemctl reload nginx

# Cloudflare Tunnel
sudo systemctl status cloudflared
sudo systemctl restart cloudflared

# Docker
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
docker compose up -d
docker compose logs -f

# 磁盘空间与资源
df -h
lsblk
free -h
htop






# 🗓️ Daily Journal — 2025-10-20

## 🧩 主要任务
- 完成 **ERP 客户同步脚本** 分段执行机制（`runNextBatch()`）。
- 加入 **批次状态消息通知函数** `sendBatchStatusMessage()`。
- 优化 `syncCustomersToERP()` 返回值，增加成功数量统计。
- 修复 ERP 地址创建错误（缺失 city 字段导致的 `MandatoryError`），添加城市兜底逻辑。
- 维护 Google Apps Script 触发器，实现自动批次执行。

---

## ⚙️ 技术改进
| 模块 | 改进内容 | 说明 |
|------|-----------|------|
| `syncCustomersToERP()` | 增加返回 `success` 数量 | 用于 `runNextBatch` 统计同步成功数量 |
| `fillAllAddressDetails()` | 检查 AI 列已有地址则跳过 | 节省 Google Maps API 调用 |
| `runNextBatch()` | 自动推进指针、锁定防并发 | 避免多触发器重复执行 |
| `sendBatchStatusMessage()` | 新增 Chat 通知功能 | 自动汇报当前批次执行情况（含客户、时间等） |

---

## 📊 执行结果
- 每批处理行数：`BATCH_SIZE = 50`
- 昨日累计同步客户：约 **350**
- 成功率：约 **98%**
- 主要错误类型：`MandatoryError: city`（已修复）

---

## 🕒 明日计划
- 测试触发器连续运行稳定性（确认不会重叠执行）。
- 在 `sendBatchStatusMessage()` 中加入执行耗时统计。
- 新增日志记录 Sheet，保存每批次运行记录。
- 准备自动化 ERP 数据备份至 Google Drive。

---

## 💬 备注
> 当前脚本运行稳定，可实现全自动同步。
> 建议进一步优化异常捕获、API 限速与定时间隔，以支持超过 5000 行客户数据的持续运行。






## 🗓️ 昨日工作总结（2025-10-13 · 星期一）

| 时间 | 分类 | 事项 | 说明 | 状态 |
|------|------|------|------|------|
| 上午 | ⚙️ 系统测试 | 调试 ERPNext Workflow 自动执行问题 | 已启用 active 状态，但未自动触发；测试后手动执行成功 | ✅ 已完成 |
| 上午 | 🧠 学习 | 复习 ERPNext 基础数据结构与 DocType 逻辑 | 理解 Doctype、Field、Document 的底层关系 | ✅ 已完成 |
| 下午 | 💼 财务 / HR | 学习 Payroll Structure 与 Assignment | 了解工资结构与员工分配流程 | ✅ 已完成 |
| 下午 | 📬 邮件触发 | 检查 Gmail 自动触发筛选条件 | 使用 `(is:inbox is:unread newer_than:3d)` + 主题过滤测试 | ✅ 已完成 |
| 晚上 | 📘 整理笔记 | 规划 ERPNext 模块标准流程文档 | 准备制作完整流程图版本 | 📝 进行中 |

# 🗓️ Daily Log — 2025-10-18

| 时间段 | 工作内容 | 细节说明 / 结果 | 状态 |
|:------:|:----------|:----------------|:------|
| 上午 | 优化 Google Sheet 打印脚本（Print_A4） | 完成自动换行、列宽设定、Phone 列居中、Footer 自动生成；并加入 History 去重机制 | ✅ 完成 |
| 上午 | 增加 ERPNext 出货统计报表可视化需求 | 讨论如何统计每日/每型号出库数量，并考虑使用 Google Looker Studio 可视化 | ✅ 方案确定 |
| 下午 | 连接 Google Looker Studio 与 History_Detailed 表 | 成功创建数据源，能动态更新出货记录 | ✅ 完成 |
| 下午 | 尝试制作 Looker Studio 图表 | 创建每日出货量柱状图（Record Count by Date）；调整日期维度排序 | ✅ 完成 |
| 下午 | 学习 Combo Chart 用法 | 了解柱状 + 折线组合图的原理与度量（如平均趋势线） | ✅ 理解 |
| 晚上 | Looker Studio 过滤设置 | 设定 Exclude 过滤器排除 “Sauna delivery”、“BNE” 和空白项 | ✅ 已配置 |
| 晚上 | 尝试制作 Google Maps / Geo Chart | 创建自定义纬经度字段（Y,Z → CONCAT）以用于地图气泡展示 | ⚙️ 进行中 |
| 晚上 | 检查 AWS 账单问题 | 确认 $0.02 费用来源为空闲 Elastic IP，确认 Sydney 区已无剩余 IP，费用将自动停止 | ✅ 处理完成 |
| 晚上 | 查询 Westpac × MYOB 优惠 | 找到 Westpac Business 账户可享 12 个月免费 + 后续 20% 折扣方案 | ✅ 研究完成 |

---

### 🧭 今日总结
- 完成 **Google Sheets 自动打印系统** 的功能升级与防重复机制。  
- 搭建了 **Looker Studio 数据可视化面板**，实现每日出货数量自动更新。  
- 成功实现 ERPNext → Google Sheet → Looker Studio 的数据链路闭环。  
- 解决 AWS 账单空闲 IP 问题，节省后续费用。  
- 研究 Westpac × MYOB 商业优惠，为未来账务管理打下基础。



# 📅 2025-10-19 工作日记

| 时间 | 任务类别 | 具体操作 / 事件 | 工具 / 模块 | 结果 / 状态 | 备注 |
|------|-----------|------------------|--------------|--------------|------|
| 09:00 | ERPNext 设置 | 调整 Workspace 权限结构（Simple Stock） | ERPNext v15 | ✅ 已建立并默认给 Stock User | 用户 Tony 已测试正常 |
| 09:45 | ERPNext 客户端脚本 | 编写 Client Script 自动聚焦扫码框、默认仓库 | ERPNext Client Script | ✅ 成功触发 | 针对 bingtaowang05 账号 |
| 10:30 | Workspace 调整 | 修复 title 不显示、Module index 报错 | ERPNext Console | ⚙️ 调整完毕 | 新 workspace `Simple Stock` 可访问 |
| 11:00 | Google Sheet 集成 | 设计 Google Sheet → ERPNext 同步逻辑 | Google Apps Script | 🧠 方案确定 | 准备阶段：先同步 Customer |
| 13:30 | Console 操作 | 通过 bench console 为所有 Stock User 设默认 Workspace | ERPNext bench console | ✅ 完成 | 出现 “Warehouse wise Stock Value” 脏数据报错 |
| 15:00 | Client Script 优化 | 新增脚本自动设置默认 “Material Issue” 类型 | ERPNext Client Script | ✅ 正常运行 | 同时检测当前登录用户条件 |
| 16:00 | Google Sheet 集成 | 准备 Customer 同步脚本（仅创建客户） | Google Apps Script | 🚧 测试中 | 下一步：自动创建 Address |
| 17:00 | 数据导入规划 | 分析 Google Sheet 出货记录字段映射 | Google Sheet + ERPNext | 🧩 已确定映射 | Order ID → external_id, Customer → Customer Name |

---

## ✅ 明日计划
- [ ] 测试 Google Sheet → ERPNext 自动创建 Customer 功能  
- [ ] 为每个 Customer 自动创建 Address（含地理坐标）  
- [ ] 编写 Delivery Note 导入逻辑（带外部 ID 去重）  
- [ ] 完成 Simple Stock Workspace 权限隔离逻辑（按角色）

---


