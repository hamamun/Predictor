# Reversal Forecast for Exness Terminal

A custom Indie indicator that starts **after a reversal candle has closed**. It studies older, comparable reversal setups and estimates the likely direction, entry trigger, stop, take-profit distance, and number of bars until the next opposite reversal.

## Indicator file

`reversal_forecast.indie` is the complete chart indicator. It is designed for M5 and M15 charts and adapts its calculations to the chart symbol using ATR and that symbol's own volume history.

## What it does

1. Detects bullish or bearish engulfing candles and rejection-wick (pin-bar) candles.
2. Waits until the candle is closed before signalling; the setup marker is placed back on that candle.
3. Compares the setup with earlier same-direction reversal candles, matching the prior trend, relative volume, distance from a recent swing, and candle family.
4. Uses only historical setups whose full outcome window is already in the past. It simulates the same entry and stop rules, and counts a setup as a success only if its configured reward/risk target is reached before the stop. If one OHLC candle touches both, it conservatively counts as a loss. Similar samples are spaced at least one forecast horizon apart to reduce overlap.
5. Estimates the weighted historical follow-through rate, average favorable movement among successful analogs, and typical bars from the reversal signal to the next opposite reversal.
6. Shows an entry trigger, a stop beyond the reversal candle, and a target based on the similar historical moves. Weak forecasts are withheld when there are too few analogs, follow-through is below the selected threshold, or estimated reward/risk is too small. The candle itself is still labelled as a candidate.

### Starting settings

- Historical lookback: 600 bars
- Maximum outcome window: 24 bars
- ATR: 14 bars
- Minimum similar examples: 6
- Minimum historical follow-through: 55%
- Minimum forecast reward/risk: 1.2

All are adjustable in the indicator settings. The forecast horizon means **bars on the chart timeframe** (for example, 24 M5 bars or 24 M15 bars).

## Volume note

The indicator normalizes the signal candle's volume against recent volume on the same symbol. Depending on the instrument and Exness feed, this may be tick volume rather than consolidated, market-wide traded volume. If volume is missing or unavailable, its similarity feature falls back to a neutral value.

## Add it to Exness Terminal

1. Open Exness Terminal in a web browser and open an M5 or M15 chart.
2. Open **Indicators** and choose **Create new script**.
3. Copy all of `reversal_forecast.indie` into the editor, save it, and add it to the chart.
4. Check that the script compiles in the actual Exness Indie runtime. Then inspect both bullish and bearish historical examples on several symbols.

The local repository has no Exness/Indie runtime, so a successful local syntax check cannot prove platform compilation. The target platform's compiler is the final compatibility check.

## Important interpretation

The displayed follow-through percentage is the similarity-weighted share of selected historical analogs whose entry triggered and whose configured reward/risk target was reached before the stop. When the same OHLC candle touches both stop and target, the sample is conservatively counted as a loss. This percentage is **not** a guaranteed or automatically calibrated probability of a profitable trade. The indicator does not place trades. Historical tests should include realistic spreads, slippage, and separate data for developing versus evaluating the rules.
