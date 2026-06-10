# 算法说明 Algorithm Notes

霞光雷达使用的是一个面向摄影师的实用评分模型。它不是专业气象预报，而是帮助用户快速判断“要不要出门拍”的轻量工具。

GoldenHourRadar uses a simple practical scoring model for photographers. It is not a meteorological-grade forecast.

## 核心思路 Core Idea

好看的朝霞 / 晚霞通常需要两个条件：

Beautiful sunrise/sunset clouds usually need two things:

1. 拍摄点附近天气不能太差。  
   Weather near the shooting point should be acceptable.
2. 太阳方向的光路不能被低云或降水挡住，同时需要中高云作为“染色画布”。  
   Clouds along the sun direction should allow low-angle light to pass and illuminate mid/high clouds.

所以算法分成两部分：

So the app separates:

- **本地点天气 Local weather**：降水、低云、能见度、湿度、风速  
- **太阳方向云层 Sun-path cloud quality**：观察点东侧或西侧一大片区域的云层质量

## 太阳方向 Sun Direction

- 晚霞模式：向西侧采样  
  Sunset mode: sample toward the west
- 朝霞模式：向东侧采样  
  Sunrise mode: sample toward the east

应用会根据纬度和一年中的日期估算太阳方位角，然后沿着这个方向采样云层。

The app estimates sun azimuth from latitude and day of year, then samples along that ray.

## 光路采样 Ray Sampling

每个地图点会沿太阳方向采样 3 个位置：

Each map point samples three positions:

```js
const rayDistances = [35, 90, 160];
const rayWeights = [0.45, 0.35, 0.20];
```

为什么是三段：

Why three points:

- 35km：近处低云最容易挡住光  
  35 km: nearby blocking clouds matter most
- 90km：中距离云墙会显著影响稳定性  
  90 km: mid-distance cloud wall or clear light path
- 160km：远处云层和地平线条件影响霞光表现  
  160 km: far cloud and horizon conditions

## 云层权重 Cloud Layer Weights

低云最容易挡光。高云更可能成为被阳光染色的画布，所以遮挡权重较低。

Low clouds block light most strongly. High clouds are less likely to block the sun and can become the colored canvas.

```js
blockingCloud = low * 0.78 + mid * 0.45 + high * 0.18
```

降水和低能见度会降低评分。

Rain and poor visibility reduce score.

## 硬性压分 Hard Rules

以下情况会限制最高分：

The score is capped when:

- 几乎完全没云  
  There is almost no cloud
- 没有中高云画布  
  There is no mid/high cloud canvas
- 低云太厚  
  Low cloud is too heavy
- 拍摄窗口附近有雨  
  Rain appears near the shooting window
- 太阳方向光路被低云 / 中云挡住  
  The sun path is blocked by low/mid clouds

## 调参建议 Suggested Tuning

想让评分更严格：

For stricter scores:

- 增加低云惩罚  
  Increase low-cloud penalty
- 降低降水阈值  
  Lower rain threshold
- 增加光路权重  
  Increase ray weight

想让评分更乐观：

For more optimistic scores:

- 降低低云惩罚  
  Reduce low-cloud penalty
- 增加高云贡献  
  Increase high-cloud contribution
- 降低 `noCanvas` 惩罚  
  Lower `noCanvas` penalty

主要调参位置：

Most tuning happens in:

- `evaluatePoint`
- `evaluateRayPath`
- `makeReasons`

