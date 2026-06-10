# GoldenHourRadar

中文名：**霞光雷达**

A lightweight, single-file web app for predicting sunrise and sunset cloud color potential. It combines weather forecast data, layered cloud cover, precipitation risk, visibility, and a simple sun-path cloud sampling model.

这是一个给摄影爱好者用的火烧云 / 朝霞 / 晚霞预测原型。它不追求专业气象级精度，而是提供一个足够直观、好改、适合 Vibe Coding 复刻的开源模板。

![Mobile preview](assets/preview-mobile.png)

## Features

- 7-day sunrise / sunset prediction
- City and travel-destination selector
- Interactive map sampling points
- Fire-cloud score from 0 to 100
- Local weather checks: precipitation, wind, visibility, humidity
- Sun-path cloud quality sampling
- Mobile-first frosted-glass UI
- Chinese map by default, English map via URL parameter
- No backend required

## Quick Start

```bash
python3 -m http.server 4173
```

Open:

```text
http://localhost:4173
```

If your file is not named `index.html`, open:

```text
http://localhost:4173/index.html
```

## Map Language

Chinese map:

```text
http://localhost:4173
```

English / international map:

```text
http://localhost:4173?map=en
```

The map is powered by MapLibre GL. The Chinese base map uses Gaode raster tiles; the English map uses OpenStreetMap raster tiles.

## Data Source

Weather data comes from the free Open-Meteo Forecast API:

```text
https://api.open-meteo.com/v1/forecast
```

Used fields:

- `cloud_cover`
- `cloud_cover_low`
- `cloud_cover_mid`
- `cloud_cover_high`
- `relative_humidity_2m`
- `visibility`
- `precipitation`
- `wind_speed_10m`
- `daily.sunrise`
- `daily.sunset`

## How The Score Works

The score is not just based on the cloud above your head.

For sunset, it samples clouds toward the west. For sunrise, it samples clouds toward the east. Each map point checks:

- local weather near the shooting point
- cloud quality along the sun direction
- whether low clouds or rain block the light path
- whether mid/high clouds can become a colored canvas
- visibility and humidity

See [docs/algorithm.md](docs/algorithm.md).

## Customize

Most useful knobs are near the top of `index.html`:

```js
const CONFIG = {
  radiusKm: 50,
  stepKm: 15,
  mapProvider: new URLSearchParams(location.search).get("map") === "en" ? "en" : "zh",
  defaultCenter: [113.3245, 23.1065],
  defaultZoom: 8.35
};
```

Common changes:

- Change `regions` to your own city list
- Change `radiusKm` for the search radius
- Change `stepKm` for sampling density
- Change `rayDistances` and `rayWeights` for sun-path sampling
- Adjust colors in `colorForScore`
- Translate UI copy

## Use With An Agent

Copy this repo link into Doubao, DeepSeek, Cursor, Codex, or any coding agent, then use the prompt in [PROMPT.md](PROMPT.md).

## Project Names

- English: **GoldenHourRadar**
- Chinese: **霞光雷达**

Alternative Chinese display name if you want stronger Douyin/Xiaohongshu recognition:

- **火烧云雷达**

## License

MIT

