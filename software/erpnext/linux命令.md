---
title: linux命令
description: 
published: true
date: 2025-11-08T05:56:57.678Z
tags: 
editor: markdown
dateCreated: 2025-11-08T04:54:40.626Z
---

# 进环境
sudo su frappe
source env/bin/activate


# Google 备份
---

## 1. ✅ 手动执行完整备份（含文件 & 加密）
```bash
bench --site erpnext.berstra.com backup --with-files
执行后备份会保存到：/home/frappe/frappe-bench/sites/erpnext.berstra.com/private/backups/
```

## 2. 🚀 立即强制上传备份到 Google Drive（跳过等待队列）
```
bench --site erpnext.berstra.com execute frappe.integrations.doctype.google_drive.google_drive.upload_system_backup_to_google_drive
成功会显示：
"Google Drive Backup Successful."
```

## 3. 🕒 走队列上传（由 Worker 后台执行）
```
bench --site erpnext.berstra.com execute frappe.integrations.doctype.google_drive.google_drive.take_backup
worker状态
bench doctor
woker挂了重启
sudo supervisorctl restart all

```

```
bench --site erpnext.berstra.com backup
查看最近备份
bench --site erpnext.berstra.com execute frappe.integrations.doctype.s3_backup_settings.s3_backup_settings.take_backups_s3
S3手动备份

ls -lh sites/erpnext.berstra.com/private/backups/
检测每天是否重新备份看到备份文件
```



























































