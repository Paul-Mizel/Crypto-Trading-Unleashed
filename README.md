# Crypto-Trading-Unleashed
Crypto Trading Unleashed - Strategies and Tactics for Consistent Profits


### Golden Cross and Death Cross Alert

```js
//@version=5
indicator(title='Golden Cross and Death Cross Alert', shorttitle='PM Golden and Death Cross', overlay=true)

src = input(close, title='Source')
ma1 = input.int(50, minval=1, title='MA 1')
ma2 = input.int(200, minval=1, title='MA 2')
exponential = input(false, title='Exponential MA')

ema_1 = ta.ema(src, ma1)
sma_1 = ta.sma(src, ma1)
select1 = exponential ? ema_1 : sma_1
ema_2 = ta.ema(src, ma2)
sma_2 = ta.sma(src, ma2)
select2 = exponential ? ema_2 : sma_2

golden_cross() => select1 > select2
death_cross() => select1 < select2

IsGolden = golden_cross() and death_cross()[1]
IsDeath = death_cross() and golden_cross()[1]

bgcolor(IsGolden ? #FFD700 : IsDeath ? #D3D3D3 : na, title='Golden & Death Cross Background Color', transp=0)

plotshape(IsGolden, color=color.new(color.black, 0), style=shape.xcross, text='Golden', location=location.top)
plotshape(IsDeath, color=color.new(color.black, 0), style=shape.xcross, text='Death', location=location.top)
```
