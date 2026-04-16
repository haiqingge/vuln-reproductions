# FOFA 查询缓存

记录从漏洞笔记中提取的 FOFA 查询语法，供快速复用。

## 漏洞列表

### JeecgBoot SQL注入
```
body="jeecg-boot"
```

### 杭州新威SMG网关 命令执行
```
body="text ml10 mr20" && (title="网关管理软件" || title="Gateway Management")
```

### EasyCVR downloadxlsx SQL注入
```
(body="EasyGBS" || body="EasyDarwin.Body" || body="EasyCVR") && (title="" || title="EasyCVR")
```

### SparkShop 任意文件上传
```
"SparkShop"
```

### 宇视视频管理系统 VM.php RCE
```
body="/Script/Public/Language.js.php"
```

### 安科瑞默认密码
```
icon_hash="-1291691164"
```

### 东胜物流SQL注入
```
body="FeeCodes/CompanysAdapter.aspx"||body="dhtmlxcombo_whp"
```

### LeanMES SQL注入
```
icon_hash="1505132128"
```

### 元迈CRM任意文件读取
```
body="/common/scripts/basic.js" && body="www.metacrm.com.cn"
```

### 青龙面板RCE
```
icon_hash=="-254502902"
```
