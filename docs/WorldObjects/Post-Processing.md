# 后处理

**阅读本文大概需要 20 分钟**

本文概述了后处理的定义，后处理都有哪些功能，每种功能表现效果以及使用方式。

## 后处理功能介绍

功能定义：后处理是在正常渲染管线结束后，对最终渲染图像进行的后期加工，比如滤镜、模糊等效果。以此来模拟物理摄像机和电影特效。也就是说后处理功能是我们做完游戏后的最后一道美化工具，将游戏的画面效果进行提升的必要手段，最终呈现给用户。

目前后处理功能包括：滤镜、模糊、景深。

后续还会拓展：运动模糊、素描效果、油画效果、环境光遮蔽、闪光弹效果等。

## 滤镜效果

功能说明：滤镜是一种图像处理方式，可以在不改变原始图像的基础上，通过调整滤镜参数，对图像进行色彩修正，使得游戏的图像效果更加真实或艳丽，从而符合游戏风格。

### 滤镜预设

功能说明：为了方便用户使用，我们添加了30种滤镜预设，用户可以一键设置与游戏相符的滤镜效果。

| 中文名称  | 枚举名称 | 枚举序号  | 应用说明 |
| ----- | ----- | ----- | ----- |
| 默认 | Default | 0 | ![](https://cdn.233xyx.com/online/AGubunyn79gX1709711997226.PNG)  |
| 梦境 | Dreamy | 1 | ![](https://cdn.233xyx.com/online/2lCXUiSWJETd1709711997226.PNG)  |
| 反差色 | Contrast | 2 | ![](https://cdn.233xyx.com/online/sWKm8g2vWYFh1709711997226.PNG)  |
| 暖阳 | WarmSunshine | 3 | ![](https://cdn.233xyx.com/online/dZa0oxnwxrPD1709711997226.PNG)  |
| 老照片 | OldPhoto | 4 | ![](https://cdn.233xyx.com/online/Ain66TxuOXgL1709711997226.PNG)  |
| 夜幕 | Night | 5 | ![](https://cdn.233xyx.com/online/ZtP9kkX5W1yl1709711997226.PNG)  |
| 鲜暖色 | WarmContrast | 6 | ![](https://cdn.233xyx.com/online/ow8RpBj1eWx71709711997226.PNG)  |
| 奶油 | Cream | 7 | ![](https://cdn.233xyx.com/online/DxUVL2OXtoWw1709711997226.PNG)  |
| 鲜亮 | Bright | 8 | ![](https://cdn.233xyx.com/online/QvCgGtfUO2Db1709711997226.PNG)  |
| 夏日 | SummerDay | 9 | ![](https://cdn.233xyx.com/online/tSp2QudWUpNJ1709711997226.PNG)  |
| 高级 | Senior | 10 | ![](https://cdn.233xyx.com/online/Y2SStK4mUVwn1709711997226.PNG)  |
| 自然 | Natural | 11 | ![](https://cdn.233xyx.com/online/KLyMZPuTrW5Y1709711997226.PNG)  |
| 苏打水 | SodaWater | 12 | ![](https://cdn.233xyx.com/online/w2acbDwJoqJE1709711997226.PNG)  |
| 日落1 | Sunset_1 | 13 | ![](https://cdn.233xyx.com/online/JjjY3OWYnrEf1709711997226.PNG)  |
| 日落2 | Sunset_2 | 14 | ![](https://cdn.233xyx.com/online/n2UwckxweZ4t1709711997226.PNG)  |
| 日落3 | Sunset_3 | 15 | ![](https://cdn.233xyx.com/online/uC8OhBbO1yS01709711997226.PNG)  |
| 灰1 | Grey_1 | 16 | ![](https://cdn.233xyx.com/online/QT5f3WTL334h1709711997226.PNG)  |
| 梦幻 | Dream | 17 | ![](https://cdn.233xyx.com/online/kxrQWj1ZW9VX1709711997226.PNG)  |
| 电影 | Film | 18 | ![](https://cdn.233xyx.com/online/QEg7BS0gTHeg1709711997225.PNG)  |
| 灰2 | Grey_2 | 19 | ![](https://cdn.233xyx.com/online/AYZC1HVcMhMb1709711997226.PNG)  |
| 多彩1 | Colorful_1 | 20 | ![](https://cdn.233xyx.com/online/3rQd08Pm4kl41709711997225.PNG)  |
| 黎明 | Dawn | 21 | ![](https://cdn.233xyx.com/online/25vamnBOllgg1709711997226.PNG)  |
| 多彩2 | Colorful_2 | 22 | ![](https://cdn.233xyx.com/online/WrBMVipZh61I1709711997225.PNG)  |
| 黄昏 | Dusk | 23 | ![](https://cdn.233xyx.com/online/TsPbvpjJUEnM1709711997226.PNG)  |
| 破晓 | BreakingDawn | 24 | ![](https://cdn.233xyx.com/online/Pj0T1H3Ef9vY1709711997226.PNG)  |
| 森林 | Forest | 25 | ![](https://cdn.233xyx.com/online/791uSxqxMpBx1709711997226.PNG)  |
| 青1 | Cyan_1 | 26 | ![](https://cdn.233xyx.com/online/hYfkQ3HeiPUq1709711997226.PNG)  |
| 青2 | Cyan_2 | 27 | ![](https://cdn.233xyx.com/online/rmLkcvKxcJ8c1709711997226.PNG)  |
| 老照片1 | OldPhoto_1 | 28 | ![](https://cdn.233xyx.com/online/f1K9gChD64UA1709711997226.PNG)  |
| 老照片2 | OldPhoto_2 | 29 | ![](https://cdn.233xyx.com/online/7VgpDR1dg2SF1709711997226.PNG)  |
| 泛黄 | Yellowing | 30 | ![](https://cdn.233xyx.com/online/Tc9RPZ6HFK0y1709711997226.PNG)  |

相关接口：

```TypeScript
//后处理预设选择“夜幕”
PostProcess.preset = PostProcessPreset.Night;
```


### 泛光

功能说明：开启泛光渲染效果，可以对场景中会发光的对象进行泛光效果调节，注意：泛光效果只在高清画质中生效。

| 属性名称  |  属性说明 | 取值范围 |
| ----- | ----- | ----- |
| 泛光强度 | 控制泛光区域的亮度 | 0.1 - 10 |
| 泛光扩散度 | 控制泛光区域向四周的扩散宽度 | 0.1 - 1.9 |
| 泛光曝光系数 | 在泛光效果下，整个画面的曝光度 | 0.1 - 1.9 |
| 泛光范围 | 数值越小泛光区域覆盖范围越大，数值从小往大调整，泛光会往光线直射的地方集中 | 0.0 - 10 |
  
演示效果：

![](https://cdn.233xyx.com/online/P34UZyxOOmLr1709711997225.png) 

相关接口：

```TypeScript
//后处理泛光值 = 1.5
PostProcess.bloom = 1.5;
```

### 全局饱和度

功能说明：可以调节镜头颜色的饱和度，饱和度越高颜色越生动。取值范围0 ~ 2，默认值为 1.2。
  
演示效果：

![](https://cdn.233xyx.com/online/hCyuP4EBrYJI1709711997225.png) 

相关接口：

```TypeScript
//后处理全局饱和度 = 1.1
PostProcess.saturation = 1.1;
```

### 全局对比度

功能说明：可以调节镜头颜色的对比度，对比度越高颜色越鲜明。取值范围0 ~ 5，默认值为 1。
  
演示效果：

![](https://cdn.233xyx.com/online/VMTg6fobBndD1709711997225.png) 

相关接口：

```TypeScript
//后处理全局对比度 = 1
PostProcess.contrast = 1;
```

## 模糊效果

![](https://cdn.233xyx.com/online/GgDZWQir695e1709711997226.png) 

功能说明：是将整个场景进行虚化，营造氛围的同时，强调游戏的部分内容，从而传递一些重点的游戏UI信息。

### 是否启用

功能说明：是否启用模糊的效果，勾选后，场景效果将会被模糊化，取消勾选则会取消模糊效果。

相关接口：

```TypeScript
//模糊功能启用
PostProcess.blurEnabled = true;
//模糊功能关闭
PostProcess.blurEnabled = false;
```

### 模糊强度

功能说明：调整场景模糊效果的程度，值越大，场景效果越模糊。
  
演示效果：

![](https://cdn.233xyx.com/online/bNYeRdeNPdq11709711997226.png) 

相关接口：

```TypeScript
//模糊强度 = 0.6
PostProcess.blurIntensity = 0.6;
```

## 景深效果

![](https://cdn.233xyx.com/online/NSOWydnlX45q1709711997225.png) 

功能说明：是将除了部分场景的其他场景效果进行虚化，突出部分场景的效果，强调游戏场景的部分信息。

### 是否启用

功能说明：是否启用景深的效果，勾选后，场景效果将会被模糊化，取消勾选则会取消景深效果。

相关接口：

```TypeScript
//景深功能启用
PostProcess.depthOfFieldEnabled = true;
//景深功能关闭
PostProcess.depthOfFieldEnabled = false;
```

### 景深强度

功能说明：调整场景虚化效果的程度，值越大，场景效果越模糊。
  
演示效果：

![](https://cdn.233xyx.com/online/TXKlq6c1XWpM1709711997225.png) 

相关接口：

```TypeScript
//景深强度 = 0.6
PostProcess.depthOfFieldIntensity = 0.6;
```

### 焦距位置

功能说明：控制对焦对象与摄像机的距离，值越小对焦对象与摄像机的距离就越远，值越大对焦对象与摄像机的距离就越近。
  
演示效果：

![](https://cdn.233xyx.com/online/ygSVEKhs79tA1709711997225.png) 

相关接口：

```TypeScript
//景深聚焦距离 = 0.6
PostProcess.focusPosition = 0.6;
```

### 焦距距离

功能说明：控制对焦对象的距离，也就是没有被虚化的上下范围。
  
演示效果：

![](https://cdn.233xyx.com/online/Ggw02pGVYEXq1709711997225.png) 

相关接口：

```TypeScript
//景深聚焦范围 = 0.2
PostProcess.focusDistance = 0.2;
```
