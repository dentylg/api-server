# wuhan2020-api-server
武汉新型冠状病毒防疫信息收集平台后端([English](https://github.com/Elfsong/api-server#english))

## 快速上手
``` bash
git clone https://github.com/wuhan2020/api-server
cd api-server
git clone https://github.com/wuhan2020/wuhan2020
pip install -r requirements.txt
bash bootstrap
```

随后便可在 `http://{your-ip}:9000/wuhan2020/{list_path}` 调试 api

注意 `list_path` 为 `utils.py` 中被 `@data.route()` 中注册的 `path`, `your-ip` 默认是 `127.0.0.1`.

## 在 Docker 容器运行
需要安装[Docker客户端](https://www.docker.com/products/docker-desktop).

### Build Docker Image
在本项目根目录下执行 `docker build -t api-server:default .`.

* 注意：国内这一步可能会耗时较长.

### Run Docker Image
执行 `docker run --name api-server --publish 5000:5000 api-server:default ` 后可在本地浏览器中打开 http://localhost:5000/wuhan2020/{endpoint}. (使用 `-d` 进入detach模式)

_如果出现 `The container name "/api-server" is already in use` 报错可先执行 `docker rm api-server` 删除残留的同名容器._

### Stop Docker Container
执行 `docker stop api-server ` 停止运行中的容器.

## 项目文件说明

```
.
├── bootstrap (阿里云serverless启动脚本)
├── config (flask config dir)
├── swagger (swagger 暂时还不能用, 待适配.)
├── test (test 数据目录, 参照wuhan2020的[readme](https://github.com/wuhan2020/wuhan2020), 移除个人的联系方式和银行卡信息.)
├── wuhan2020 (submoudle, 用于获取data-sync同步过来的数据.)
├── index.py (flask应用默认配置脚本)
└── utils.py (flask蓝图, 目前有csv和json的接口, csv的加上了bearer token 认证, json的接口暂时不能用.)
```

## 配置说明

```
# Flask config
Config

# cache dir
CacheCfg
 - csv;
 - json;
```

## 测试

`dev url`是 `http://127.0.0.1:9000/wuhan2020/xxx_list`

```sh
# dev
curl --location --request GET '127.0.0.1:9000/wuhan2020/logistical_list' \
--header 'Authorization: Bearer test-safe-wuhan'
# product
curl --location --request GET '127.0.0.1:9000/wuhan2020/logistical_list' \
--header 'Authorization: Bearer product-token'
```

## 遗留问题

- [ ] 添加swagger适配;
- [ ] csv转换出来都是拍平的字段, 里面有部分中文转英文需要帮助转成合适的英文, 实在找不到用拼音替代.


## 前端项目issues
https://github.com/wuhan2020/WebApp/issues

<h2 id="english"> wuhan2020-api-server </h2>
The Information Collection Backend for Wuhan 2019-nCoV epidemic prevention

## Quick Start
``` bash
git clone https://github.com/wuhan2020/api-server
cd api-server
git clone https://github.com/wuhan2020/wuhan2020
pip install -r requirements.txt
bash bootstrap
```

And then debug APIs at `http://your-ip:9000/wuhan2020/`.
Note that `list_path` is the registered path in `utils.py` by `data.route`,
`your-ip` is 127.0.0.1 by default.

## Run Docker Container
Install [Docker Client](https://www.docker.com/products/docker-desktop).

### Build Docker Image
Run `docker build -t api-server:default .` at the root dir.
Note that this step may spend a lot time in the Mainland China area.

### Run Docker Image
Run `docker run --name api-server --publish 5000:5000 api-server:default ` , and then open `http://localhost:5000/wuhan2020/{endpoint}` in the local browser. (Add `-d` into the detach mode)

_If you met this error `The container name "/api-server" is already in use`, run `docker rm api-server` to delete previous containers which have the same name._

### Stop Docker Container
Run `docker stop api-server `to stop running containers.

## Project Description

```
.
├── bootstrap (Serverless Launch Script)
├── index.py  (Flask Application Default Configuration Script)
└── utils.py  (Flask Blueprint Script)
```
**index.py**
```python
# -*- coding: utf-8 -*-
from flask import  Flask,session,request,Blueprint
import os
import sys
import json
import platform
import datetime
from utils import data

app = Flask(__name__)
app.debug = True
# Debug mode is enabled by default
path_prefix= "/wuhan2020"
# Url request prefix, with /wuhan2020 by default
if platform.system()=="Linux":
    path_home="/home/wuhan2020/wuhan2020"
else:
    path_home=os.path.join(app.root_path,"wuhan2020")
# Serverless use remote NAS to save cache files
# Cache files will be saved at the root dir when you debug locally
if not os.path.exists(path_home):
    os.mkdir(path_home)
app.register_blueprint(data, url_prefix=path_prefix)
# Use Flask blueprint to register http-router

if __name__ == '__main__':
    # Use default port 9000
    port = os.environ.get("FC_SERVER_PORT", "9000")
    app.run(host='127.0.0.1', port=int(port))
```
## Front-end issues
https://github.com/wuhan2020/front-pages/issues
