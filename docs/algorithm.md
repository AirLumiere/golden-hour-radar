# Algorithm Notes

GoldenHourRadar uses a simple practical scoring model for photographers. It is not a meteorological-grade forecast.

## Core Idea

Beautiful sunrise/sunset clouds need two things:

1. Weather near the shooting point should be acceptable.
2. Clouds along the sun direction should allow low-angle light to pass and illuminate mid/high clouds.

So the app separates:

- **Local weather**: rain, low cloud, visibility, humidity, wind
- **Sun-path cloud quality**: cloud layers east or west of the observer

## Sun Direction

- Sunset mode: sample toward the west
- Sunrise mode: sample toward the east

The app estimates sun azimuth from latitude and day of year, then samples along that ray.

## Ray Sampling

Each map point samples three positions:

```js
const rayDistances = [35, 90, 160];
const rayWeights = [0.45, 0.35, 0.20];
```

Why three points:

- 35 km: nearby blocking clouds matter most
- 90 km: mid-distance cloud wall or clear light path
- 160 km: far cloud and horizon conditions

## Cloud Layer Weights

Low clouds block light most strongly. High clouds are less likely to block the sun and can become the colored canvas.

```js
blockingCloud = low * 0.78 + mid * 0.45 + high * 0.18
```

Rain and poor visibility reduce score.

## Hard Rules

The score is capped when:

- There is almost no cloud
- There is no mid/high cloud canvas
- Low cloud is too heavy
- Rain appears near the shooting window
- The sun path is blocked by low/mid clouds

## Suggested Tuning

For stricter scores:

- Increase low-cloud penalty
- Lower rain threshold
- Increase ray weight

For more optimistic scores:

- Reduce low-cloud penalty
- Increase high-cloud contribution
- Lower `noCanvas` penalty

Most tuning happens in:

- `evaluatePoint`
- `evaluateRayPath`
- `makeReasons`

