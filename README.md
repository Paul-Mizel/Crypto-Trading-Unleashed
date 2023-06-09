# Crypto-Trading-Unleashed

Crypto Trading Unleashed - Strategies and Tactics for Consistent Profits

![image](https://github.com/Paul-Mizel/Crypto-Trading-Unleashed/blob/main/free-book-cover.jpg?raw=true)

## Indicators 
- [Candle Patterns](#candle-patterns-paulmzl)
- [Golden Cross and Death Cross](#golden-cross-and-death-cross-paulmzl)
- [Heiken-Ashi Candles](#heiken-ashi-candles-paulmzl)

---
### Website: http://www.paulmizel.com
---


### All Candlestick Patterns [paulmzl]

```js
//@version=5
indicator("All Candlestick Patterns [paulmzl]", shorttitle = "PM_ALL_PATTERNS", overlay=true)

C_DownTrend = true
C_UpTrend = true
var trendRule1 = "SMA50"
var trendRule2 = "SMA50, SMA200"
var trendRule3 = "EMA50"
var trendRule4 = "EMA50, EMA200"
var trendRule = input.string(trendRule1, "Detect Trend Based On", options=[trendRule1, trendRule2, trendRule3, trendRule4, "No detection"])

var showTrendlines = input.bool(false, "Show Trend Lines")

var filterByPivots = input.bool(false, "Filter by Pivots")
var showPivots = input.bool(true, "ShowPivots")
var pivotsLength = input.int(21, "Pivots Length")
var waitForClose = input.bool(true, "Wait for Close")



pm_calculatePivots(Shunt) =>
    pvthi_ = ta.pivothigh(high, pivotsLength, pivotsLength)
    pvtlo_ = ta.pivotlow(low, pivotsLength, pivotsLength)

    pvthi = pvthi_[Shunt]
    pvtlo = pvtlo_[Shunt]

    h1 = ta.valuewhen(pvthi, high[pivotsLength + Shunt], 1)
    h0 = ta.valuewhen(pvthi, high[pivotsLength + Shunt], 0)
    l1 = ta.valuewhen(pvtlo, low[pivotsLength + Shunt], 1)
    l0 = ta.valuewhen(pvtlo, low[pivotsLength + Shunt], 0) 
    higherhigh = na(pvthi) ? na : h1 < h0 ? pvthi : na
    lowerhigh = na(pvthi) ? na : h1 > h0 ? pvthi : na
    higherlow = na(pvtlo) ? na :l1 < l0 ? pvtlo : na
    lowerlow = na(pvtlo) ? na : l1 > l0 ? pvtlo : na

    [higherhigh, lowerhigh, higherlow, lowerlow]
shunt = waitForClose ? 1 : 0
[higherhigh, lowerhigh, higherlow, lowerlow] = pm_calculatePivots(shunt) 

var pvthi_bearish = not na(higherhigh) or not na(lowerhigh)
var pvtlo_bullish = not na(higherlow) or not na(lowerlow)

plotshape(showPivots? higherhigh : na, title='HH', style=shape.triangledown, location=location.abovebar, color=color.new(color.green, 25), text='HH', offset=-pivotsLength - shunt, size = size.small)
plotshape(showPivots? higherlow : na, title='HL', style=shape.triangleup, location=location.belowbar, color=color.new(color.red, 0), text='HL', offset=-pivotsLength - shunt, size = size.small)
plotshape(showPivots? lowerhigh : na, title='LH', style=shape.triangledown, location=location.abovebar, color=color.new(color.green, 0), text='LH', offset=-pivotsLength - shunt, size = size.small)
plotshape(showPivots? lowerlow : na, title='LL', style=shape.triangleup, location=location.belowbar, color=color.new(color.red, 25), text='LL', offset=-pivotsLength - shunt, size = size.small)



if trendRule == trendRule1
	priceAvg = ta.sma(close, 50)
	C_DownTrend := close < priceAvg
	C_UpTrend := close > priceAvg

if trendRule == trendRule2
	sma200 = ta.sma(close, 200)
	sma50 = ta.sma(close, 50)
	C_DownTrend := close < sma50 and sma50 < sma200
	C_UpTrend := close > sma50 and sma50 > sma200

if trendRule == trendRule3
	priceAvg = ta.ema(close, 50)
	C_DownTrend := close < priceAvg
	C_UpTrend := close > priceAvg

if trendRule == trendRule4
	ema200 = ta.ema(close, 200)
	ema50 = ta.ema(close, 50)
	C_DownTrend := close < ema50 and ema50 < ema200
	C_UpTrend := close > ema50 and ema50 > ema200

plot((trendRule == trendRule1 or trendRule == trendRule2) and showTrendlines?ta.sma(close, 50):na)
plot((trendRule == trendRule2) and showTrendlines?ta.sma(close, 200):na, color=color.orange)
plot((trendRule == trendRule3 or trendRule == trendRule4) and showTrendlines?ta.ema(close, 50):na)
plot((trendRule == trendRule4) and showTrendlines?ta.ema(close, 200):na, color=color.orange)

C_Len = 14 // ta.ema depth for bodyAvg
C_ShadowPercent = 5.0 // size of shadows
C_ShadowEqualsPercent = 100.0
C_DojiBodyPercent = 5.0
C_Factor = 2.0 // shows the number of times the shadow dominates the candlestick body

C_BodyHi = math.max(close, open)
C_BodyLo = math.min(close, open)
C_Body = C_BodyHi - C_BodyLo
C_BodyAvg = ta.ema(C_Body, C_Len)
C_SmallBody = C_Body < C_BodyAvg
C_LongBody = C_Body > C_BodyAvg
C_UpShadow = high - C_BodyHi
C_DnShadow = C_BodyLo - low
C_HasUpShadow = C_UpShadow > C_ShadowPercent / 100 * C_Body
C_HasDnShadow = C_DnShadow > C_ShadowPercent / 100 * C_Body
C_WhiteBody = open < close
C_BlackBody = open > close
C_Range = high-low
C_IsInsideBar = C_BodyHi[1] > C_BodyHi and C_BodyLo[1] < C_BodyLo
C_BodyMiddle = C_Body / 2 + C_BodyLo
C_ShadowEquals = C_UpShadow == C_DnShadow or (math.abs(C_UpShadow - C_DnShadow) / C_DnShadow * 100) < C_ShadowEqualsPercent and (math.abs(C_DnShadow - C_UpShadow) / C_UpShadow * 100) < C_ShadowEqualsPercent
C_IsDojiBody = C_Range > 0 and C_Body <= C_Range * C_DojiBodyPercent / 100
C_Doji = C_IsDojiBody and C_ShadowEquals

patternLabelPosLow = low - (ta.atr(30) * 0.6)
patternLabelPosHigh = high + (ta.atr(30) * 0.6)

label_color_bullish = input(#26A69A, "Label Color Bullish")
label_color_bearish = input(#FF5252, "Label Color Bearish")
label_color_neutral = input(color.gray, "Label Color Neutral")
CandleType = input.string(title = "Pattern Type", defval="Both", options=["Bullish", "Bearish", "Both"])

AbandonedBabyInput = input(title = "Abandoned Baby" ,defval=true) 

DarkCloudCoverInput = input(title = "Dark Cloud Cover" ,defval=false) 

DojiInput = input(title = "Doji" ,defval=true) 

DojiStarInput = input(title = "Doji Star" ,defval=false) 

DownsideTasukiGapInput = input(title = "Downside Tasuki Gap" ,defval=false) 

DragonflyDojiInput = input(title = "Dragonfly Doji" ,defval=true) 

EngulfingInput = input(title = "Engulfing" ,defval=true) 

EveningDojiStarInput = input(title = "Evening Doji Star" ,defval=false) 

EveningStarInput = input(title = "Evening Star" ,defval=false) 

FallingThreeMethodsInput = input(title = "Falling Three Methods" ,defval=false) 

FallingWindowInput = input(title = "Falling Window" ,defval=false) 

GravestoneDojiInput = input(title = "Gravestone Doji" ,defval=false) 

HammerInput = input(title = "Hammer" ,defval=true) 

HangingManInput = input(title = "Hanging Man" ,defval=false) 

HaramiCrossInput = input(title = "Harami Cross" ,defval=false) 

HaramiInput = input(title = "Harami" ,defval=false) 

InvertedHammerInput = input(title = "Inverted Hammer" ,defval=false) 

KickingInput = input(title = "Kicking" ,defval=false) 

LongLowerShadowInput = input(title = "Long Lower Shadow" ,defval=false) 

LongUpperShadowInput = input(title = "Long Upper Shadow" ,defval=false) 

MarubozuBlackInput = input(title = "Marubozu Black" ,defval=false) 

MarubozuWhiteInput = input(title = "Marubozu White" ,defval=false) 

MorningDojiStarInput = input(title = "Morning Doji Star" ,defval=false) 

MorningStarInput = input(title = "Morning Star" ,defval=false) 

OnNeckInput = input(title = "On Neck" ,defval=false) 

PiercingInput = input(title = "Piercing" ,defval=false) 

RisingThreeMethodsInput = input(title = "Rising Three Methods" ,defval=false) 

RisingWindowInput = input(title = "Rising Window" ,defval=false) 

ShootingStarInput = input(title = "Shooting Star" ,defval=false) 

SpinningTopBlackInput = input(title = "Spinning Top Black" ,defval=false) 

SpinningTopWhiteInput = input(title = "Spinning Top White" ,defval=false) 

ThreeBlackCrowsInput = input(title = "Three Black Crows" ,defval=false) 

ThreeWhiteSoldiersInput = input(title = "Three White Soldiers" ,defval=false) 

TriStarInput = input(title = "Tri-Star" ,defval=false) 

TweezerBottomInput = input(title = "Tweezer Bottom" ,defval=false) 

TweezerTopInput = input(title = "Tweezer Top" ,defval=false) 

UpsideTasukiGapInput = input(title = "Upside Tasuki Gap" ,defval=false) 

C_OnNeckBearishNumberOfCandles = 2
C_OnNeckBearish = false
if C_DownTrend and C_BlackBody[1] and C_LongBody[1] and C_WhiteBody and open < close[1] and C_SmallBody and C_Range!=0 and math.abs(close-low[1])<=C_BodyAvg*0.05
	C_OnNeckBearish := true and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_OnNeckBearish, title = "On Neck – Bearish", message = "New On Neck – Bearish pattern detected")
if C_OnNeckBearish  and  OnNeckInput and (("Bearish" == CandleType) or CandleType == "Both") 
    var ttBearishOnNeck = "On Neck\nOn Neck is a two-line continuation pattern found in a downtrend. The first candle is long and red, the second candle is short and has a green body. The closing price of the second candle is close or equal to the first candle's low price. The pattern hints at a continuation of a downtrend, and penetrating the low of the green candlestick is sometimes considered a confirmation. "
    label.new(bar_index, patternLabelPosHigh, text="ON", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishOnNeck)
C_RisingWindowBullishNumberOfCandles = 2
C_RisingWindowBullish = false
if C_UpTrend[1] and (C_Range!=0 and C_Range[1]!=0) and low > high[1]
	C_RisingWindowBullish := true and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_RisingWindowBullish, title = "Rising Window – Bullish", message = "New Rising Window – Bullish pattern detected")
if C_RisingWindowBullish  and  RisingWindowInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishRisingWindow = "Rising Window\nRising Window is a two-candle bullish continuation pattern that forms during an uptrend. Both candles in the pattern can be of any type with the exception of the Four-Price Doji. The most important characteristic of the pattern is a price gap between the first candle's high and the second candle's low. That gap (window) between two bars signifies support against the selling pressure."
    label.new(bar_index, patternLabelPosLow, text="RW", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishRisingWindow)
C_FallingWindowBearishNumberOfCandles = 2
C_FallingWindowBearish = false
if C_DownTrend[1] and (C_Range!=0 and C_Range[1]!=0) and high < low[1]
	C_FallingWindowBearish := true and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_FallingWindowBearish, title = "Falling Window – Bearish", message = "New Falling Window – Bearish pattern detected")
if C_FallingWindowBearish  and  FallingWindowInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishFallingWindow = "Falling Window\nFalling Window is a two-candle bearish continuation pattern that forms during a downtrend. Both candles in the pattern can be of any type, with the exception of the Four-Price Doji. The most important characteristic of the pattern is a price gap between the first candle's low and the second candle's high. The existence of this gap (window) means that the bearish trend is expected to continue."
    label.new(bar_index, patternLabelPosHigh, text="FW", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishFallingWindow)
C_FallingThreeMethodsBearishNumberOfCandles = 5
C_FallingThreeMethodsBearish = false
if C_DownTrend[4] and (C_LongBody[4] and C_BlackBody[4]) and (C_SmallBody[3] and C_WhiteBody[3] and open[3]>low[4] and close[3]<high[4]) and (C_SmallBody[2] and C_WhiteBody[2] and open[2]>low[4] and close[2]<high[4]) and (C_SmallBody[1] and C_WhiteBody[1] and open[1]>low[4] and close[1]<high[4]) and (C_LongBody and C_BlackBody and close<close[4])
	C_FallingThreeMethodsBearish := true and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_FallingThreeMethodsBearish, title = "Falling Three Methods – Bearish", message = "New Falling Three Methods – Bearish pattern detected")
if C_FallingThreeMethodsBearish  and  FallingThreeMethodsInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishFallingThreeMethods = "Falling Three Methods\nFalling Three Methods is a five-candle bearish pattern that signifies a continuation of an existing downtrend. The first candle is long and red, followed by three short green candles with bodies inside the range of the first candle. The last candle is also red and long and it closes below the close of the first candle. This decisive fifth strongly bearish candle hints that bulls could not reverse the prior downtrend and that bears have regained control of the market."
    label.new(bar_index, patternLabelPosHigh, text="FTM", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishFallingThreeMethods)
C_RisingThreeMethodsBullishNumberOfCandles = 5
C_RisingThreeMethodsBullish = false
if C_UpTrend[4] and (C_LongBody[4] and C_WhiteBody[4]) and (C_SmallBody[3] and C_BlackBody[3] and open[3]<high[4] and close[3]>low[4]) and (C_SmallBody[2] and C_BlackBody[2] and open[2]<high[4] and close[2]>low[4]) and (C_SmallBody[1] and C_BlackBody[1] and open[1]<high[4] and close[1]>low[4]) and (C_LongBody and C_WhiteBody and close>close[4])
	C_RisingThreeMethodsBullish := true and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_RisingThreeMethodsBullish, title = "Rising Three Methods – Bullish", message = "New Rising Three Methods – Bullish pattern detected")
if C_RisingThreeMethodsBullish  and  RisingThreeMethodsInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishRisingThreeMethods = "Rising Three Methods\nRising Three Methods is a five-candle bullish pattern that signifies a continuation of an existing uptrend. The first candle is long and green, followed by three short red candles with bodies inside the range of the first candle. The last candle is also green and long and it closes above the close of the first candle. This decisive fifth strongly bullish candle hints that bears could not reverse the prior uptrend and that bulls have regained control of the market."
    label.new(bar_index, patternLabelPosLow, text="RTM", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishRisingThreeMethods)
C_TweezerTopBearishNumberOfCandles = 2
C_TweezerTopBearish = false
if C_UpTrend[1] and (not C_IsDojiBody or (C_HasUpShadow and C_HasDnShadow)) and math.abs(high-high[1]) <= C_BodyAvg*0.05 and C_WhiteBody[1] and C_BlackBody and C_LongBody[1]
	C_TweezerTopBearish := true and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_TweezerTopBearish, title = "Tweezer Top – Bearish", message = "New Tweezer Top – Bearish pattern detected")
if C_TweezerTopBearish  and  TweezerTopInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishTweezerTop = "Tweezer Top\nTweezer Top is a two-candle pattern that signifies a potential bearish reversal. The pattern is found during an uptrend. The first candle is long and green, the second candle is red, and its high is nearly identical to the high of the previous candle. The virtually identical highs, together with the inverted directions, hint that bears might be taking over the market."
    label.new(bar_index, patternLabelPosHigh, text="TT", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishTweezerTop)
C_TweezerBottomBullishNumberOfCandles = 2
C_TweezerBottomBullish = false
if C_DownTrend[1] and (not C_IsDojiBody or (C_HasUpShadow and C_HasDnShadow)) and math.abs(low-low[1]) <= C_BodyAvg*0.05 and C_BlackBody[1] and C_WhiteBody and C_LongBody[1]
	C_TweezerBottomBullish := true and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_TweezerBottomBullish, title = "Tweezer Bottom – Bullish", message = "New Tweezer Bottom – Bullish pattern detected")
if C_TweezerBottomBullish  and  TweezerBottomInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishTweezerBottom = "Tweezer Bottom\nTweezer Bottom is a two-candle pattern that signifies a potential bullish reversal. The pattern is found during a downtrend. The first candle is long and red, the second candle is green, its lows nearly identical to the low of the previous candle. The virtually identical lows together with the inverted directions hint that bulls might be taking over the market."
    label.new(bar_index, patternLabelPosLow, text="TB", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishTweezerBottom)
C_DarkCloudCoverBearishNumberOfCandles = 2
C_DarkCloudCoverBearish = false
if (C_UpTrend[1] and C_WhiteBody[1] and C_LongBody[1]) and (C_BlackBody and open >= high[1] and  close < C_BodyMiddle[1] and close > open[1])
	C_DarkCloudCoverBearish := true and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_DarkCloudCoverBearish, title = "Dark Cloud Cover – Bearish", message = "New Dark Cloud Cover – Bearish pattern detected")
if C_DarkCloudCoverBearish  and  DarkCloudCoverInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishDarkCloudCover = "Dark Cloud Cover\nDark Cloud Cover is a two-candle bearish reversal candlestick pattern found in an uptrend. The first candle is green and has a larger than average body. The second candle is red and opens above the high of the prior candle, creating a gap, and then closes below the midpoint of the first candle. The pattern shows a possible shift in the momentum from the upside to the downside, indicating that a reversal might happen soon."
    label.new(bar_index, patternLabelPosHigh, text="DCC", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishDarkCloudCover)
C_DownsideTasukiGapBearishNumberOfCandles = 3
C_DownsideTasukiGapBearish = false
if C_LongBody[2] and C_SmallBody[1] and C_DownTrend and C_BlackBody[2] and C_BodyHi[1] < C_BodyLo[2] and C_BlackBody[1] and C_WhiteBody and C_BodyHi <= C_BodyLo[2] and C_BodyHi >= C_BodyHi[1]
	C_DownsideTasukiGapBearish := true   and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_DownsideTasukiGapBearish, title = "Downside Tasuki Gap – Bearish", message = "New Downside Tasuki Gap – Bearish pattern detected")
if C_DownsideTasukiGapBearish  and  DownsideTasukiGapInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishDownsideTasukiGap = "Downside Tasuki Gap\nDownside Tasuki Gap is a three-candle pattern found in a downtrend that usually hints at the continuation of the downtrend. The first candle is long and red, followed by a smaller red candle with its opening price that gaps below the body of the previous candle. The third candle is green and it closes inside the gap created by the first two candles, unable to close it fully. The bull’s inability to close that gap hints that the downtrend might continue."
    label.new(bar_index, patternLabelPosHigh, text="DTG", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishDownsideTasukiGap)
C_UpsideTasukiGapBullishNumberOfCandles = 3
C_UpsideTasukiGapBullish = false
if C_LongBody[2] and C_SmallBody[1] and C_UpTrend and C_WhiteBody[2] and C_BodyLo[1] > C_BodyHi[2] and C_WhiteBody[1] and C_BlackBody and C_BodyLo >= C_BodyHi[2] and C_BodyLo <= C_BodyLo[1]
	C_UpsideTasukiGapBullish := true and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_UpsideTasukiGapBullish, title = "Upside Tasuki Gap – Bullish", message = "New Upside Tasuki Gap – Bullish pattern detected")
if C_UpsideTasukiGapBullish  and  UpsideTasukiGapInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishUpsideTasukiGap = "Upside Tasuki Gap\nUpside Tasuki Gap is a three-candle pattern found in an uptrend that usually hints at the continuation of the uptrend. The first candle is long and green, followed by a smaller green candle with its opening price that gaps above the body of the previous candle. The third candle is red and it closes inside the gap created by the first two candles, unable to close it fully. The bear’s inability to close the gap hints that the uptrend might continue."
    label.new(bar_index, patternLabelPosLow, text="UTG", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishUpsideTasukiGap)
C_EveningDojiStarBearishNumberOfCandles = 3
C_EveningDojiStarBearish = false
if C_LongBody[2] and C_IsDojiBody[1] and C_LongBody and C_UpTrend and C_WhiteBody[2] and C_BodyLo[1] > C_BodyHi[2] and C_BlackBody and C_BodyLo <= C_BodyMiddle[2] and C_BodyLo > C_BodyLo[2] and C_BodyLo[1] > C_BodyHi
	C_EveningDojiStarBearish := true  and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_EveningDojiStarBearish, title = "Evening Doji Star – Bearish", message = "New Evening Doji Star – Bearish pattern detected")
if C_EveningDojiStarBearish  and  EveningDojiStarInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishEveningDojiStar = "Evening Doji Star\nThis candlestick pattern is a variation of the Evening Star pattern. It is bearish and continues an uptrend with a long-bodied, green candle day. It is then followed by a gap and a Doji candle and concludes with a downward close. The close would be below the first day’s midpoint. It is more bearish than the regular evening star pattern because of the existence of the Doji."
    label.new(bar_index, patternLabelPosHigh, text="EDS", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishEveningDojiStar)
C_DojiStarBearishNumberOfCandles = 2
C_DojiStarBearish = false
if C_UpTrend and C_WhiteBody[1] and C_LongBody[1] and C_IsDojiBody and C_BodyLo > C_BodyHi[1]
	C_DojiStarBearish := true  and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_DojiStarBearish, title = "Doji Star – Bearish", message = "New Doji Star – Bearish pattern detected")
if C_DojiStarBearish  and  DojiStarInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishDojiStar = "Doji Star\nThis is a bearish reversal candlestick pattern that is found in an uptrend and consists of two candles. First comes a long green candle, followed by a Doji candle (except 4-Price Doji) that opens above the body of the first one, creating a gap. It is considered a reversal signal with confirmation during the next trading day."
    label.new(bar_index, patternLabelPosHigh, text="DS", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishDojiStar)
C_DojiStarBullishNumberOfCandles = 2
C_DojiStarBullish = false
if C_DownTrend and C_BlackBody[1] and C_LongBody[1] and C_IsDojiBody and C_BodyHi < C_BodyLo[1]
	C_DojiStarBullish := true and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_DojiStarBullish, title = "Doji Star – Bullish", message = "New Doji Star – Bullish pattern detected")
if C_DojiStarBullish  and  DojiStarInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishDojiStar = "Doji Star\nThis is a bullish reversal candlestick pattern that is found in a downtrend and consists of two candles. First comes a long red candle, followed by a Doji candle (except 4-Price Doji) that opens below the body of the first one, creating a gap. It is considered a reversal signal with confirmation during the next trading day."
    label.new(bar_index, patternLabelPosLow, text="DS", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishDojiStar)
C_MorningDojiStarBullishNumberOfCandles = 3
C_MorningDojiStarBullish = false
if C_LongBody[2] and C_IsDojiBody[1] and C_LongBody and C_DownTrend and C_BlackBody[2] and C_BodyHi[1] < C_BodyLo[2] and C_WhiteBody and C_BodyHi >= C_BodyMiddle[2] and C_BodyHi < C_BodyHi[2] and C_BodyHi[1] < C_BodyLo
	C_MorningDojiStarBullish := true and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_MorningDojiStarBullish, title = "Morning Doji Star – Bullish", message = "New Morning Doji Star – Bullish pattern detected")
if C_MorningDojiStarBullish  and  MorningDojiStarInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishMorningDojiStar = "Morning Doji Star\nThis candlestick pattern is a variation of the Morning Star pattern. A three-day bullish reversal pattern, which consists of three candlesticks will look something like this: The first being a long-bodied red candle that extends the current downtrend. Next comes a Doji that gaps down on the open. After that comes a long-bodied green candle, which gaps up on the open and closes above the midpoint of the body of the first day. It is more bullish than the regular morning star pattern because of the existence of the Doji."
    label.new(bar_index, patternLabelPosLow, text="MDS", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishMorningDojiStar)
C_PiercingBullishNumberOfCandles = 2
C_PiercingBullish = false
if (C_DownTrend[1] and C_BlackBody[1] and C_LongBody[1]) and (C_WhiteBody and open <= low[1] and close > C_BodyMiddle[1] and close < open[1])
	C_PiercingBullish := true and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_PiercingBullish, title = "Piercing – Bullish", message = "New Piercing – Bullish pattern detected")
if C_PiercingBullish  and  PiercingInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishPiercing = "Piercing\nPiercing is a two-candle bullish reversal candlestick pattern found in a downtrend. The first candle is red and has a larger than average body. The second candle is green and opens below the low of the prior candle, creating a gap, and then closes above the midpoint of the first candle. The pattern shows a possible shift in the momentum from the downside to the upside, indicating that a reversal might happen soon."
    label.new(bar_index, patternLabelPosLow, text="P", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishPiercing)
C_HammerBullishNumberOfCandles = 1
C_HammerBullish = false
if C_SmallBody and C_Body > 0 and C_BodyLo > hl2 and C_DnShadow >= C_Factor * C_Body and not C_HasUpShadow
    if C_DownTrend
        C_HammerBullish := true and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_HammerBullish, title = "Hammer – Bullish", message = "New Hammer – Bullish pattern detected")
if C_HammerBullish  and  HammerInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishHammer = "Hammer\nHammer candlesticks form when a security moves lower after the open, but continues to rally into close above the intraday low. The candlestick that you are left with will look like a square attached to a long stick-like figure. This candlestick is called a Hammer if it happens to form during a decline."
    label.new(bar_index, patternLabelPosLow, text="H", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishHammer)
C_HangingManBearishNumberOfCandles = 1
C_HangingManBearish = false
if C_SmallBody and C_Body > 0 and C_BodyLo > hl2 and C_DnShadow >= C_Factor * C_Body and not C_HasUpShadow
	if C_UpTrend
	    C_HangingManBearish := true and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_HangingManBearish, title = "Hanging Man – Bearish", message = "New Hanging Man – Bearish pattern detected")
if C_HangingManBearish  and  HangingManInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishHangingMan = "Hanging Man\nWhen a specified security notably moves lower after the open, but continues to rally to close above the intraday low, a Hanging Man candlestick will form. The candlestick will resemble a square, attached to a long stick-like figure. It is referred to as a Hanging Man if the candlestick forms during an advance."
    label.new(bar_index, patternLabelPosHigh, text="HM", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishHangingMan)
C_ShootingStarBearishNumberOfCandles = 1
C_ShootingStarBearish = false
if C_SmallBody and C_Body > 0 and C_BodyHi < hl2 and C_UpShadow >= C_Factor * C_Body and not C_HasDnShadow
	if C_UpTrend
	    C_ShootingStarBearish := true and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_ShootingStarBearish, title = "Shooting Star – Bearish", message = "New Shooting Star – Bearish pattern detected")
if C_ShootingStarBearish  and  ShootingStarInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishShootingStar = "Shooting Star\nThis single day pattern can appear during an uptrend and opens high, while it closes near its open. It trades much higher as well. It is bearish in nature, but looks like an Inverted Hammer."
    label.new(bar_index, patternLabelPosHigh, text="SS", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishShootingStar)
C_InvertedHammerBullishNumberOfCandles = 1
C_InvertedHammerBullish = false
if C_SmallBody and C_Body > 0 and C_BodyHi < hl2 and C_UpShadow >= C_Factor * C_Body and not C_HasDnShadow
    if C_DownTrend
        C_InvertedHammerBullish := true and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_InvertedHammerBullish, title = "Inverted Hammer – Bullish", message = "New Inverted Hammer – Bullish pattern detected")
if C_InvertedHammerBullish  and  InvertedHammerInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishInvertedHammer = "Inverted Hammer\nIf in a downtrend, then the open is lower. When it eventually trades higher, but closes near its open, it will look like an inverted version of the Hammer Candlestick. This is a one-day bullish reversal pattern."
    label.new(bar_index, patternLabelPosLow, text="IH", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishInvertedHammer)
C_MorningStarBullishNumberOfCandles = 3
C_MorningStarBullish = false
if C_LongBody[2] and C_SmallBody[1] and C_LongBody
    if C_DownTrend and C_BlackBody[2] and C_BodyHi[1] < C_BodyLo[2] and C_WhiteBody and C_BodyHi >= C_BodyMiddle[2] and C_BodyHi < C_BodyHi[2] and C_BodyHi[1] < C_BodyLo
        C_MorningStarBullish := true and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_MorningStarBullish, title = "Morning Star – Bullish", message = "New Morning Star – Bullish pattern detected")
if C_MorningStarBullish  and  MorningStarInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishMorningStar = "Morning Star\nA three-day bullish reversal pattern, which consists of three candlesticks will look something like this: The first being a long-bodied red candle that extends the current downtrend. Next comes a short, middle candle that gaps down on the open. After comes a long-bodied green candle, which gaps up on the open and closes above the midpoint of the body of the first day."
    label.new(bar_index, patternLabelPosLow, text="MS", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishMorningStar)
C_EveningStarBearishNumberOfCandles = 3
C_EveningStarBearish = false
if C_LongBody[2] and C_SmallBody[1] and C_LongBody
    if C_UpTrend and C_WhiteBody[2] and C_BodyLo[1] > C_BodyHi[2] and C_BlackBody and C_BodyLo <= C_BodyMiddle[2] and C_BodyLo > C_BodyLo[2] and C_BodyLo[1] > C_BodyHi
        C_EveningStarBearish := true  and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_EveningStarBearish, title = "Evening Star – Bearish", message = "New Evening Star – Bearish pattern detected")
if C_EveningStarBearish  and  EveningStarInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishEveningStar = "Evening Star\nThis candlestick pattern is bearish and continues an uptrend with a long-bodied, green candle day. It is then followed by a gapped and small-bodied candle day, and concludes with a downward close. The close would be below the first day’s midpoint."
    label.new(bar_index, patternLabelPosHigh, text="ES", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishEveningStar)
C_MarubozuWhiteBullishNumberOfCandles = 1
C_MarubozuShadowPercentWhite = 5.0
C_MarubozuWhiteBullish = C_WhiteBody and C_LongBody and C_UpShadow <= C_MarubozuShadowPercentWhite/100*C_Body and C_DnShadow <= C_MarubozuShadowPercentWhite/100*C_Body and C_WhiteBody and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_MarubozuWhiteBullish, title = "Marubozu White – Bullish", message = "New Marubozu White – Bullish pattern detected")
if C_MarubozuWhiteBullish  and  MarubozuWhiteInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishMarubozuWhite = "Marubozu White\nA Marubozu White Candle is a candlestick that does not have a shadow that extends from its candle body at either the open or the close. Marubozu is Japanese for “close-cropped” or “close-cut.” Other sources may call it a Bald or Shaven Head Candle."
    label.new(bar_index, patternLabelPosLow, text="MW", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishMarubozuWhite)
C_MarubozuBlackBearishNumberOfCandles = 1
C_MarubozuShadowPercentBearish = 5.0
C_MarubozuBlackBearish = C_BlackBody and C_LongBody and C_UpShadow <= C_MarubozuShadowPercentBearish/100*C_Body and C_DnShadow <= C_MarubozuShadowPercentBearish/100*C_Body and C_BlackBody and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_MarubozuBlackBearish, title = "Marubozu Black – Bearish", message = "New Marubozu Black – Bearish pattern detected")
if C_MarubozuBlackBearish  and  MarubozuBlackInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishMarubozuBlack = "Marubozu Black\nThis is a candlestick that has no shadow, which extends from the red-bodied candle at the open, the close, or even at both. In Japanese, the name means “close-cropped” or “close-cut.” The candlestick can also be referred to as Bald or Shaven Head."
    label.new(bar_index, patternLabelPosHigh, text="MB", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishMarubozuBlack)
C_DojiNumberOfCandles = 1
C_DragonflyDoji = C_IsDojiBody and C_UpShadow <= C_Body
C_GravestoneDojiOne = C_IsDojiBody and C_DnShadow <= C_Body
alertcondition(C_Doji and not C_DragonflyDoji and not C_GravestoneDojiOne, title = "Doji", message = "New Doji pattern detected")
if C_Doji and not C_DragonflyDoji and not C_GravestoneDojiOne and DojiInput and CandleType == "Both"
    var ttDoji = "Doji\nWhen the open and close of a security are essentially equal to each other, a doji candle forms. The length of both upper and lower shadows may vary, causing the candlestick you are left with to either resemble a cross, an inverted cross, or a plus sign. Doji candles show the playout of buyer-seller indecision in a tug-of-war of sorts. As price moves either above or below the opening level during the session, the close is either at or near the opening level."
    label.new(bar_index, patternLabelPosLow, text="D", style=label.style_label_up, color = label_color_neutral, textcolor=color.white, tooltip = ttDoji)
C_GravestoneDojiBearishNumberOfCandles = 1
C_GravestoneDojiBearish = C_IsDojiBody and C_DnShadow <= C_Body   and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_GravestoneDojiBearish, title = "Gravestone Doji – Bearish", message = "New Gravestone Doji – Bearish pattern detected")
if C_GravestoneDojiBearish  and  GravestoneDojiInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishGravestoneDoji = "Gravestone Doji\nWhen a doji is at or is close to the day’s low point, a doji line will develop."
    label.new(bar_index, patternLabelPosHigh, text="GD", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishGravestoneDoji)
C_DragonflyDojiBullishNumberOfCandles = 1
C_DragonflyDojiBullish = C_IsDojiBody and C_UpShadow <= C_Body and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_DragonflyDojiBullish, title = "Dragonfly Doji – Bullish", message = "New Dragonfly Doji – Bullish pattern detected")
if C_DragonflyDojiBullish  and  DragonflyDojiInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishDragonflyDoji = "Dragonfly Doji\nSimilar to other Doji days, this particular Doji also regularly appears at pivotal market moments. This is a specific Doji where both the open and close price are at the high of a given day."
    label.new(bar_index, patternLabelPosLow, text="DD", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishDragonflyDoji)
C_HaramiCrossBullishNumberOfCandles = 2
C_HaramiCrossBullish = C_LongBody[1] and C_BlackBody[1] and C_DownTrend[1] and C_IsDojiBody and high <= C_BodyHi[1] and low >= C_BodyLo[1] and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_HaramiCrossBullish, title = "Harami Cross – Bullish", message = "New Harami Cross – Bullish pattern detected")
if C_HaramiCrossBullish  and  HaramiCrossInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishHaramiCross = "Harami Cross\nThis candlestick pattern is a variation of the Harami Bullish pattern. It is found during a downtrend. The two-day candlestick pattern consists of a Doji candle that is entirely encompassed within the body of what was once a red-bodied candle."
    label.new(bar_index, patternLabelPosLow, text="HC", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishHaramiCross)
C_HaramiCrossBearishNumberOfCandles = 2
C_HaramiCrossBearish = C_LongBody[1] and C_WhiteBody[1] and C_UpTrend[1] and C_IsDojiBody and high <= C_BodyHi[1] and low >= C_BodyLo[1]   and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_HaramiCrossBearish, title = "Harami Cross – Bearish", message = "New Harami Cross – Bearish pattern detected")
if C_HaramiCrossBearish  and  HaramiCrossInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishHaramiCross = "Harami Cross\nThis candlestick pattern is a variation of the Harami Bearish pattern. It is found during an uptrend. This is a two-day candlestick pattern with a Doji candle that is entirely encompassed within the body that was once a green-bodied candle. The Doji shows that some indecision has entered the minds of sellers, and the pattern hints that the trend might reverse."
    label.new(bar_index, patternLabelPosHigh, text="HC", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishHaramiCross)
C_HaramiBullishNumberOfCandles = 2
C_HaramiBullish = C_LongBody[1] and C_BlackBody[1] and C_DownTrend[1] and C_WhiteBody and C_SmallBody and high <= C_BodyHi[1] and low >= C_BodyLo[1] and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_HaramiBullish, title = "Harami – Bullish", message = "New Harami – Bullish pattern detected")
if C_HaramiBullish  and  HaramiInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishHarami = "Harami\nThis two-day candlestick pattern consists of a small-bodied green candle that is entirely encompassed within the body of what was once a red-bodied candle."
    label.new(bar_index, patternLabelPosLow, text="BH", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishHarami)
C_HaramiBearishNumberOfCandles = 2
C_HaramiBearish = C_LongBody[1] and C_WhiteBody[1] and C_UpTrend[1] and C_BlackBody and C_SmallBody and high <= C_BodyHi[1] and low >= C_BodyLo[1]   and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_HaramiBearish, title = "Harami – Bearish", message = "New Harami – Bearish pattern detected")
if C_HaramiBearish  and  HaramiInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishHarami = "Harami\nThis is a two-day candlestick pattern with a small, red-bodied candle that is entirely encompassed within the body that was once a green-bodied candle."
    label.new(bar_index, patternLabelPosHigh, text="BH", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishHarami)
C_LongLowerShadowBullishNumberOfCandles = 1
C_LongLowerShadowPercent = 75.0
C_LongLowerShadowBullish = C_DnShadow > C_Range/100*C_LongLowerShadowPercent and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_LongLowerShadowBullish, title = "Long Lower Shadow – Bullish", message = "New Long Lower Shadow – Bullish pattern detected")
if C_LongLowerShadowBullish  and  LongLowerShadowInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishLongLowerShadow = "Long Lower Shadow\nTo indicate seller domination of the first part of a session, candlesticks will present with long lower shadows and short upper shadows, consequently lowering prices."
    label.new(bar_index, patternLabelPosLow, text="LLS", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishLongLowerShadow)
C_LongUpperShadowBearishNumberOfCandles = 1
C_LongShadowPercent = 75.0
C_LongUpperShadowBearish = C_UpShadow > C_Range/100*C_LongShadowPercent   and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_LongUpperShadowBearish, title = "Long Upper Shadow – Bearish", message = "New Long Upper Shadow – Bearish pattern detected")
if C_LongUpperShadowBearish  and  LongUpperShadowInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishLongUpperShadow = "Long Upper Shadow\nTo indicate buyer domination of the first part of a session, candlesticks will present with long upper shadows, as well as short lower shadows, consequently raising bidding prices."
    label.new(bar_index, patternLabelPosHigh, text="LUS", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishLongUpperShadow)
C_SpinningTopWhiteNumberOfCandles = 1
C_SpinningTopWhitePercent = 34.0
C_IsSpinningTopWhite = C_DnShadow >= C_Range / 100 * C_SpinningTopWhitePercent and C_UpShadow >= C_Range / 100 * C_SpinningTopWhitePercent and not C_IsDojiBody
C_SpinningTopWhite = C_IsSpinningTopWhite and C_WhiteBody
alertcondition(C_SpinningTopWhite, title = "Spinning Top White", message = "New Spinning Top White pattern detected")
if C_SpinningTopWhite and SpinningTopWhiteInput and CandleType == "Both"
    var ttSpinningTopWhite = "Spinning Top White\nWhite spinning tops are candlestick lines that are small, green-bodied, and possess shadows (upper and lower) that end up exceeding the length of candle bodies. They often signal indecision between buyer and seller."
    label.new(bar_index, patternLabelPosLow, text="STW", style=label.style_label_up, color = label_color_neutral, textcolor=color.white, tooltip = ttSpinningTopWhite)
C_SpinningTopBlackNumberOfCandles = 1
C_SpinningTopBlackPercent = 34.0
C_IsSpinningTop = C_DnShadow >= C_Range / 100 * C_SpinningTopBlackPercent and C_UpShadow >= C_Range / 100 * C_SpinningTopBlackPercent and not C_IsDojiBody
C_SpinningTopBlack = C_IsSpinningTop and C_BlackBody
alertcondition(C_SpinningTopBlack, title = "Spinning Top Black", message = "New Spinning Top Black pattern detected")
if C_SpinningTopBlack and SpinningTopBlackInput and CandleType == "Both"
    var ttSpinningTopBlack = "Spinning Top Black\nBlack spinning tops are candlestick lines that are small, red-bodied, and possess shadows (upper and lower) that end up exceeding the length of candle bodies. They often signal indecision."
    label.new(bar_index, patternLabelPosLow, text="STB", style=label.style_label_up, color = label_color_neutral, textcolor=color.white, tooltip = ttSpinningTopBlack)
C_ThreeWhiteSoldiersBullishNumberOfCandles = 3
C_3WSld_ShadowPercent = 5.0
C_3WSld_HaveNotUpShadow = C_Range * C_3WSld_ShadowPercent / 100 > C_UpShadow
C_ThreeWhiteSoldiersBullish = false
if C_LongBody and C_LongBody[1] and C_LongBody[2]
    if C_WhiteBody and C_WhiteBody[1] and C_WhiteBody[2]
        C_ThreeWhiteSoldiersBullish := close > close[1] and close[1] > close[2] and open < close[1] and open > open[1] and open[1] < close[2] and open[1] > open[2] and C_3WSld_HaveNotUpShadow and C_3WSld_HaveNotUpShadow[1] and C_3WSld_HaveNotUpShadow[2] and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_ThreeWhiteSoldiersBullish, title = "Three White Soldiers – Bullish", message = "New Three White Soldiers – Bullish pattern detected")
if C_ThreeWhiteSoldiersBullish  and  ThreeWhiteSoldiersInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishThreeWhiteSoldiers = "Three White Soldiers\nThis bullish reversal pattern is made up of three long-bodied, green candles in immediate succession. Each one opens within the body before it and the close is near to the daily high."
    label.new(bar_index, patternLabelPosLow, text="3WS", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishThreeWhiteSoldiers)
C_ThreeBlackCrowsBearishNumberOfCandles = 3
C_3BCrw_ShadowPercent = 5.0
C_3BCrw_HaveNotDnShadow = C_Range * C_3BCrw_ShadowPercent / 100 > C_DnShadow
C_ThreeBlackCrowsBearish = false
if C_LongBody and C_LongBody[1] and C_LongBody[2]
    if C_BlackBody and C_BlackBody[1] and C_BlackBody[2]
        C_ThreeBlackCrowsBearish := close < close[1] and close[1] < close[2] and open > close[1] and open < open[1] and open[1] > close[2] and open[1] < open[2] and C_3BCrw_HaveNotDnShadow and C_3BCrw_HaveNotDnShadow[1] and C_3BCrw_HaveNotDnShadow[2]   and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_ThreeBlackCrowsBearish, title = "Three Black Crows – Bearish", message = "New Three Black Crows – Bearish pattern detected")
if C_ThreeBlackCrowsBearish  and  ThreeBlackCrowsInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishThreeBlackCrows = "Three Black Crows\nThis is a bearish reversal pattern that consists of three long, red-bodied candles in immediate succession. For each of these candles, each day opens within the body of the day before and closes either at or near its low."
    label.new(bar_index, patternLabelPosHigh, text="3BC", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishThreeBlackCrows)
C_EngulfingBullishNumberOfCandles = 2
C_EngulfingBullish = C_DownTrend and C_WhiteBody and C_LongBody and C_BlackBody[1] and C_SmallBody[1] and close >= open[1] and open <= close[1] and ( close > open[1] or open < close[1] ) and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_EngulfingBullish, title = "Engulfing – Bullish", message = "New Engulfing – Bullish pattern detected")
if C_EngulfingBullish  and  EngulfingInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishEngulfing = "Engulfing\nAt the end of a given downward trend, there will most likely be a reversal pattern. To distinguish the first day, this candlestick pattern uses a small body, followed by a day where the candle body fully overtakes the body from the day before, and closes in the trend’s opposite direction. Although similar to the outside reversal chart pattern, it is not essential for this pattern to completely overtake the range (high to low), rather only the open and the close."
    label.new(bar_index, patternLabelPosLow, text="BE", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishEngulfing)
C_EngulfingBearishNumberOfCandles = 2
C_EngulfingBearish = C_UpTrend and C_BlackBody and C_LongBody and C_WhiteBody[1] and C_SmallBody[1] and close <= open[1] and open >= close[1] and ( close < open[1] or open > close[1] )  and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_EngulfingBearish, title = "Engulfing – Bearish", message = "New Engulfing – Bearish pattern detected")
if C_EngulfingBearish  and  EngulfingInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishEngulfing = "Engulfing\nAt the end of a given uptrend, a reversal pattern will most likely appear. During the first day, this candlestick pattern uses a small body. It is then followed by a day where the candle body fully overtakes the body from the day before it and closes in the trend’s opposite direction. Although similar to the outside reversal chart pattern, it is not essential for this pattern to fully overtake the range (high to low), rather only the open and the close."
    label.new(bar_index, patternLabelPosHigh, text="BE", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishEngulfing)
C_AbandonedBabyBullishNumberOfCandles = 3
C_AbandonedBabyBullish = C_DownTrend[2] and C_BlackBody[2] and C_IsDojiBody[1] and low[2] > high[1] and C_WhiteBody and high[1] < low and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_AbandonedBabyBullish, title = "Abandoned Baby – Bullish", message = "New Abandoned Baby – Bullish pattern detected")
if C_AbandonedBabyBullish  and  AbandonedBabyInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishAbandonedBaby = "Abandoned Baby\nThis candlestick pattern is quite rare as far as reversal patterns go. The first of the pattern is a large down candle. Next comes a doji candle that gaps below the candle before it. The doji candle is then followed by another candle that opens even higher and swiftly moves to the upside."
    label.new(bar_index, patternLabelPosLow, text="AB", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishAbandonedBaby)
C_AbandonedBabyBearishNumberOfCandles = 3
C_AbandonedBabyBearish = C_UpTrend[2] and C_WhiteBody[2] and C_IsDojiBody[1] and high[2] < low[1] and C_BlackBody and low[1] > high  and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_AbandonedBabyBearish, title = "Abandoned Baby – Bearish", message = "New Abandoned Baby – Bearish pattern detected")
if C_AbandonedBabyBearish  and  AbandonedBabyInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishAbandonedBaby = "Abandoned Baby\nA bearish abandoned baby is a specific candlestick pattern that often signals a downward reversal trend in terms of security price. It is formed when a gap appears between the lowest price of a doji-like candle and the candlestick of the day before. The earlier candlestick is green, tall, and has small shadows. The doji candle is also tailed by a gap between its lowest price point and the highest price point of the candle that comes next, which is red, tall and also has small shadows. The doji candle shadows must completely gap either below or above the shadows of the first and third day in order to have the abandoned baby pattern effect."
    label.new(bar_index, patternLabelPosHigh, text="AB", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishAbandonedBaby)
C_TriStarBullishNumberOfCandles = 3
C_3DojisBullish = C_Doji[2] and C_Doji[1] and C_Doji
C_BodyGapUpBullish = C_BodyHi[1] < C_BodyLo
C_BodyGapDnBullish = C_BodyLo[1] > C_BodyHi
C_TriStarBullish = C_3DojisBullish and C_DownTrend[2] and C_BodyGapDnBullish[1] and C_BodyGapUpBullish and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_TriStarBullish, title = "Tri-Star – Bullish", message = "New Tri-Star – Bullish pattern detected")
if C_TriStarBullish  and  TriStarInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishTriStar = "Tri-Star\nA bullish TriStar candlestick pattern can form when three doji candlesticks materialize in immediate succession at the tail-end of an extended downtrend. The first doji candle marks indecision between bull and bear. The second doji gaps in the direction of the leading trend. The third changes the attitude of the market once the candlestick opens in the direction opposite to the trend. Each doji candle has a shadow, all comparatively shallow, which signify an interim cutback in volatility."
    label.new(bar_index, patternLabelPosLow, text="3S", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishTriStar)
C_TriStarBearishNumberOfCandles = 3
C_3Dojis = C_Doji[2] and C_Doji[1] and C_Doji
C_BodyGapUp = C_BodyHi[1] < C_BodyLo
C_BodyGapDn = C_BodyLo[1] > C_BodyHi
C_TriStarBearish = C_3Dojis and C_UpTrend[2] and C_BodyGapUp[1] and C_BodyGapDn and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_TriStarBearish, title = "Tri-Star – Bearish", message = "New Tri-Star – Bearish pattern detected")
if C_TriStarBearish  and  TriStarInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishTriStar = "Tri-Star\nThis particular pattern can form when three doji candlesticks appear in immediate succession at the end of an extended uptrend. The first doji candle marks indecision between bull and bear. The second doji gaps in the direction of the leading trend. The third changes the attitude of the market once the candlestick opens in the direction opposite to the trend. Each doji candle has a shadow, all comparatively shallow, which signify an interim cutback in volatility."
    label.new(bar_index, patternLabelPosHigh, text="3S", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishTriStar)
C_KickingBullishNumberOfCandles = 2
C_MarubozuShadowPercent = 5.0
C_Marubozu = C_LongBody and C_UpShadow <= C_MarubozuShadowPercent/100*C_Body and C_DnShadow <= C_MarubozuShadowPercent/100*C_Body
C_MarubozuWhiteBullishKicking = C_Marubozu and C_WhiteBody
C_MarubozuBlackBullish = C_Marubozu and C_BlackBody
C_KickingBullish = C_MarubozuBlackBullish[1] and C_MarubozuWhiteBullishKicking and high[1] < low and ((filterByPivots and pvtlo_bullish) or not filterByPivots) 
alertcondition(C_KickingBullish, title = "Kicking – Bullish", message = "New Kicking – Bullish pattern detected")
if C_KickingBullish  and  KickingInput and (("Bullish" == CandleType) or CandleType == "Both")
    var ttBullishKicking = "Kicking\nThe first day candlestick is a bearish marubozu candlestick with next to no upper or lower shadow and where the price opens at the day’s high and closes at the day’s low. The second day is a bullish marubozu pattern, with next to no upper or lower shadow and where the price opens at the day’s low and closes at the day’s high. Additionally, the second day gaps up extensively and opens above the opening price of the day before. This gap or window, as the Japanese call it, lies between day one and day two’s bullish candlesticks."
    label.new(bar_index, patternLabelPosLow, text="K", style=label.style_label_up, color = label_color_bullish, textcolor=color.white, tooltip = ttBullishKicking)
C_KickingBearishNumberOfCandles = 2
C_MarubozuBullishShadowPercent = 5.0
C_MarubozuBearishKicking = C_LongBody and C_UpShadow <= C_MarubozuBullishShadowPercent/100*C_Body and C_DnShadow <= C_MarubozuBullishShadowPercent/100*C_Body
C_MarubozuWhiteBearish = C_MarubozuBearishKicking and C_WhiteBody
C_MarubozuBlackBearishKicking = C_MarubozuBearishKicking and C_BlackBody
C_KickingBearish = C_MarubozuWhiteBearish[1] and C_MarubozuBlackBearishKicking and low[1] > high  and ((filterByPivots and pvthi_bearish) or not filterByPivots)
alertcondition(C_KickingBearish, title = "Kicking – Bearish", message = "New Kicking – Bearish pattern detected")
if C_KickingBearish  and  KickingInput and (("Bearish" == CandleType) or CandleType == "Both")
    var ttBearishKicking = "Kicking\nA bearish kicking pattern will occur, subsequently signaling a reversal for a new downtrend. The first day candlestick is a bullish marubozu. The second day gaps down extensively and opens below the opening price of the day before. There is a gap between day one and two’s bearish candlesticks."
    label.new(bar_index, patternLabelPosHigh, text="K", style=label.style_label_down, color = label_color_bearish, textcolor=color.white, tooltip = ttBearishKicking)
    var ttAllCandlestickPatterns = "All Candlestick Patterns\n"
    label.new(bar_index, patternLabelPosLow, text="Collection", style=label.style_label_up, color = label_color_neutral, textcolor=color.white, tooltip = ttAllCandlestickPatterns)

```


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
---
### Website: http://www.paulmizel.com
---

The code, scripts, indicators, or ideas provided herein are intended for educational purposes only. They are not intended to be used as financial advice or a solicitation to buy or sell any securities. Trading and investing involve risks, and the past performance of any strategy or trading system does not guarantee future results. Users of this code are solely responsible for their investment decisions and should carefully evaluate their financial circumstances, risk tolerance, and objectives before making any trades. The author or provider of the code will not be held liable for any losses or damages, including loss of profit, arising from the use of or reliance on the provided code. It is recommended to seek professional financial advice for personalized guidance.


