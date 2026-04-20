# RiskSizer

使用 MEXC 資料。輸入每單預計最大虧損 USDT 與止損價格，系統自動抓取目前市價，依照進場價與止損價的距離，再加上預估 round-trip fee，一起反推出建議總倉位，讓每筆交易打到止損時的總損失更接近你設定的固定 USDT。工具支援 MEXC 交易對搜尋、最新價格帶入、手動覆寫進場價，以及可選的止盈獲利估算，採用純前端 HTML、CSS、Vanilla JavaScript 製作。

## Features

- Search MEXC spot trading pairs from `exchangeInfo`
- Auto-fill `entry price` from the latest ticker price
- Manual entry price override
- `Use latest price` button
- LONG and SHORT validation
- Fee-aware risk-based position sizing
- Position sizing includes stop distance and estimated round-trip fee
- Optional take-profit input with estimated profit in USDT
- No backend required

## Inputs

- `side`: `LONG` or `SHORT`
- `risk USDT`
- `entry price`
- `stop loss price`
- `take profit price` (optional)

## Formula

```text
effective_risk = risk_usdt * 0.98
distance = abs(entry - stop)
price_loss_ratio = distance / entry
total_loss_ratio = price_loss_ratio + 0.001
position_usdt = effective_risk / total_loss_ratio
qty = position_usdt / entry
estimated_price_loss = position_usdt * price_loss_ratio
estimated_fee_loss = position_usdt * 0.001
estimated_loss = estimated_price_loss + estimated_fee_loss
estimated_profit =
  LONG  => (takeProfit - entry) * qty
  SHORT => (entry - takeProfit) * qty
```

## Default Settings

- `POSITION_BUFFER = 0.98`
- `ESTIMATED_ROUND_TRIP_FEE_RATE = 0.001`

The sizing model is fee-aware. `position_usdt` is determined by both stop distance and the estimated round-trip fee, instead of using a large fixed haircut on position size.

## Validation

- LONG: `stop < entry`
- SHORT: `stop > entry`
- `distance` cannot be `0`
- LONG with take profit: `takeProfit > entry`
- SHORT with take profit: `takeProfit < entry`

## Local Use

1. Download or clone this repository.
2. Open `index.html` in a browser.

If the MEXC pair list cannot be loaded because of browser CORS restrictions, you can still type a full symbol like `TAOUSDT` and click `使用最新價格`.

## Files

- [index.html](index.html)
- [style.css](style.css)
- [app.js](app.js)

## Notes

- This project uses public MEXC API endpoints via `fetch`.
- Browser access to exchange APIs can be affected by CORS policies.
- The app includes fallback proxy and local cache logic, but third-party proxy availability can still change over time.
- The default fee assumption is based on a round-trip taker fee estimate of `0.10%`.
