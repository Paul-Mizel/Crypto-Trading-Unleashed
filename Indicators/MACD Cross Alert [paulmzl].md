### MACD Cross Alert [paulmzl]

```js
// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © paulmzl

//@version=5
indicator(title="MACD Cross Alert [paulmzl]", shorttitle="PM_MACD_ALERT", timeframe="", timeframe_gaps=true)
// Getting inputs
fast_length = input(title="MACD Fast Length", defval=12)
slow_length = input(title="MACD Slow Length", defval=26)
src = input(title="MACD Source", defval=close)
signal_length = input.int(title="MACD Signal Smoothing",  minval = 1, maxval = 50, defval = 9)
sma_source = input.string(title="MACD Oscillator MA Type",  defval="EMA", options=["SMA", "EMA"])
sma_signal = input.string(title="MACD Signal Line MA Type", defval="EMA", options=["SMA", "EMA"])
// Plot colors
col_macd = input(#2962FF,   "MACD Line", group="Color Settings", inline="MACD")
col_signal = input(#FF6D00, "MACD Signal Line", group="Color Settings", inline="Signal")
col_grow_above = input(#26A69A, "MACD Above Grow", group="Histogram", inline="Above")
col_fall_above = input(#B2DFDB, "MACD Fall", group="Histogram", inline="Above")
col_grow_below = input(#FFCDD2, "MACD Below Grow", group="Histogram", inline="Below")
col_fall_below = input(#FF5252, "MACD Fall", group="Histogram", inline="Below")

string filterType = input.string("None", "MACD EMA Filter", options = ["None", "EMA 10", "EMA 20", "EMA 50", "EMA 100", "EMA 200"], group="Filter")

//macd_ema20_filter = input.bool(false, "MACD EMA20", group="Filter")
//macd_ema50_filter = input.bool(false, "MACD EMA50", group="Filter")
//macd_ema100_filter = input.bool(true, "MACD EMA100", group="Filter")
//macd_ema200_filter = input.bool(false, "MACD EMA200", group="Filter")

bool mcad_ema_long_filter = switch filterType
    "EMA 10" => ta.ema(src, 10) <= close
    "EMA 20" => ta.ema(src, 20) <= close
    "EMA 50" => ta.ema(src, 50) <= close
    "EMA 100" => ta.ema(src, 10) <= close
    "EMA 200" => ta.ema(src, 200) <= close
    => true
bool mcad_ema_short_filter = switch filterType
    "EMA 10" => ta.ema(src, 10) > close
    "EMA 20" => ta.ema(src, 20) > close
    "EMA 50" => ta.ema(src, 50) > close
    "EMA 100" => ta.ema(src, 100) > close
    "EMA 200" => ta.ema(src, 200) > close
    => true  

// Calculating
fast_ma = sma_source == "EMA" ? ta.ema(src, fast_length) : ta.sma(src, fast_length)
slow_ma = sma_source == "EMA" ? ta.ema(src, slow_length) : ta.sma(src, slow_length)
macd = fast_ma - slow_ma
signal = sma_signal == "EMA" ? ta.ema(macd, signal_length) : ta.sma(macd, signal_length)
hist = macd - signal

plot(hist, title="Histogram", style=plot.style_columns, color=(hist>=0 ? (hist[1] < hist ? col_grow_above : col_fall_above) : (hist[1] < hist ? col_grow_below : col_fall_below)))
plot(macd, title="MACD", color=col_macd)
plot(signal, title="Signal", color=col_signal) 

macd_long =  (ta.crossover(signal,macd)  and signal < 0 or (ta.crossover(macd, 0)  and signal >= 0 or ta.crossover(signal, 0) and macd >= 0))
macd_short = (ta.crossunder(signal,macd) and signal > 0 or (ta.crossunder(macd, 0) and signal < 0 or ta.crossunder(signal, 0) and macd < 0))

//signal
plotshape(macd_long ? signal : na, title='Long', text='Long', location=location.absolute, style=shape.labeldown, size=size.tiny, color=color.new(color.green, 0), textcolor=color.new(color.white, 0))
plotshape(macd_short ? signal : na, title='Short', text='Short', location=location.absolute, style=shape.labelup, size=size.tiny, color=color.new(color.red, 0), textcolor=color.new(color.white, 0))


plot(macd_long or macd_short ? signal : na, color = color.black , style = plot.style_circles, linewidth = 3)
plot(macd_long or macd_short ? signal : na, color = (signal - macd > 0 ? col_fall_below : col_grow_above) , style = plot.style_circles, linewidth = 2)

//plot(macd_long or macd_short  ? -0.05 : na , style=plot.style_cross, color = (signal - macd > 0 ? col_fall_below : col_grow_above), title="Cross Confirmed", linewidth = 5)

//plotshape(hist>=0 and (hist[1] > hist) or  hist<0 and hist[1] < hist? -300 : na, style=shape.diamond, location=location.absolute, color=(hist>=0 and hist[1] > hist ? col_fall_below : col_grow_above), title="Cross Confirmed", size=size.small)


mcad_ema_filter_data = switch filterType
    "EMA 10" => close-ta.ema(src, 10)
    "EMA 20" => close-ta.ema(src, 20)
    "EMA 50" => close-ta.ema(src, 50)
    "EMA 100" => close-ta.ema(src, 100)
    "EMA 200" => close-ta.ema(src, 200)
    => na 

plot(mcad_ema_filter_data and ta.cross(mcad_ema_filter_data, 0) ? mcad_ema_filter_data : na, color = color.black , style = plot.style_circles, linewidth = 3)
plot(mcad_ema_filter_data and ta.cross(mcad_ema_filter_data, 0) ? mcad_ema_filter_data : na, color = (mcad_ema_filter_data >= 0 ? col_grow_above: col_fall_below) , style = plot.style_circles, linewidth = 2)


//plot(mcad_ema_filter_data?mcad_ema_filter_data:na, color = mcad_ema_filter_data >= 0?color.lime:color.red)
//plot(mcad_ema_filter_data and ta.cross(mcad_ema_filter_data, signal) ? -0.07 : na , style=plot.style_cross, color = (mcad_ema_filter_data < 0 ? col_fall_below : col_grow_above), title="Cross Confirmed", linewidth = 3)


//mcad_bg_color=(hist>=0 ? (hist[1] < hist ? #00E00010 : #E0000010) : (hist[1] < hist ? #00E00010 : #E0000010 ))

//bgcolor(mcad_bg_color)

```

