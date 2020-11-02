---
title: GEE Python API 使用
toc: true
description: ' '
date: 2019-01-01 16:05:19
tags:
categories:
---

# GEE Python API 使用

> [GeoHackWeek2016 - EE Python API](https://docs.google.com/presentation/d/1ZhPI_7uro7hn4sJQQa-yZv021bJWwgsU7j6sYB7RPC0/edit#slide=id.g14e75e974e_0_664)
>
> https://github.com/google/earthengine-api/tree/master/python/examples
>
> https://github.com/tylere/ee-jupyter-examples

GEE 的 Python API 基本与 Javascript API一致

## 导出影像

> https://developers.google.com/earth-engine/exporting
>
> https://stackoverflow.com/questions/39219705/how-to-download-images-using-google-earth-engines-python-api
>
> https://gis.stackexchange.com/questions/287004/download-landsat-data-from-google-earth-engine-for-the-continental-united-states

```python
import ee
ee.Initialize()
# Load a landsat image and select three bands.
landsat = ee.Image('LANDSAT/LC08/C01/T1_TOA/LC08_123032_20140515')
  .select(['B4', 'B3', 'B2'])

# Create a geometry representing an export region.
geometry = ee.Geometry.Rectangle([116.2621, 39.8412, 116.4849, 40.01236])
geometry_region = geometry['coordinates'][0]
task_config = {
    'description': 'imageToDriveExample',
    'scale': 30,  
    'region': geometry_region
    }

# Export the image, specifying scale and region.
task = ee.batch.Export.image(landsat, 'exportExample', task_config)

task.start()
```

将导出`exportExample.tif`到GoogleDrive 

若需导出 [cloud-optimized GeoTIFF](http://www.cogeo.org/)，`task_config`中增加选项：

```
fileFormat: 'GeoTIFF',
formatOptions: {
   cloudOptimized: true
}
```

导出到指定文件夹：`task_config`中增加选项`folder`？

## 获取下载数据链接

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

## 上传下载 

> https://github.com/tracek/gee_asset_manager