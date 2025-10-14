---
title:  payrollmd
description: 
published: true
date: 2025-10-14T04:40:53.866Z
tags: 
editor: markdown
dateCreated: 2025-10-14T04:40:53.866Z
---

# 💼 ERPNext Payroll Workflow (澳洲版工资全流程)
**Berstra Internal SOP**  
Version 2025.10

---

## 🧩 一、系统角色与模块概览 | Overview

| 模块 | 职责 | 对应现实功能 |
|------|------|---------------|
| **Employee** | 员工信息 | TFN、Super、银行信息 |
| **Salary Component** | 工资组成项目 | Basic、Allowance、Deduction、Tax |
| **Salary Structure** | 工资模板 | 定义计算规则与税公式 |
| **Salary Slip** | 工资单 | 每位员工每月发放的工资凭证 |
| **Payroll Entry** | 工资发放批次 | 批量生成与提交工资单 |
| **Journal Entry** | 会计凭证 | 自动记录工资支出与税务负债 |
| **Email / SMTP** | 邮件系统 | 自动发送工资单 PDF |
| **GovReports / STP** | 税务上报 | 向 ATO 提交 STP（工资申报） |
| **Beam / QuickSuper** | 养老金支付 | 向 Super Fund 转账养老金 |

---

## 🧠 二、工资计算与发放流程（ERPNext内）

| 步骤 | 操作 | 系统动作 | 说明 |
|------|--------|------------|------|
| ① 创建员工档案 | HR → Employee → New | 保存银行账号、TFN、Super Fund 名称、Member ID | 一次性操作 |
| ② 定义工资组件 | Payroll → Salary Component | Basic（基本工资）、PAYG（预扣税）、Super（养老金） | PAYG 要写计算公式 |
| ③ 建立工资结构 | Payroll → Salary Structure | 添加收入与扣除项目，选择 “AUS Income Tax - PAYG” 公式 | 可按职位或全员共享 |
| ④ 分配工资结构 | Employee → Assign Salary Structure | 选择对应模板 | 每位员工一份 |
| ⑤ 生成工资单 | Payroll Entry → New → Get Employees → Create Salary Slips | 系统自动计算 PAYG、Net Pay | 生成后可手动修改 |
| ⑥ 审核工资单 | Payroll Entry → Submit Salary Slip | ERPNext 自动生成 Journal Entry | 状态变为 “Submitted” |
| ⑦ 发送工资单 | Payroll Entry → Email Salary Slip | 员工收到 PDF 邮件 | 发件人可设为 payroll@berstra.com |
| ⑧ 导出银行文件 | Payroll Entry → Download Bank Payment File (.ABA) | 生成澳洲标准银行转账文件 | 上传至 CBA / NAB / Westpac |

---

## 💰 三、税务与养老金 | Tax & Superannuation

| 模块 | 内容 | 周期 | 工具 |
|------|-------|--------|--------|
| **PAYG Withholding** | ERPNext 自动计算税额 → 报送 ATO → 在 BAS 中缴纳 | 月度 / 季度 | GovReports / ATO Portal |
| **STP（Single Touch Payroll）** | 上传 CSV / XML 至 GovReports（或 Xero） | 每次发薪后 | GovReports STP-only ($13/月) |
| **Superannuation (养老金)** | ERPNext 计算 → Beam 平台转账 | 每次发薪后 7 天内 | Beam / QuickSuper |
| **Medicare Levy** | 在 PAYG 公式中自动计算 | 同 PAYG | ERPNext 内部公式 |

---

## 🧾 四、银行打款 | Payroll Payment

| 模式 | 说明 | 推荐度 |
|------|------|----------|
| 🅰️ 手动银行转账 | 登录 CBA 手动输入每人金额 | ⭐ |
| 🅱️ ABA 批量上传 | ERPNext 导出 `.ABA` → 银行网页上传 | ⭐⭐⭐⭐⭐ |
| 🅾️ API 自动支付 | n8n + CBA API 或 Paytron 实现 | ⭐⭐⭐（复杂但酷） |

---

## 🧮 五、上报与付款节奏 | Lodgement & Payment Cycle

| 项目 | 频率 | 提交方式 | 支付途径 |
|------|-------|------------|-----------|
| **STP（工资申报）** | 每次发薪 | GovReports / Xero 上传 CSV | - |
| **PAYG（预扣税）** | 每月 / 季度 | ATO Business Portal BAS | BPAY / PayID |
| **Super（养老金）** | 发薪后 7 天内 | Beam / QuickSuper | 自动扣款 |

---

## 🧱 六、会计分录 | Accounting Flow

ERPNext 会自动生成以下分录：

| 科目 | 借方 | 贷方 |
|------|------|------|
| Salary Expense | ✅ | - |
| PAYG Payable | - | ✅ |
| Super Payable | - | ✅ |
| Payroll Payable (净工资) | - | ✅ |
| Bank (发放工资后) | ✅ | - |

---

## ☁️ 七、系统安全与备份 | Stability & Backup

| 层级 | 方案 |
|------|------|
| ERPNext Daily Backup | 自动 SQL + Files |
| Google Drive Sync | 异地备份 |
| GCP Snapshot 每周 | 系统级快照 |
| Healthchecks.io | 备份任务监控 |
| Disaster Recovery | 10 分钟内恢复整站 |

---

## 🚀 八、Berstra 推荐工作流 | Recommended Setup

| 模块 | 工具 / 平台 | 说明 |
|------|----------------|------|
| 工资计算 | ERPNext (GCP 自建) | 自动生成工资单、税额、ABA |
| 工资上报 | GovReports STP Only | 成本低、操作简洁 |
| 工资发放 | ABA 文件 + CBA | 一键上传 |
| 税务缴纳 | ATO Business Portal | PAYG + GST |
| 养老金 | Beam Clearing House | 自动转账 |
| 备份与监控 | GCP Snapshot + Google Drive | 双重保险 |

---

> ✅ **结论：**  
> ERPNext 负责 “计算 + 发放 + 报表”，  
> GovReports 负责 “工资申报 (STP)” ，  
> Beam 负责 “养老金支付”，  
> 这一整套组合实现了中小企业在澳洲的 **合规自动化薪资体系**，  
> 成本仅为 Xero 等 SaaS 的 1/10，但数据完全自控。

---