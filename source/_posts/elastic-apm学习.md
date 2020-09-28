---
title: elastic-apm学习
date: 2020-09-28 15:14:43
categories:
- 编程学习
tags:
- elastic-apm
- 监控
---
分别安装elasticsearch，kibana，elastic-apm对应安装包
在windows 下将安装包解压，分别启动es和kibana对应的bat文件

在管理员powerShell下执行
```
PowerShell.exe -ExecutionPolicy UnRestricted -File .\install-service-apm-server.ps1

Start-Service apm-server
```
在程序中添加包
```
pip install elastic-apm[flask]
```
在启动代码中加入
```
# initialize using environment variables
from elasticapm.contrib.flask import ElasticAPM
app = Flask(__name__)
apm = ElasticAPM(app)

# or configure to use ELASTIC_APM in your application's settings
from elasticapm.contrib.flask import ElasticAPM
app.config['ELASTIC_APM'] = {
  # Set required service name. Allowed characters:
  # a-z, A-Z, 0-9, -, _, and space
  'SERVICE_NAME': '',

  # Use if APM Server requires a token
  'SECRET_TOKEN': '',

  # Set custom APM Server URL (default: http://localhost:8200)
  'SERVER_URL': '',
}

apm = ElasticAPM(app)
```

写的简单的holle world flask监控不到服务，使用别人的能检测到
之后在实际服务上，远程试试能否检测到