### Golden Cross and Death Cross [paulmzl]

```js
//@version=5
indicator(title='Golden Cross and Death Cross [paulmzl]', shorttitle='PM_GOLDEN_DEATH_CROSS', overlay=true)

long_color = input(#26A69A, "Long", group="Colors", inline="Cross Colors") 
short_color = input(#FF5252, "Short", group="Colors", inline="Cross Colors")

src = input(close, title='Source')
ma1 = input.int(50, minval=1, title='MA 1')
ma2 = input.int(200, minval=1, title='MA 2')
exponential = input(false, title='Exponential MA')

s1 = exponential ? ta.ema(src, ma1) : ta.sma(src, ma1)
s2 = exponential ? ta.ema(src, ma2) : ta.sma(src, ma2)

IsGolden = ta.crossover(s1, s2) 
IsDeath = ta.crossunder(s1, s2)

bgcolor(IsGolden ? #FFD700 : IsDeath ? #D3D3D3 : na, title='Background Color')
//signal
plotshape(IsGolden ? low  : na,
     title='Long', text='Long', location=location.absolute, 
     style=shape.labelup, size=size.tiny, color=long_color, textcolor=color.new(color.white, 0))
plotshape(IsDeath ? high  : na, 
     title='Short', text='Short', location=location.absolute, 
     style=shape.labeldown, size=size.tiny, color=short_color, textcolor=color.new(color.white, 0))
```
