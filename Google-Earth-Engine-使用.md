---
title: Google Earth Engine 使用
toc: true
description: 'GEE学习记录 '
date: 2018-11-09 20:31:57
tags:
categories:
---

# Google Earth Engine 

>A planetary-scale platform for Earth science data & analysis

## 基本信息

- 地址：https://earthengine.google.com/

- JS代码编辑器： [code.earthengine.google.com](https://code.earthengine.google.com/)

- API文档：https://developers.google.com/earth-engine/tutorials
- 数据目录：https://developers.google.com/earth-engine/datasets/

## 数据结构

 Earth Engine 中最基本的地理数据结构是 `Image` 和`Feature` ，分别对应与 raster 和 vector 数据类型. Images 包含一系列波段和属性字典（ bands and a dictionary of properties）. Features 由 `Geometry` 和 一个属性字典组成（a dictionary of properties）. 一组图像用 `ImageCollection`表示和处理.，相应的要素集为 `FeatureCollection`. 其他基本数据结构包括 `Dictionary`, `List`, `Array`, `Geometry`, `Date`, `Number` 和`String`. 谨记这些都是服务端对象（ *server-side* objects）. 也就是说，除非你明确地去请求他们的信息，否则你的客户端浏览器对这些对象是一无所知的。 请求会触发一个由google发送给 Code Editor的信息. 若信息较大，则速度肯定会慢一些。

## 算法

 Code Editor 左侧的 **Docs** 标签列出了每个API的方法

使用例子：

```javascript
// This function gets NDVI from Landsat 5 imagery.
var getNDVI = function(image) {
  return image.normalizedDifference(['B4', 'B3']);
};

// Load two Landsat 5 images, 20 years apart.
var image1 = ee.Image('LANDSAT/LT05/C01/T1_TOA/LT05_044034_19900604');
var image2 = ee.Image('LANDSAT/LT05/C01/T1_TOA/LT05_044034_20100611');

// Compute NDVI from the scenes.
var ndvi1 = getNDVI(image1);
var ndvi2 = getNDVI(image2);

// Compute the difference in NDVI.
var ndviDifference = ndvi2.subtract(ndvi1);
```

## Colaboratory

在云端运行的 Jupyter 笔记本环境，不需要进行任何设置就可以使用

可配置Google Earth Engine（GEE）的python线上开发环境。

> https://zhuanlan.zhihu.com/p/41591886

安装第三方Python库：`！pip install [package]`,  如 `earthengine-api`