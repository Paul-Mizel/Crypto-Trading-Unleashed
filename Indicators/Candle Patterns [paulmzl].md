
### Candle Patterns and Pivot Trading [paulmzl]

```js
//@version=5
indicator('Candle Patterns and Pivot Trading [paulmzl]', "PM_CANDLE_PATTERNS_PIVOT_TRADING", overlay=true)

length = input.int(50, "Pivot Length")
long_color = input(#26A69A, "Long", group="Colors", inline="Cross Colors") 
short_color = input(#FF5252, "Short", group="Colors", inline="Cross Colors")
filter = input.bool(true,"Filter Empty Candles")
showPivots = input.bool(false,"Show Pivots")

o = open[length]
h = high[length]
c = close[length]
l = low[length]

ph = ta.pivothigh(close, length, length)
pl = ta.pivotlow(open, length, length)
valH = ta.valuewhen(ph, c, 0)
valL = ta.valuewhen(pl, c, 0)
valpH = ta.valuewhen(ph, c, 1)
valpL = ta.valuewhen(pl, c, 1)



d = math.abs(c - o)
hammer = pl and math.min(o, c) - l > d and h - math.max(c, o) < d
ihammer = pl and h - math.max(c, o) > d and math.min(c, o) - l < d
bulleng = c > o and c[1] < o[1] and c > o[1] and o < c[1]
hanging = ph and math.min(c, o) - l > d and h - math.max(o, c) < d
shooting = ph and h - math.max(o, c) > d and math.min(c, o) - l < d
beareng = c > o and c[1] < o[1] and c > o[1] and o < c[1]

hammer_ = 'The hammer candlestick pattern is formed of a short body with a long lower wick, and is found at the bottom of a downward trend.' + '\n' + '\n A hammer shows that although there were selling pressures during the day, ultimately a strong buying pressure drove the price back up.'
ihammer_ = 'The inverted hammer is a similar pattern than the hammer pattern. The only difference being that the upper wick is long, while the lower wick is short.' + '\n' + '\n It indicates a buying pressure, followed by a selling pressure that was not strong enough to drive the market price down. The inverse hammer suggests that buyers will soon have control of the market.'
bulleng_ = 'The bullish engulfing pattern is formed of two candlesticks. The first candle is a short red body that is completely engulfed by a larger green candle' + '\n' + '\n Though the second day opens lower than the first, the bullish market pushes the price up, culminating in an obvious win for buyers'
hanging_ = 'The hanging man is the bearish equivalent of a hammer; it has the same shape but forms at the end of an uptrend.' + '\n' + 'It indicates that there was a significant sell-off during the day, but that buyers were able to push the price up again. The large sell-off is often seen as an indication that the bulls are losing control of the market.'
shotting_ = 'The shooting star is the same shape as the inverted hammer, but is formed in an uptrend: it has a small lower body, and a long upper wick.' + '\n' + 'Usually, the market will gap slightly higher on opening and rally to an intra-day high before closing at a price just above the open – like a star falling to the ground.'
beareng_ = 'A bearish engulfing pattern occurs at the end of an uptrend. The first candle has a small green body that is engulfed by a subsequent long red candle.' + '\n' + 'It signifies a peak or slowdown of price movement, and is a sign of an impending market downturn. The lower the second candle goes, the more significant the trend is likely to be.'


n = bar_index
label lbl = na
H = valH > valpH ? 'HH' : valH < valpH ? 'LH' : na
L = valL < valpL ? 'LL' : valL > valpL ? 'HL' : na
txt = hammer ? 'Hammer' : ihammer ? 'Inverse Hammer' : bulleng ? 'Bullish Engulfing' : hanging ? 'Hanging Man' : shooting ? 'Shooting Star' : beareng ? 'Bearish Engulfing' : 'None'
des = hammer ? hammer_ : ihammer ? ihammer_ : bulleng ? bulleng_ : hanging ? hanging_ : shooting ? shotting_ : beareng ? beareng_ : ''

plotshape(showPivots and ph? valpH : na, title='HH', style=shape.triangledown, location=location.abovebar, color=short_color, text='HH', offset=-length - 1, size = size.small)
plotshape(showPivots and ph? valpH : na, title='HL', style=shape.triangleup, location=location.belowbar, color=long_color, text='HL', offset=-length - 1, size = size.small)
plotshape(showPivots and pl? valL : na, title='LH', style=shape.triangledown, location=location.abovebar, color=short_color, text='LH', offset=-length - 1, size = size.small)
plotshape(showPivots and pl? valL : na, title='LL', style=shape.triangleup, location=location.belowbar, color=long_color, text='LL', offset=-length - 1, size = size.small)



if ph and ((filter and txt != 'None') or not filter)
    lbl := label.new(n[length], math.max(c, o), H + '\n' + txt, color=short_color, style=label.style_label_down, textcolor=color.white, tooltip=des)
    label.delete(lbl[1])
else if pl and ((filter and txt != 'None') or not filter)
    lbl := label.new(n[length], math.min(c, o), L + '\n' + txt, color=long_color, style=label.style_label_up, textcolor=color.white, tooltip=des)
    label.delete(lbl[1])



	
```