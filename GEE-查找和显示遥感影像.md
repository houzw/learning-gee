---
title: GEE 查找和显示遥感影像
toc: true
description: ' '
date: 2018-11-25 22:04:01
tags: 'GEE'
categories:
---

# GEE 查找和显示遥感影像

进入GEE代码编辑器   https://code.earthengine.google.com/ ，界面如下（图片来自google）：

![Earth Engine Code Editor](https://developers.google.com/earth-engine/images/Playground_diagram_v3_crop.png)

## 搜索并导入数据

- 在搜索框搜索感兴趣地点，如北京，使用 geometry tools 在地图上绘制一个点，并重命名，例如*beijing*。

- 搜索一个数据集，如 **Sentinel–2**，选择 *Sentinel–2: Multi-spectral Instrument (MSI), Level–1C* 并 **import**，重命名为 *sentinel2* （点击 code editor 中 *var* 后面的变量名进行修改）。

## 筛选数据集

此时我们可访问 Sentinel–2 数据集的所有数据，但我们只需要感兴趣地点的部分，因此需要进一步筛选数据集

```javascript
var filtered = sentinel2
   .filterDate('2017-05-01','2018-01-01')  // by date
   .filterBounds(beijing) // by location
  // sentinel2 Image Properties 无云数据
  .sort("CLOUD_COVERAGE_ASSESSMENT").first();
var image = ee.Image(filtered);
print("A Sentinel-2 scene:", image)
// run
```

> 可用数据：Jun 23, 2015 - Nov 25, 2018

编辑器右侧打印的信息

```
Image COPERNICUS/S2/20170525T025551_20170525T030448_T50TMK (16 bands)
type: Image
id: COPERNICUS/S2/20170525T025551_20170525T030448_T50TMK
version: 1495829848819000
bands: List (16 elements)
properties: Object (71 properties)
```

## 添加影像到Map

- 首先定义影像的显示颜色，使用**真彩色**，即给 **4/3/2** 波段（红绿蓝波段）赋予 **红、绿、蓝** 色，使其接近自然色

  ```javascript
  // https://developers.google.com/earth-engine/image_visualization
  // min、max 应该是RGB三种颜色组合的范围，颜色值从 0 到 255 能够组合出超过一千六百万种颜色
  var trueColor = {bands:['B4','B3','B2'],min:0,max:3000};
  Map.addLayer(image, trueColor, "true-colour image");
  ```

  执行之后会在地图上显示包含我们所选点的一幅影像.

  也可以显示假彩色影像，即波段与其颜色不一致

  ```javascript
  var falseColor = {bands:['B8','B4','B3'],min:0,max:3000}
  Map.addLayer(image, falseColor, "false-colour image");
  ```

## 计算常用指数

## NDVI 归一化植被指数

**NDVI = (近红外波段 - 红波段) / (近红外波段 + 红波段)**  

NDVI = (NIR - RED)/(NIR + RED)

```text
Landsat8: NDVI = (band5 - band4) / (band5 + band4)
Sentinel2: NDVI = (band8 - band4) / (band8 + band4)
Modis: NDVI = (band2 - band1) / (band2 + band1)
ETM/TM: NDVI = (band4 - band3) / (band4 + band3)
AVHRR: NDVI = (CH2 - CH1) / (CH2 + CH1)
```

GEE中有多种计算方式，可参考 https://zhuanlan.zhihu.com/p/29620366，

```js
// 方法1，简单
var NDVI_1 = image.normalizedDifference(["B5","B4"]);
// 方法2，灵活
var NDVI_2 = image.expression("(NIR - RED) / (NIR + RED)",        
        { RED: image.select("B4"),    //  RED          
          NIR: image.select("B8"),    // NIR          
          BLUE: image.select("B2")    // BLUE        
         });
Map.addLayer(NDVI, {min: 0, max: 1}, "NDVI");
```

### NDWI 归一化水体指数

**NDWI = (绿波段 - 近红外波段) / (绿波段 + 近红外波段)**

```js

landsat8: NDWI = (band3 - band5) / (band3 + band5)
landsat5/7: NDWI = (band2 - band4) / (band2 + band4)
sentinel2: NDWI = (band3 - band8) / (band3 + band8)
```

---

> https://www.geospatialecology.com/lab1