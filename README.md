# Reversal Forecast for Exness Terminal

Two separate Indie indicators are available. Both start **after a reversal candle has closed**, use older comparable setups for historical estimates, and are designed for M5/M15 charts and other symbols.

## Choose an indicator

| File | Status | Use it for |
| --- | --- | --- |
| `reversal_forecast.indie` | Original predictor, unchanged | The existing engulfing/pin-bar historical-analog forecast. |
| `confirmed_reversal_forecast.indie` | New separate variant | A stricter confirmed-reversal forecast with fewer false reversal signals and a duration range. |

Copy only one file into one Exness custom-indicator script. The original file has deliberately been kept separate and unchanged.

## Confirmed Reversal Forecast — `confirmed_reversal_forecast.indie`

### Signal rules

1. Only three reversal pattern families are used:
   - Bullish/Bearish Engulfing.
   - Hammer, Inverted Hammer, Hanging Man and Shooting Star, interpreted by prior trend and swing location.
   - Morning Star and Evening Star, including Doji Star versions. A Doji alone has no direction and is not a standalone signal.
2. A candidate must appear near a recent swing after a meaningful ATR-normalized move in the opposite direction. The fixed thresholds are one ATR of prior trend and one ATR of swing tolerance; the default lookbacks are 12 bars for trend and 20 bars for swing context, both adjustable.
3. The closed candle is shown first as an **unconfirmed candidate**. BUY is confirmed only when a later candle closes above the candidate high; SELL is confirmed only when a later candle closes below the candidate low. Confirmation must occur within two closed bars.
4. Nearby raw patterns are merged: only one pending candidate exists at a time, and a direction-specific cooldown of roughly one swing window prevents repeated signals from the same swing. An opposite confirmed reversal can start a fresh forecast.
5. Historical analogs use exactly the same pattern, context and confirmation definition. Only fully observed historical episodes are sampled, and accepted analogs are separated by one forecast horizon to reduce overlapping examples.

Other candlestick patterns from the reviewed list are intentionally left out until they show a measured benefit. The goal is a smaller, more consistent signal set rather than more signals.

### Forecast and dashboard

The indicator estimates direction, entry, stop, TP, expected ATR move, historical follow-through, sample count and duration from similar confirmed historical setups. Historical success requires the configured reward/risk target before the stop; if one OHLC candle touches both, the sample is conservatively counted as a loss.

Duration is shown as a **typical estimate plus a historical range**, for example `9 bars (6-13)`. The range is the similarity-weighted mean plus or minus a bounded historical spread. The upper end is the forecast's hard expiry; there are **no rolling TP extensions**. Stop, target, opposite confirmed reversal, replacement by a newer signal, or expiry ends the forecast using closed candles only.

The upper-left dashboard below the indicator header shows status, pattern, any pending candidate, data through the last closed candle, entry/trigger, SL, TP, expected move, duration range, quality, follow-through/samples, forecast expiry, no-signal reason, and the last result/update age. When no forecast is active, the status is **Waiting for new confirmed reversal**; retained levels are labeled `Last entry`, `Last SL`, `Last TP`, and so on. `Data through` uses the chart bar count so a frozen panel is easy to detect, and `Last result / update` reports how many bars ago the last meaningful event occurred. `No-signal reason` distinguishes a candidate that expired, an active one-signal-per-swing cooldown, a nearby pattern merged into an existing swing, and a latest closed candle with no qualifying setup. A yellow dot marks an unconfirmed candidate; green/red dots mark confirmed bullish/bearish reversals. Active levels use small step plots.

### Starting settings

- Historical lookback: 600 bars
- Maximum outcome window: 24 bars
- ATR: 14 bars
- Prior trend: 12 bars
- Swing context: 20 bars
- Minimum similar examples: 6
- Minimum historical follow-through: 55%
- Minimum forecast reward/risk: 1.2

The forecast horizon means **bars on the chart timeframe** (for example, 24 M5 bars or 24 M15 bars). All settings are adjustable in the indicator panel.

## Original predictor — `reversal_forecast.indie`

The original indicator detects bullish/bearish engulfing and rejection-wick candles on closed bars, compares prior trend, relative volume, swing distance and candle family with older setups, and displays a single expected duration. It continues to use its original dashboard and forecast-level behavior.

## Volume note

Both indicators normalize signal-candle volume against recent volume on the same symbol. Depending on the instrument and Exness feed, this may be tick volume rather than consolidated, market-wide traded volume. If volume is missing or unavailable, the similarity feature falls back to a neutral value.

## Add it to Exness Terminal

1. Open Exness Terminal in a web browser and open an M5 or M15 chart.
2. Open **Indicators** and choose **Create new script**.
3. Copy all of either `reversal_forecast.indie` or `confirmed_reversal_forecast.indie` into the editor, save it, and add it to the chart.
4. Check that the script compiles in the actual Exness Indie runtime. Then inspect bullish and bearish examples on several symbols.

The local repository has no Exness/Indie runtime, so local syntax and simulation checks cannot prove platform compilation. The target platform's compiler is the final compatibility check.

## Important interpretation

The displayed follow-through percentage is a similarity-weighted historical statistic, not a guaranteed or automatically calibrated probability of a profitable trade. A duration range is an estimate of historical variation, not a deadline for price to reverse. The indicators do not place trades. Historical tests should include realistic spreads, slippage, and separate data for developing versus evaluating the rules.
