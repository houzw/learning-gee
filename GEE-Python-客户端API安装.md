---
title: GEE Python 客户端API安装
toc: true
description: ' 简单介绍 GEE Python 客户端API安装'
date: 2018-11-25 20:29:57
tags: 'GEE'
categories:
---

## GEE Python 客户端API安装
- 安装 谷歌的 python api 客户端
cmd 中执行`pip install google-api-python-client`
- 鉴权验证依赖库
查看鉴权库是否存在
cmd 中执行`python -c "from oauth2client import crypt"`
没有则安装
cmd 中执行`pip install pyCrypto`
若不能安装，可以下载 https://files.pythonhosted.org/packages/95/a9/4f25a14d23f0786b64875b91784607c2277eff25d48f915e39ff0cff505a/oauth2client-4.1.3-py2.py3-none-any.whl
然后 `pip install oauth2client-4.1.3-py2.py3-none-any.whl` 安装
- 安装`earthengine-api`
cmd 中执行`pip install earthengine-api`
- 测试 gee
cmd 中执行`python -c "import ee; ee.Initialize()"`
应该会出现一条错误信息，因为需要验证身份
- 鉴权
cmd 中执行 `earthengine authenticate`，会打开默认浏览器，根据提示操作即可
- 验证
```python
python
>>> import ee
>>> ee.Initialize()
>>> image = ee.Image('srtm90_v4')
>>> print(image.getInfo())
```
应该会输出以下信息
```
{'type': 'Image', 'bands': [{'id': 'elevation', 'data_type': {'type': 'PixelType', 'precision': 'int', 'min': -32768, 'max': 32767}, 'dimensions': [432000, 144000], 'crs': 'EPSG:4326', 'crs_transform': [0.000833333333333, 0.0, -180.0, 0.0, -0.000833333333333, 60.0]}], 'version': 1494271934303000, 'id': 'srtm90_v4', 'properties': {'system:time_start': 950227200000, 'system:time_end': 951177600000, 'system:asset_size': 18827626666}}
```
- 例子: 获取下载数据链接
```python
import ee
ee.Initialize()
# Get a download URL for an image.
image1 = ee.Image('srtm90_v4')
path = image1.getDownloadUrl({
    'scale': 30,
    'crs': 'EPSG:4326',
    'region': '[[-120, 35], [-119, 35], [-119, 34], [-120, 34]]'
})
print( path)
```

### 网络代理问题

由于国内无法访问Google，GEE 的 python api 会无法使用


```
Exception has occurred: TimeoutError

[WinError 10060] A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond
```
配置代理（失败？）

```python
# use proxy to visit google
# 根据你的VPN进行设置
os.environ['HTTP_PROXY'] = 'http://127.0.0.1:9666'
os.environ['HTTPS_PROXY'] = 'http://127.0.0.1:9666'

# https://github.com/google/earthengine-api/tree/master/python
ee.Initialize()
```

### QGIS 插件

https://github.com/gee-community/qgis-earthengine-plugin

注意，同样需要设置代理：（失败）

`Setting | Options | Network | Use proxy for web access | HTTPProxy`

A collection of 260+ Python examples for using Google Earth Engine in QGIS

https://github.com/giswqs/qgis-earthengine-examples

测试

```python
>>> import ee
>>> print(ee.String('Hello World from EE!').getInfo())
```



---

#### 参考

https://developers.google.com/earth-engine/python_install_manual
**[intro-google-earth-engine-python-api](https://github.com/earthlab/tutorials/blob/master/documentation/intro-google-earth-engine-python-api.md)**

https://github.com/google/earthengine-api/tree/master/python

https://zhuanlan.zhihu.com/p/40319581

[亦可使用 Anaconda 安装](https://anaconda.org/conda-forge/earthengine-api)

---

## [geemap](https://geemap.readthedocs.io/en/latest/readme.html)

**A Python package for interactive mapping with Google Earth Engine, ipyleaflet, and ipywidgets.**

```shell
# 以下操作附带安装了 gee 的 python api
conda create -n gee python
conda activate gee
conda install mamba -c conda-forge
mamba install geemap -c conda-forge
```

安装 [Jupyter notebook extensions](https://github.com/ipython-contrib/jupyter_contrib_nbextensions) （可选）

```shell
mamba install jupyter_contrib_nbextensions -c conda-forge
```

然后进入虚拟环境，启动 ` jupyter notebook`。

新建Python页面，进行测试

```python
import geemap
# 必须这样设置代理，否则无法连接gee
geemap.set_proxy(port=8080, ip='http://127.0.0.1')
# Authenticates and initializes Earth Engine
import ee
try:
    ee.Initialize()
except Exception as e:
    ee.Authenticate()
    ee.Initialize()
```

显示地图

```python
Map = geemap.Map(center=[36,114], zoom=4) # 纬度在前
Map
```



---

例子

 [A collection of 300+ Jupyter Python notebook examples for using Google Earth Engine with interactive mapping](https://github.com/giswqs/earthengine-py-notebooks).

[Interactive mapping using GEE Python API and geemap](https://geemap.readthedocs.io/en/latest/readme.html#interactive-mapping-using-gee-python-api-and-geemap)

```python

```

