

### Auto Fib Retracement [paulmzl]

```js
//@version=5
indicator("Auto Fib Retracement [paulmzl]" , "PM_AUTO_FIB", overlay=true)

devTooltip = "Deviation is a multiplier that affects how much the price should deviate from the previous pivot in order for the bar to become a new pivot."
depthTooltip = "The minimum number of bars that will be taken into account when calculating the indicator."
// pivots threshold
threshold_multiplier = input.float(title="Deviation", defval=3, minval=0, tooltip=devTooltip)
depth = input.int(title="Depth", defval=10, minval=2, tooltip=depthTooltip)
reverse = input(false, "Reverse")
var extendLeft = input(false, "Extend Left    |    Extend Right", inline = "Extend Lines")
var extendRight = input(true, "", inline = "Extend Lines")
var extending = extend.none
if extendLeft and extendRight
    extending := extend.both
if extendLeft and not extendRight
    extending := extend.left
if not extendLeft and extendRight
    extending := extend.right
prices = input(true, "Show Prices")
levels = input(true, "Show Levels", inline = "Levels")
levelsFormat = input.string("Values", "", options = ["Values", "Percent"], inline = "Levels")
labelsPosition = input.string("Left", "Labels Position", options = ["Left", "Right"])
var int backgroundTransparency = input.int(85, "Background Transparency", minval = 0, maxval = 100)

import TradingView/ZigZag/6 as zigzag

update()=>
    var settings = zigzag.Settings.new(threshold_multiplier, depth, color(na), false, false, false, false, "Absolute", true)
    var zigzag.ZigZag zigZag = zigzag.newInstance(settings)
    var zigzag.Pivot lastP = na
    var float startPrice = na
    var float height = na
    settings.devThreshold := ta.atr(10) / close * 100 * threshold_multiplier
    if zigZag.update()
        lastP := zigZag.lastPivot()
        if not na(lastP)
            var line lineLast = na
            if na(lineLast)
                lineLast := line.new(lastP.start.tm, lastP.start.price, lastP.end.tm, lastP.end.price, xloc=xloc.bar_time, color=color.gray, width=1, style=line.style_dashed)
            else
                line.set_xy1(lineLast, lastP.start.tm, lastP.start.price)
                line.set_xy2(lineLast, lastP.end.tm, lastP.end.price)

            startPrice := reverse ? lastP.start.price : lastP.end.price
            endPrice = reverse ? lastP.end.price : lastP.start.price
            height := (startPrice > endPrice ? -1 : 1) * math.abs(startPrice - endPrice)
    [lastP, startPrice, height]

[lastP, startPrice, height] = update()

_draw_line(price, col) =>
    var id = line.new(lastP.start.tm, lastP.start.price, time, price, xloc=xloc.bar_time, color=col, width=1, extend=extending)
    line.set_xy1(id, lastP.start.tm, price)
    line.set_xy2(id, lastP.end.tm, price)
	id


_draw_label(price, txt, txtColor) =>
    x = labelsPosition == "Left" ? lastP.start.tm : not extendRight ? lastP.end.tm : time
    labelStyle = labelsPosition == "Left" ? label.style_label_right : label.style_label_left
    align = labelsPosition == "Left" ? text.align_right : text.align_left
    labelsAlignStrLeft = txt + '\n ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏ \n'
    labelsAlignStrRight = '       ' + txt + '\n ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏ \n'
    labelsAlignStr = labelsPosition == "Left" ? labelsAlignStrLeft : labelsAlignStrRight
    var id = label.new(x=x, y=price, xloc=xloc.bar_time, text=labelsAlignStr, textcolor=txtColor, style=labelStyle, textalign=align, color=#00000000)
    label.set_xy(id, x, price)
    label.set_text(id, labelsAlignStr)
    label.set_textcolor(id, txtColor)

_wrap(txt) =>
    "(" + str.tostring(txt, format.mintick) + ")"

_label_txt(level, price) =>
    l = levelsFormat == "Values" ? str.tostring(level) : str.tostring(level * 100) + "%"
    (levels ? l : "") + (prices ? _wrap(price) : "")

_crossing_level(series float sr, series float r) =>
    (r > sr and r < sr[1]) or (r < sr and r > sr[1])

processLevel(bool show, float value, color colorL, line lineIdOther) =>
    float m = value
	r = startPrice + height * m
    crossed = _crossing_level(close, r)
    if show and not na(lastP)
		lineId = _draw_line(r, colorL)
        _draw_label(r, _label_txt(m, r), colorL)
        if crossed
            alert("Autofib: " + syminfo.ticker + " crossing level " + str.tostring(value))
        if not na(lineIdOther)
            linefill.new(lineId, lineIdOther, color = color.new(colorL, backgroundTransparency))
		lineId
    else
		lineIdOther

show_0 = input(true, "", inline = "Level0")
value_0 = input(0, "", inline = "Level0")
color_0 = input(#787b86, "", inline = "Level0")

show_0_236 = input(true, "", inline = "Level0")
value_0_236 = input(0.236, "", inline = "Level0")
color_0_236 = input(#f44336, "", inline = "Level0")

show_0_382 = input(true, "", inline = "Level1")
value_0_382 = input(0.382, "", inline = "Level1")
color_0_382 = input(#81c784, "", inline = "Level1")

show_0_5 = input(true, "", inline = "Level1")
value_0_5 = input(0.5, "", inline = "Level1")
color_0_5 = input(#4caf50, "", inline = "Level1")

show_0_618 = input(true, "", inline = "Level2")
value_0_618 = input(0.618, "", inline = "Level2")
color_0_618 = input(#009688, "", inline = "Level2")

show_0_65 = input(false, "", inline = "Level2")
value_0_65 = input(0.65, "", inline = "Level2")
color_0_65 = input(#009688, "", inline = "Level2")

show_0_786 = input(true, "", inline = "Level3")
value_0_786 = input(0.786, "", inline = "Level3")
color_0_786 = input(#64b5f6, "", inline = "Level3")

show_1 = input(true, "", inline = "Level3")
value_1 = input(1, "", inline = "Level3")
color_1 = input(#787b86, "", inline = "Level3")

show_1_272 = input(false, "", inline = "Level4")
value_1_272 = input(1.272, "", inline = "Level4")
color_1_272 = input(#81c784, "", inline = "Level4")

show_1_414 = input(false, "", inline = "Level4")
value_1_414 = input(1.414, "", inline = "Level4")
color_1_414 = input(#f44336, "", inline = "Level4")

show_1_618 = input(true, "", inline = "Level5")
value_1_618 = input(1.618, "", inline = "Level5")
color_1_618 = input(#2962ff, "", inline = "Level5")

show_1_65 = input(false, "", inline = "Level5")
value_1_65 = input(1.65, "", inline = "Level5")
color_1_65 = input(#2962ff, "", inline = "Level5")

show_2_618 = input(true, "", inline = "Level6")
value_2_618 = input(2.618, "", inline = "Level6")
color_2_618 = input(#f44336, "", inline = "Level6")

show_2_65 = input(false, "", inline = "Level6")
value_2_65 = input(2.65, "", inline = "Level6")
color_2_65 = input(#f44336, "", inline = "Level6")

show_3_618 = input(true, "", inline = "Level7")
value_3_618 = input(3.618, "", inline = "Level7")
color_3_618 = input(#9c27b0, "", inline = "Level7")

show_3_65 = input(false, "", inline = "Level7")
value_3_65 = input(3.65, "", inline = "Level7")
color_3_65 = input(#9c27b0, "", inline = "Level7")

show_4_236 = input(true, "", inline = "Level8")
value_4_236 = input(4.236, "", inline = "Level8")
color_4_236 = input(#e91e63, "", inline = "Level8")

show_4_618 = input(false, "", inline = "Level8")
value_4_618 = input(4.618, "", inline = "Level8")
color_4_618 = input(#81c784, "", inline = "Level8")

show_neg_0_236 = input(false, "", inline = "Level9")
value_neg_0_236 = input(-0.236, "", inline = "Level9")
color_neg_0_236 = input(#f44336, "", inline = "Level9")

show_neg_0_382 = input(false, "", inline = "Level9")
value_neg_0_382 = input(-0.382, "", inline = "Level9")
color_neg_0_382 = input(#81c784, "", inline = "Level9")

show_neg_0_618 = input(false, "", inline = "Level10")
value_neg_0_618 = input(-0.618, "", inline = "Level10")
color_neg_0_618 = input(#009688, "", inline = "Level10")

show_neg_0_65 = input(false, "", inline = "Level10")
value_neg_0_65 = input(-0.65, "", inline = "Level10")
color_neg_0_65 = input(#009688, "", inline = "Level10")


lineId0 = processLevel(show_neg_0_65, value_neg_0_65, color_neg_0_65, line(na))
lineId1 = processLevel(show_neg_0_618, value_neg_0_618, color_neg_0_618, lineId0)
lineId2 = processLevel(show_neg_0_382, value_neg_0_382, color_neg_0_382, lineId1)
lineId3 = processLevel(show_neg_0_236, value_neg_0_236, color_neg_0_236, lineId2)
lineId4 = processLevel(show_0, value_0, color_0, lineId3)
lineId5 = processLevel(show_0_236, value_0_236, color_0_236, lineId4)
lineId6 = processLevel(show_0_382, value_0_382, color_0_382, lineId5)
lineId7 = processLevel(show_0_5, value_0_5, color_0_5, lineId6)
lineId8 = processLevel(show_0_618, value_0_618, color_0_618, lineId7)
lineId9 = processLevel(show_0_65, value_0_65, color_0_65, lineId8)
lineId10 = processLevel(show_0_786, value_0_786, color_0_786, lineId9)
lineId11 = processLevel(show_1, value_1, color_1, lineId10)
lineId12 = processLevel(show_1_272, value_1_272, color_1_272, lineId11)
lineId13 = processLevel(show_1_414, value_1_414, color_1_414, lineId12)
lineId14 = processLevel(show_1_618, value_1_618, color_1_618, lineId13)
lineId15 = processLevel(show_1_65, value_1_65, color_1_65, lineId14)
lineId16 = processLevel(show_2_618, value_2_618, color_2_618, lineId15)
lineId17 = processLevel(show_2_65, value_2_65, color_2_65, lineId16)
lineId18 = processLevel(show_3_618, value_3_618, color_3_618, lineId17)
lineId19 = processLevel(show_3_65, value_3_65, color_3_65, lineId18)
lineId20 = processLevel(show_4_236, value_4_236, color_4_236, lineId19)
lineId21 = processLevel(show_4_618, value_4_618, color_4_618, lineId20)

```