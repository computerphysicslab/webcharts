# 📈 Crypto Charts – Multi-Provider (Binance / Bybit / CoinGecko / CoinPaprika)

A high-performance, browser-based cryptocurrency charting tool that aggregates OHLCV market data from multiple providers and renders interactive candlestick charts with advanced indicators using **Apache ECharts**.

---

## 🚀 Overview

This project is a **lightweight, dependency-minimal trading visualization tool** designed for:

* Multi-exchange data comparison
* Infinite historical data exploration
* Custom indicator visualization
* Fast, responsive chart rendering

It runs entirely in the browser using vanilla JavaScript and external APIs.

---

## ✨ Features

### 📊 Charting

* Candlestick (OHLC) chart

* Logarithmic price scale

* EMA 200 overlay

* Custom volume-based indicator:

  ```
  SuperVolume
  ```

* Dual-panel layout:

  * Top: Price chart
  * Bottom: Indicator scatter plot

---

### 🔄 Multi-Provider Support

Supports 4 data providers:

| Provider    | Type         | Infinite Scroll | Notes                 |
| ----------- | ------------ | --------------- | --------------------- |
| Binance     | Exchange API | ✅               | Fast, granular data   |
| Bybit       | Exchange API | ✅               | Alternative liquidity |
| CoinGecko   | Aggregator   | ❌               | No pagination         |
| CoinPaprika | Aggregator   | ✅               | Flexible intervals    |

---

### 🪙 Supported Assets

* BTC, ETH, BNB, SOL, XRP
* DOGE, DOT, KSM, SUI
* ATOM, TRUMP

Each asset is mapped per provider internally.

---

### ⏱ Timeframes

Dynamic per provider:

* Ultra-short: `1m`, `3m`, `5m`
* Intraday: `15m`, `1h`, `2h`, `6h`, `12h`
* Swing: `1d`, `3d`
* Macro: `1w`, `1M`

---

### ♾ Infinite Scrolling

* Automatic historical data loading when panning left
* Maintains viewport position after loading
* Graceful fallback for non-paginated APIs

---

### 🎯 UX Details

* Smooth zoom & pan (mouse + trackpad)
* Dynamic tooltip with:

  * OHLC values
  * EMA 200
  * Volume
  * Custom indicator
* Auto-adjusting timeframe buttons per provider
* Visual indicator when no more data:

  * 🦕 “Dino mode” appears at historical limit

---

## 🧱 Architecture

### 1. Data Layer

Each provider implements a unified interface:

```js
{
  mapSymbol(asset),
  fetch(symbol, interval, limit, endTime),
  supportedTimeframes,
  supportsInfinite
}
```

#### Fetchers

* `fetchBinance()`
* `fetchBybit()`
* `fetchCoinGecko()`
* `fetchCoinPaprika()`

All return normalized OHLCV:

```js
{
  time: number (seconds),
  open: number,
  high: number,
  low: number,
  close: number,
  volume: number
}
```

---

### 2. State Management

Global state variables:

```js
currentAsset
currentProvider
currentTimeframe
allData
oldestTimeMs
hasMore
isLoading
```

---

### 3. Indicator Engine

#### EMA 200

```js
EMA = (Close - PrevEMA) * k + PrevEMA
k = 2 / (period + 1)
```


Safeguards:

* Division-by-zero protection
* Value clamping
* Log-scale compatibility

---

### 4. Rendering Layer

Built with **ECharts 5**

#### Series:

* `candlestick`
* `line` (EMA 200)
* `scatter` (indicator)

#### Layout:

```
[ Price Chart (log scale) ]
[ Indicator Panel (log scale) ]
```

#### DataZoom:

* Inside zoom (mouse)
* Slider (bottom)

---

### 5. Infinite Data Engine

Triggered on:

```js
chart.on('datazoom')
```

Logic:

1. Detect proximity to oldest candle
2. Fetch older data using `endTime`
3. Prepend dataset
4. Recalculate viewport percentages
5. Restore zoom window

---

## 📦 Installation

No build step required.

### Option 1: Run locally

```bash
git clone <repo>
cd crypto-charts
open btc_chart.html
```

### Option 2: Serve via HTTP

```bash
python -m http.server 8080
```

---

## 🌐 API Endpoints Used

### Binance

```
GET /api/v3/klines
```

### Bybit

```
GET /v5/market/kline
```

### CoinGecko

```
GET /coins/{id}/ohlc
```

### CoinPaprika

```
GET /coins/{id}/ohlcv
```

---

## ⚙️ Configuration

### Add New Asset

Edit:

```js
const ASSETS = {
  NEW: {
    label: 'NEW/USD',
    binance: 'NEWUSDT',
    bybit: 'NEWUSDT',
    gecko: 'new',
    paprika: 'new-new'
  }
}
```

---

### Add New Provider

1. Create fetch function
2. Add config:

```js
PROVIDERS.newProvider = {
  name: 'NewProvider',
  supportedTimeframes: [...],
  mapSymbol: fn,
  fetch: fn,
  supportsInfinite: true/false
}
```

---

## ⚠️ Limitations

* No backend → subject to API rate limits
* CoinGecko:

  * No volume data
  * No pagination
* No caching layer
* No WebSocket (REST polling only)

---

## 🔮 Future Improvements

* WebSocket live candles
* Volume profile / order flow
* Drawing tools (trendlines, fibs)
* Indicator library (RSI, MACD)
* Multi-chart layout
* Mobile gesture optimization
* Local caching (IndexedDB)

---

## 🧠 Design Philosophy

* **Minimal dependencies**
* **Max performance**
* **Readable math**
* **Trader-first UX**

---

## 📄 License

MIT License

---

## 🙌 Credits

* [Apache ECharts](https://echarts.apache.org/)
* Binance, Bybit, CoinGecko, CoinPaprika APIs

