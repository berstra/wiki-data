---
title: Journal 
description: 
published: true
date: 2025-10-19T13:55:33.172Z
tags: 
editor: markdown
dateCreated: 2025-10-15T04:00:23.198Z
---

# Wiki Journal Recording




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


