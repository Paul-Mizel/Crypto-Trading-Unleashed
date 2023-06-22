

### All Moving Averages [paulmzl]

```js
//@version=5
indicator("All Moving Averages [paulmzl]", "PM_ALL_MAS", overlay=true)

long_color = input(#26A69A, "Long", group="Colors", inline="Cross Colors") 
short_color = input(#FF5252, "Short", group="Colors", inline="Cross Colors")


// Moving Average Type
//options=["SMA", "EMA", "WMA", "HMA", "DEMA", "TEMA", "LWMA", "SMMA", "AMA", "KAMA"])
enabled_1 = input.bool(true, "Enables", group="MA1", inline = "MA1")
maType_1 = input.string(title = "Type", defval = "SMA", options=["SMA", "EMA", "SMMA (RMA)", "WMA", "VWMA"], group="MA1", inline = "MA1")
color_1 = input.color(color.red, "Color", group="MA1", inline = "MA1") 
length_1 = input.int(20, "Length", minval=1, group="MA1")

enabled_2 = input.bool(true, "Enables", group="MA2", inline = "MA2")
maType_2 = input.string(title = "Type", defval = "EMA", options=["SMA", "EMA", "SMMA (RMA)", "WMA", "VWMA"], group="MA2", inline = "MA2")
color_2 = input.color(color.blue, "Color", group="MA2", inline = "MA2")
fill_2 = input.bool(false, "Fill", group="MA2", inline = "MA2")
length_2 = input.int(50, "Length", minval=1, group="MA2")


enabled_3 = input.bool(true, "Enables", group="MA3", inline = "MA3")
maType_3 = input.string(title = "Type", defval = "EMA", options=["SMA", "EMA", "SMMA (RMA)", "WMA", "VWMA"], group="MA3", inline = "MA3")
color_3 = input.color(color.orange, "Color", group="MA3", inline = "MA3")
fill_3 = input.bool(false, "Fill", group="MA3", inline = "MA3")
length_3 = input.int(200, "Length", minval=1, group="MA3")


calculateMA(type, length, source) =>
    switch type
        "SMA" => ta.sma(source, length)
        "EMA" => ta.ema(source, length)
        "SMMA (RMA)" => ta.rma(source, length)
        "WMA" => ta.wma(source, length)
        "VWMA" => ta.vwma(source, length)


// Calculate and plot Moving Average
ma1 = calculateMA(maType_1, length_1, close)
p1=plot(enabled_1?ma1:na, title="MA1", linewidth=1, color = color_1)

ma2 = calculateMA(maType_2, length_2, close)
p2=plot(enabled_2?ma2:na, title="MA2", linewidth=2, color = color_2)
fillColor1_2=ma1>=ma2 ? color.green : color.red
fill(p1,p2,color=color.new(fillColor1_2,fill_2?90:100))

ma3 = calculateMA(maType_3, length_3, close)
p3=plot(enabled_3?ma3:na, title="MA2", linewidth=4, color = color_3)
fillColor2_3=ma2>=ma3 ? color.green : color.red
fill(p2,p3,color=color.new(fillColor2_3,fill_3?90:100))

fillColor1_3=ma1>=ma3 ? color.green : color.red
fill(p1,p3,color=color.new(fillColor1_3,fill_3?90:100))


//signal


long_signal = ta.crossover(ma2, ma3) and ma2 > ma3 and ma1 > ma3
short_signal = ta.crossunder(ma2, ma3) and ma2 < ma3 and ma1 < ma3

plotshape(long_signal ? low  : na, title='Long', text='Long', location=location.absolute, style=shape.labelup, size=size.tiny, color=long_color, textcolor=color.new(color.white, 0))
plotshape(short_signal ? high  : na, title='Short', text='Short', location=location.absolute, style=shape.labeldown, size=size.tiny, color=short_color, textcolor=color.new(color.white, 0))


```