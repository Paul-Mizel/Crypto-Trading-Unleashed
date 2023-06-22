

### Supply and Demand [paulmzl]

```js
//@version=5
indicator(title='Supply and Demand [paulmzl]', shorttitle='PM_SUPPLY_DEMAND', overlay=true)
src = close
len = input.int(14, minval=1, title='Length')
up = ta.rma(math.max(ta.change(src), 0), len)
down = ta.rma(-math.min(ta.change(src), 0), len)
rsi = down == 0 ? 100 : up == 0 ? 0 : 100 - 100 / (1 + up / down)
tresh = input.int(30, minval=1, title='Treshold eg. 100 - 30 = 70')
uppertreshold = 100 - tresh
lowertreshold = 0 + tresh

count = input(3, title='Confirmation Bars')

rsu = 0
rsd = 0
rsx = 0
incrementer_up = rsi > uppertreshold ? 1 : 0
incrementer_down = rsi < lowertreshold ? 1 : 0
incrementer_both = rsi > uppertreshold or rsi < lowertreshold ? 1 : 0

if incrementer_both
    rsx := nz(rsx[1], 0) + incrementer_both
    rsx
else
    rsx := 0
    rsx

rxH = if rsx >= count
    x = high
    x
rxL = if rsx >= count
    y = low
    y

rH = fixnan(rxH)
rL = fixnan(rxL)


if incrementer_up
    rsu := nz(rsu[1], 0) + incrementer_up
    rsu
else
    rsu := 0
    rsu

rssH = if rsu >= count
    x = high
    x

rssL = if rsu >= count
    y = low
    y

if incrementer_down
    rsd := nz(rsd[1], 0) + incrementer_down
    rsd
else
    rsd := 0
    rsd

rsrH = if rsd >= count
    x = high
    x

rsrL = if rsd >= count
    y = low
    y

rsh = fixnan(rssH)
rsl = fixnan(rssL)
rrh = fixnan(rsrH)
rrl = fixnan(rsrL)
p1 = plot(rsh, style=plot.style_cross, title='Resistance Zone High')
p2 = plot(rsl, style=plot.style_cross, title='Resistance Zone High')
p3 = plot(rrh, style=plot.style_cross, title='Support Zone High')
p4 = plot(rrl, style=plot.style_cross, title='Support Zone Low')
p5 = plot(rH, style=plot.style_cross, title='Demand Below / Supply Above | High')
p6 = plot(rL, style=plot.style_cross, title='Demand Below / Supply Above | Low')
fill(p1, p2, color=color.new(color.green, 100))
fill(p3, p4, color=color.new(color.green, 100))
fill(p5, p6, color=color.new(color.blue, 90))
```