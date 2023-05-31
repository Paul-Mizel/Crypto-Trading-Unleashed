
### Heiken-Ashi Candles [paulmzl]

```js
//@version=5
indicator(title = "Heiken-Ashi Candles [paulmzl]", shorttitle = "PM_HEIKEN_ASHI", overlay = false)

long_color = input(#26A69A, "Long", group="Colors", inline="Cross Colors") 
short_color = input(#FF5252, "Short", group="Colors", inline="Cross Colors")

haClose = (open + high + low + close) / 4
haOpen  = float(na)
haOpen := na(haOpen[1]) ? (open + close) / 2 : (nz(haOpen[1]) + nz(haClose[1])) / 2
haHigh  = math.max(high, math.max(haOpen, haClose))
haLow   = math.min(low,  math.min(haOpen, haClose))
haColor = haClose > haOpen ? long_color : short_color

plotcandle(haOpen, haHigh, haLow, haClose, title = "", color = haColor, wickcolor = haColor, bordercolor = haColor)

long_signal = haClose > haOpen and haClose[1] > haOpen[1] and haClose[2] <= haOpen[2] and haClose[3] <= haOpen[3]
short_signal = haClose < haOpen and haClose[1] < haOpen[1] and haClose[2] > haOpen[2] and haClose[3] > haOpen[3] 

//signal
plotshape(long_signal ? low  : na,
     title='Long', text='Long', location=location.absolute, 
     style=shape.labelup, size=size.tiny, color=long_color, textcolor=color.new(color.white, 0))
plotshape(short_signal ? high  : na, 
     title='Short', text='Short', location=location.absolute, 
     style=shape.labeldown, size=size.tiny, color=short_color, textcolor=color.new(color.white, 0))

```
