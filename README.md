
//@version=5

var int max_bars_back = 3000
indicator('POMPA TARAMA', overlay=true, max_bars_back=max_bars_back)

show_ototrend = input(true,"Otomatik Trend Gösterilsin Mi ?",group = "SEÇENEKLER")
show_rsz = input(true,"Destek Direnç Bölgeleri Gösterilsin Mi ?",group = "SEÇENEKLER")
show_pivot = input(true,"Pivotlar Gösterilsin Mi?",group = "SEÇENEKLER")
moving_ok = input(false,"Hareketli Ortalamalar Gösterilsin Mi ?",group = "SEÇENEKLER")
show_fibo = input(false,"Fibo Gösterilsin Mi ?",group = "SEÇENEKLER")
show_ttfibo = input(false,"Trend Temelli Fibo Gösterilsin Mi ?",group = "SEÇENEKLER")


//////////////////////////////////////////////////////////////////////////////////////////////
//━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  HAREKETLİ ORTALAMALAR  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
//////////////////////////////////////////////////////////////////////////////////////////////

//Source
src = input(close, title='Source',group = "HAREKETLİ ORTALAMALAR")

//Moving Averages Period
res = input.timeframe(title='Resolution', defval='',group = "HAREKETLİ ORTALAMALAR")

//mtf source
_close = request.security(syminfo.tickerid, res, src)

//MA1 settings
ma1_active = input.bool(true, title='MA1', inline='MA1',group = "HAREKETLİ ORTALAMALAR")
ma1_length = input.int(13, title='Length', inline='MA1',group = "HAREKETLİ ORTALAMALAR")
MAType_1 = input.string(defval='EMA', title='Type', options=['SMA', 'EMA', 'WMA', 'VWMA', 'SMMA', 'DEMA', 'TEMA', 'HullMA', 'EHMA', 'ETMA', 'ZEMA', 'TMA', 'SSMA', 'STMA', 'ALMA'], inline='MA1',group = "HAREKETLİ ORTALAMALAR")
MAType_1_col = input.color(color.rgb(0, 230, 118),"", inline='MA1', group="HAREKETLİ ORTALAMALAR")

//MA2 settings
ma2_active = input.bool(true, title='MA2', inline='MA2',group = "HAREKETLİ ORTALAMALAR")
ma2_length = input.int(21, title='Length', inline='MA2',group = "HAREKETLİ ORTALAMALAR")
MAType_2 = input.string(defval='EMA', title='Type', options=['SMA', 'EMA', 'WMA', 'VWMA', 'SMMA', 'DEMA', 'TEMA', 'HullMA', 'EHMA', 'ETMA', 'ZEMA', 'TMA', 'SSMA', 'STMA', 'ALMA'], inline='MA2',group = "HAREKETLİ ORTALAMALAR")
MAType_2_col = input.color(color.rgb(33, 150, 243),"", inline='MA2', group="HAREKETLİ ORTALAMALAR")

//MA3 settings
ma3_active = input.bool(true, title='MA3', inline='MA3',group = "HAREKETLİ ORTALAMALAR")
ma3_length = input.int(55, title='Length', inline='MA3',group = "HAREKETLİ ORTALAMALAR")
MAType_3 = input.string(defval='EMA', title='Type', options=['SMA', 'EMA', 'WMA', 'VWMA', 'SMMA', 'DEMA', 'TEMA', 'HullMA', 'EHMA', 'ETMA', 'ZEMA', 'TMA', 'SSMA', 'STMA', 'ALMA'], inline='MA3',group = "HAREKETLİ ORTALAMALAR")
MAType_3_col = input.color(color.rgb(255, 82, 82),"", inline='MA3', group="HAREKETLİ ORTALAMALAR")

//MA4 settings
ma4_active = input.bool(true, title='MA4', inline='MA4',group = "HAREKETLİ ORTALAMALAR")
ma4_length = input.int(89, title='Length', inline='MA4',group = "HAREKETLİ ORTALAMALAR")
MAType_4 = input.string(defval='EMA', title='Type', options=['SMA', 'EMA', 'WMA', 'VWMA', 'SMMA', 'DEMA', 'TEMA', 'HullMA', 'EHMA', 'ETMA', 'ZEMA', 'TMA', 'SSMA', 'STMA', 'ALMA'], inline='MA4',group = "HAREKETLİ ORTALAMALAR")
MAType_4_col = input.color(color.rgb(255, 152, 0),"", inline='MA4', group="HAREKETLİ ORTALAMALAR")

//MA5 settings
ma5_active = input.bool(true, title='MA5', inline='MA5',group = "HAREKETLİ ORTALAMALAR")
ma5_length = input.int(144, title='Length', inline='MA5',group = "HAREKETLİ ORTALAMALAR")
MAType_5 = input.string(defval='EMA', title='Type', options=['SMA', 'EMA', 'WMA', 'VWMA', 'SMMA', 'DEMA', 'TEMA', 'HullMA', 'EHMA', 'ETMA', 'ZEMA', 'TMA', 'SSMA', 'STMA', 'ALMA'], inline='MA5',group = "HAREKETLİ ORTALAMALAR")
MAType_5_col = input.color(color.rgb(76, 175, 80),"", inline='MA5', group="HAREKETLİ ORTALAMALAR")

//MA6 settings
ma6_active = input.bool(true, title='MA6', inline='MA6',group = "HAREKETLİ ORTALAMALAR")
ma6_length = input.int(233, title='Length', inline='MA6',group = "HAREKETLİ ORTALAMALAR")
MAType_6 = input.string(defval='EMA', title='Type', options=['SMA', 'EMA', 'WMA', 'VWMA', 'SMMA', 'DEMA', 'TEMA', 'HullMA', 'EHMA', 'ETMA', 'ZEMA', 'TMA', 'SSMA', 'STMA', 'ALMA'], inline='MA6',group = "HAREKETLİ ORTALAMALAR")
MAType_6_col = input.color(color.rgb(156, 39, 176),"", inline='MA6', group="HAREKETLİ ORTALAMALAR")

//gamma değeri
gamma = 0.55

//ssma
variant_supersmoother(src, MovAv) =>
    a1 = math.exp(-1.414 * 3.14159 / MovAv)
    b1 = 2 * a1 * math.cos(1.414 * 3.14159 / MovAv)
    c2 = b1
    c3 = -a1 * a1
    c1 = 1 - c2 - c3
    v9 = 0.0
    v9 := c1 * (src + nz(src[1])) / 2 + c2 * nz(v9[1]) + c3 * nz(v9[2])
    v9

//smma
variant_smoothed(src, MovAv) =>
    _sma = ta.sma(src, MovAv)
    v5 = 0.0
    v5 := na(v5[1]) ? _sma : (v5[1] * (MovAv - 1) + src) / MovAv
    v5

//zema
variant_zerolagema(src, MovAv) =>
    ema1 = ta.ema(src, MovAv)
    ema2 = ta.ema(ema1, MovAv)
    v10 = ema1 + ema1 - ema2
    v10

//dema
variant_doubleema(src, MovAv) =>
    v2 = ta.ema(src, MovAv)
    v6 = 2 * v2 - ta.ema(v2, MovAv)
    v6

//tema
variant_tripleema(src, MovAv) =>
    v2 = ta.ema(src, MovAv)
    v7 = 3 * (v2 - ta.ema(v2, MovAv)) + ta.ema(ta.ema(v2, MovAv), MovAv)
    v7

//Laguerre
variant_lag(p, g) =>
    L0 = 0.0
    L1 = 0.0
    L2 = 0.0
    L3 = 0.0
    L0 := (1 - g) * p + g * nz(L0[1])
    L1 := -g * L0 + nz(L0[1]) + g * nz(L1[1])
    L2 := -g * L1 + nz(L1[1]) + g * nz(L2[1])
    L3 := -g * L2 + nz(L2[1]) + g * nz(L3[1])
    f = (L0 + 2 * L1 + 2 * L2 + L3) / 6
    f

//alma
variant_alma(src, MovAv) =>
    var float _offset = 0.85
    var float _sigma = 6.0
    ta.alma(src, MovAv, _offset, _sigma)

//vidya
variant_vidya(src, MovAv) =>
    _cmo = ta.cmo(src, MovAv) / 100  //Change Momentum Oscillator
    var _factor = 2 / (MovAv + 1)
    var _vidya = 0.0
    _vidya := src * _factor * math.abs(_cmo) + nz(_vidya[1]) * (1 - _factor * math.abs(_cmo))
    _vidya

//return variant, defaults to SMA 
variant(type, src, MovAv, g) =>
    type == 'EMA' ? ta.ema(src, MovAv) : type == 'WMA' ? ta.wma(src, MovAv) : type == 'VWMA' ? ta.vwma(src, MovAv) : type == 'SMMA' ? variant_smoothed(src, MovAv) : type == 'DEMA' ? variant_doubleema(src, MovAv) : type == 'TEMA' ? variant_tripleema(src, MovAv) : type == 'LAGMA' ? variant_lag(src, g) : type == 'HullMA' ? ta.wma(2 * ta.wma(src, MovAv / 2) - ta.wma(src, MovAv), math.round(math.sqrt(MovAv))) : type == 'EHMA' ? ta.ema(2 * ta.ema(src, MovAv / 2) - ta.ema(src, MovAv), math.round(math.sqrt(MovAv))) : type == 'ETMA' ? ta.ema(ta.ema(src, MovAv), MovAv) : type == 'SSMA' ? variant_supersmoother(src, MovAv) : type == 'ALMA' ? variant_alma(src, MovAv) : type == 'VIDYA' ? variant_vidya(src, MovAv) : type == 'STMA' ? ta.sma(ta.sma(src, MovAv), MovAv) : type == 'ZEMA' ? variant_zerolagema(src, MovAv) : type == 'TMA' ? ta.sma(ta.sma(src, MovAv), MovAv) : ta.sma(src, MovAv)

//assign functions
mov_1 = variant(MAType_1, _close, ma1_length, gamma)
mov_2 = variant(MAType_2, _close, ma2_length, gamma)
mov_3 = variant(MAType_3, _close, ma3_length, gamma)
mov_4 = variant(MAType_4, _close, ma4_length, gamma)
mov_5 = variant(MAType_5, _close, ma5_length, gamma)
mov_6 = variant(MAType_6, _close, ma6_length, gamma)

//moving averages plots
ma1 = plot(moving_ok and ma1_active ? mov_1 : na, title='Moving Average - 1', color=MAType_1_col, linewidth=3,editable = false)
ma2 = plot(moving_ok and ma2_active ? mov_2 : na, title='Moving Average - 2', color=MAType_2_col, linewidth=3,editable = false)
ma3 = plot(moving_ok and ma3_active ? mov_3 : na, title='Moving Average - 3', color=MAType_3_col, linewidth=3,editable = false)
ma4 = plot(moving_ok and ma4_active ? mov_4 : na, title='Moving Average - 4', color=MAType_4_col, linewidth=3,editable = false)
ma5 = plot(moving_ok and ma5_active ? mov_5 : na, title='Moving Average - 5', color=MAType_5_col, linewidth=3,editable = false)
ma6 = plot(moving_ok and ma6_active ? mov_6 : na, title='Moving Average - 6', color=MAType_6_col, linewidth=3,editable = false)

///////////////////////////////////////////////////////////////////////////////////////
//━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  OTOMATİK TREND  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
///////////////////////////////////////////////////////////////////////////////////////

// -- OTOMATİK TREND ŞEKİL AYARLARI
var int history_bars = input(title='History bars back', defval=300,group = "OTOMATİK TREND")
x1 = input(title='Resolution (bars)', defval=6,group = "OTOMATİK TREND")
line_col_res = input.color(color.rgb(240, 84, 84, 5), ' Line Rengi', inline='otodirenç', group='OTOMATİK TREND')
label_col_res = input.color(color.rgb(26, 202, 20), ' Label Rengi', inline='otodirenç', group='OTOMATİK TREND')
label_text_col_res = input.color(color.rgb(0, 0, 0, 0), ' Yazı Rengi', inline='otodirenç', group='OTOMATİK TREND')

line_col_sup = input.color(color.rgb(23, 255, 39, 75), ' Line Rengi', inline='otodestek', group='OTOMATİK TREND')
label_col_sup = input.color(color.rgb(23, 255, 39, 75), ' Label Rengi', inline='otodestek', group='OTOMATİK TREND')
label_text_col_sup = input.color(color.rgb(0, 0, 0, 0), ' Yazı Rengi', inline='otodestek', group='OTOMATİK TREND')

// -- OTOMATİK TREND STİL AYARLARI

line_style0 = 'Solid'
line_style1 = 'Dotted'
line_style2 = 'Dashed'

Oto_Resist_Style = input.string(line_style0, ' OtoTrendDirenç', inline='otolineres', group='OTOMATİK TREND', options=[line_style0, line_style1, line_style2])
Oto_Support_Style = input.string(line_style0, ' OtoTrendDestek', inline='otolinesup', group='OTOMATİK TREND', options=[line_style0, line_style1, line_style2])

style_res = Oto_Resist_Style == line_style1 ? line.style_dotted : Oto_Resist_Style == line_style2 ? line.style_dashed : line.style_solid
style_sup = Oto_Support_Style == line_style1 ? line.style_dotted : Oto_Support_Style == line_style2 ? line.style_dashed : line.style_solid

line_width_res = input.int(defval=2, step=1, title = ' Kalınlık', inline='otolineres', group='OTOMATİK TREND')
line_width_sup = input.int(defval=2, step=1, title = ' Kalınlık' , inline='otolinesup', group='OTOMATİK TREND')

// © pikusov
// Diagonal Supports and Resistances
//
// Classic diagonal support and resistance based on pivot points.
// As a result, they form triangles, wedges, channels and other patterns.
// Realtime update up to 1 second chart.
// 
// Input parameters:
// History Bars Back: Number of bars back to find high and low points
// Please note that History Bars Back more than 300 may cause errors
// Resolution: The power of high and low points to take into drawing
//
// You can enable alerts for crossing supports and resistances in the Alerts menu
// https://www.tradingview.com/support/solutions/43000595315-how-to-set-up-alerts

// Функция вычисляет цену в точке t3 для линии,
// заданной первыми четырьмя координатами (t1, p1, t2, p2)
price_at(t1, p1, t2, p2, t3) =>
    p1 + (p2 - p1) * (t3 - t1) / (t2 - t1)

// Alerts
if 1 == 1
    alert('test')
// округление
round_to_tick(x) =>
    mult = 1 / syminfo.mintick
    value = math.ceil(x * mult) / mult
    value

// Тут храним линии для удаления при появлении нового бара
var line[] supports = array.new_line()
var line[] resistances = array.new_line()
var label[] labels = array.new_label()

fire_alert_sup = false
fire_alert_res = false
fire_alert_sup := false
fire_alert_res := false
// Удаляем прошлые линии и заодно вызываем алерты
line temp_line = na
if array.size(supports) > 0
    for i = array.size(supports) - 1 to 0 by 1
        temp_line := array.get(supports, i)
        if low[1] > line.get_price(temp_line, bar_index - 1) and close < line.get_price(temp_line, bar_index)
            fire_alert_sup := true
            fire_alert_sup
        line.delete(temp_line)
        array.remove(supports, i)
if array.size(resistances) > 0
    for i = array.size(resistances) - 1 to 0 by 1
        temp_line := array.get(resistances, i)
        if high[1] < line.get_price(temp_line, bar_index - 1) and close > line.get_price(temp_line, bar_index)
            fire_alert_res := true
            fire_alert_res
        line.delete(temp_line)
        array.remove(resistances, i)
label temp_label = na
if array.size(labels) > 0
    for i = array.size(labels) - 1 to 0 by 1
        temp_label := array.get(labels, i)
        label.delete(temp_label)
        array.remove(labels, i)


alertcondition(fire_alert_sup, 'Diagonal Support Alert', 'Diagonal support crossed down')
alertcondition(fire_alert_res, 'Diagonal Resistance Alert', 'Diagonal resistance crossed up')

// Определяем экстремумы
min_values = low
max_values = high
//x1 = input(title='Resolution (bars)', defval=6,group = "OTOMATİK TREND")
x2 = math.round(x1 / 2)
int minimums = 0
minimums := ta.lowestbars(min_values, x1) == -x2 ? x2 : minimums[1] + 1

int maximums = 0
maximums := ta.highestbars(max_values, x1) == -x2 ? x2 : maximums[1] + 1


int minimum1 = 0
int minimum2 = 0
int maximum1 = 0
int maximum2 = 0
int medium = 0
// Поддержка     
if barstate.islast
    //label.new(bar_index, close , style=label.style_labeldown, text=timeframe.period, color=color.new(color.red, 90))
    line last_line = na
    label last_label = na
    for k1 = 0 to 50 by 1
        if minimum1 >= history_bars
            break
        minimum1 += minimums[minimum1]
        minimum2 := minimum1 * 2
        for k2 = 0 to 50 by 1
            if minimum2 >= minimum1 * 8 or minimum2 >= history_bars
                break
            minimum2 += minimums[minimum2]

            if minimum1 >= history_bars or minimum2 >= history_bars
                break

            bar1 = bar_index - minimum1
            bar2 = bar_index - minimum2

            price1 = low[minimum1]
            price2 = low[minimum2]

            current_price = price_at(bar2, price2, bar1, price1, bar_index)
            // Если поддержка проходит ниже текущей цены
            if current_price < high[1]

                // проверяем пересечения
                crossed = 0
                medium := 0
                for k3 = 0 to 50 by 1
                    if medium >= minimum2
                        break
                    medium += minimums[medium]
                    if medium >= minimum2
                        break
                    if price_at(bar2, price2, bar1, price1, bar_index - medium) > math.min(open[medium], close[medium])
                        crossed := 1
                        break

                // если нет пересечений        
                if crossed == 0  // and overtilt == 0
                    // сравниваем с прошлой созданной линией
                    if not na(last_line)
                        last_price = price_at(line.get_x1(last_line), line.get_y1(last_line), line.get_x2(last_line), line.get_y2(last_line), bar_index)
                        if bar1 == line.get_x2(last_line)
                            if current_price > last_price
                                line.set_xy1(last_line, bar2, price2)
                                line.set_xy2(last_line, bar1, price1)
                                line.set_color(last_line, line_col_sup)
                                label.set_xy(last_label, bar_index, current_price)
                                label.set_text(last_label, str.tostring(round_to_tick(current_price)))
                                true
                        else
                            last_line := if (show_ototrend == 1)  
                                line.new(bar2, price2, bar1, price1, extend=extend.right, color=line_col_sup, style=style_sup,width = line_width_sup)
                            last_label := if (show_ototrend == 1) 
                                label.new(bar_index, current_price, color=label_col_sup, style=label.style_label_upper_left, text=str.tostring(round_to_tick(current_price)),textcolor = label_text_col_sup)
                            array.push(labels, last_label)
                            array.push(supports, last_line)
                            true
                    else
                    // добавляем линию
                        last_line := if (show_ototrend == 1) 
                            line.new(bar2, price2, bar1, price1, extend=extend.right, color=line_col_sup, style=style_sup,width = line_width_sup)
                        last_label := if (show_ototrend == 1) 
                            label.new(bar_index, current_price, color=label_col_sup, style=label.style_label_upper_left, text=str.tostring(round_to_tick(current_price)),textcolor = label_text_col_sup)
                        array.push(labels, last_label)
                        array.push(supports, last_line)
                        true

    last_line := na
    last_label := na
    for k1 = 0 to 100 by 1
        if maximum1 >= history_bars
            break
        maximum1 += maximums[maximum1]
        maximum2 := maximum1 * 2
        for k2 = 0 to 50 by 1
            if maximum2 >= maximum1 * 8 or maximum2 >= history_bars
                break
            maximum2 += maximums[maximum2]

            if maximum1 >= history_bars or maximum2 >= history_bars
                break

            bar1 = bar_index - maximum1
            bar2 = bar_index - maximum2

            price1 = high[maximum1]
            price2 = high[maximum2]

            current_price = price_at(bar2, price2, bar1, price1, bar_index)
            // Если сопротивоение проходит выше текущей цены
            if current_price > low[1]

                // проверяем пересечения
                crossed = 0
                medium := 0
                for k3 = 0 to 100 by 1
                    if medium >= maximum2
                        break
                    medium += maximums[medium]
                    if medium >= maximum2
                        break
                    if price_at(bar2, price2, bar1, price1, bar_index - medium) < math.max(open[medium], close[medium])
                        crossed := 1
                        break

                // если нет пересечений        
                if crossed == 0  // and overtilt == 0
                    // сравниваем с прошлой созданной линией
                    if not na(last_line)
                        last_price = price_at(line.get_x1(last_line), line.get_y1(last_line), line.get_x2(last_line), line.get_y2(last_line), bar_index)
                        if bar1 == line.get_x2(last_line)
                            if current_price < last_price
                                line.set_xy1(last_line, bar2, price2)
                                line.set_xy2(last_line, bar1, price1)
                                line.set_color(last_line, line_col_res)
                                label.set_xy(last_label, bar_index, current_price)
                                label.set_text(last_label, str.tostring(round_to_tick(current_price)))
                                true
                        else
                            last_line := if (show_ototrend == 1)  
                                line.new(bar2, price2, bar1, price1, extend=extend.right, color=line_col_res, style=style_res,width = line_width_res)
                            last_label := if (show_ototrend == 1) 
                                label.new(bar_index, current_price, color=label_col_res, style=label.style_label_lower_left, text=str.tostring(round_to_tick(current_price)),textcolor = label_text_col_res)
                            array.push(labels, last_label)
                            array.push(resistances, last_line)
                            true
                    else
                    // добавляем линию
                        last_line := if (show_ototrend == 1)  
                            line.new(bar2, price2, bar1, price1, extend=extend.right, color=line_col_res, style=style_res,width = line_width_res)
                        last_label := if (show_ototrend == 1) 
                            label.new(bar_index, current_price, color=label_col_res, style=label.style_label_lower_left, text=str.tostring(round_to_tick(current_price)),textcolor = label_text_col_res)
                        array.push(labels, last_label)
                        array.push(resistances, last_line)
                        true

/////////////////////////////////////////////////////////////////////////////////////////////////
//━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  OTO FİBONACCİ DÜZELTMESİ  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
/////////////////////////////////////////////////////////////////////////////////////////////////

devTooltip = "Deviation is a multiplier that affects how much the price should deviate from the previous pivot in order for the bar to become a new pivot."
depthTooltip = "The minimum number of bars that will be taken into account when calculating the indicator."
// pivots threshold
threshold_multiplier = input.float(title="Deviation", defval=3, minval=0, tooltip=devTooltip,group = "FİBONACCİ DÜZELTMESİ")
dev_threshold = ta.atr(10) / close * 100 * threshold_multiplier
depth = input.int(title="Depth", defval=10, minval=1, tooltip=depthTooltip,group = "FİBONACCİ DÜZELTMESİ")
reverse = input(false, "Reverse",group = "FİBONACCİ DÜZELTMESİ")
var extendLeft = input(false, "Extend Left    |    Extend Right", inline = "Extend Lines",group = "FİBONACCİ DÜZELTMESİ")
var extendRight = input(true, "", inline = "Extend Lines",group = "FİBONACCİ DÜZELTMESİ")
var extending = extend.none
if extendLeft and extendRight
    extending := extend.both
if extendLeft and not extendRight
    extending := extend.left
if not extendLeft and extendRight
    extending := extend.right
prices = input(true, "Show Prices",group = "FİBONACCİ DÜZELTMESİ")
levels = input(true, "Show Levels", inline = "Levels",group = "FİBONACCİ DÜZELTMESİ")
levelsFormat = input.string("Values", "", options = ["Values", "Percent"], inline = "Levels",group = "FİBONACCİ DÜZELTMESİ")
labelsPosition = input.string("Right", "Labels Position", options = ["Left", "Right"],group = "FİBONACCİ DÜZELTMESİ")
var int backgroundTransparency = input.int(100, "Background Transparency", minval = 0, maxval = 100,group = "FİBONACCİ DÜZELTMESİ")

var line lineLast = na
var int iLast = 0
var int iPrev = 0
var float pLast = 0
var isHighLast = false // otherwise the last pivot is a low pivot

pivots(src, length, isHigh) =>
    l2 = length * 2
    c = nz(src[length])
    ok = true
    for i = 0 to l2
        if isHigh and src[i] > c
            ok := false

        if not isHigh and src[i] < c
            ok := false
    if ok
        [bar_index[length], c]
    else
        [int(na), float(na)]
[iH, pH] = pivots(high, depth / 2, true)
[iL, pL] = pivots(low, depth / 2, false)

calc_dev(base_price, price) =>
    100 * (price - base_price) / price

pivotFound(dev, isHigh, index, price) =>
    if isHighLast == isHigh and not na(lineLast)
        // same direction
        if isHighLast ? price > pLast : price < pLast
            line.set_xy2(lineLast, index, price)
            [lineLast, isHighLast]
        else
            [line(na), bool(na)]
    else // reverse the direction (or create the very first line)
        if math.abs(dev) > dev_threshold
            // price move is significant
            id = line.new(iLast, pLast, index, price, color=color.rgb(120, 123, 134, 100), width=1, style=line.style_dashed)
            [id, isHigh]
        else
            [line(na), bool(na)]

if not na(iH)
    dev = calc_dev(pLast, pH)
    [id, isHigh] = pivotFound(dev, true, iH, pH)
    if not na(id)
        if id != lineLast
            line.delete(lineLast)
        lineLast := id
        isHighLast := isHigh
        iPrev := iLast
        iLast := iH
        pLast := pH
else
    if not na(iL)
        dev = calc_dev(pLast, pL)
        [id, isHigh] = pivotFound(dev, false, iL, pL)
        if not na(id)
            if id != lineLast
                line.delete(lineLast)
            lineLast := id
            isHighLast := isHigh
            iPrev := iLast
            iLast := iL
            pLast := pL

_draw_line(price, col) =>
    var id = if show_fibo ==1 
        line.new(iLast, price, bar_index, price, color=col, width=1, extend=extending,style = line.style_dashed)
    if not na(lineLast)
        line.set_xy1(id, line.get_x1(lineLast), price)
        line.set_xy2(id, line.get_x2(lineLast), price)  
	id  


_draw_label(price, txt, txtColor) =>
    x = labelsPosition == "Left" ? line.get_x1(lineLast) : not extendRight ? line.get_x2(lineLast) : bar_index
    labelStyle = labelsPosition == "Left" ? label.style_label_right : label.style_label_left
    align = labelsPosition == "Left" ? text.align_right : text.align_right
    labelsAlignStrLeft = txt + '\n ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏ \n'
    labelsAlignStrRight = '       ' + txt + '\n ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏ \n'
    labelsAlignStr = labelsPosition == "Left" ? labelsAlignStrLeft : labelsAlignStrRight
    var id = if show_fibo ==1 
        label.new(x=x, y=price, text=labelsAlignStr, textcolor=txtColor, style=labelStyle, textalign=align, color=color.rgb(0, 0, 0, 100),size = size.small)
    label.set_xy(id, x, price)
    label.set_text(id, labelsAlignStr)
    label.set_textcolor(id, txtColor)

_wrap(txt) =>
    "(" + str.tostring(txt, format.mintick) + ")"

_label_txt(level, price) =>
    l = levelsFormat == "Values" ? str.tostring(level) : str.tostring(level * 100) + "%"
    (levels ? l : "") + (prices ? _wrap(price) : "")

_crossing_level(sr, r) =>
    (r > sr and r < sr[1]) or (r < sr and r > sr[1])

startPrice = reverse ? line.get_y1(lineLast) : pLast
endPrice = reverse ? pLast : line.get_y1(lineLast)

iHL = startPrice > endPrice
diff = (iHL ? -1 : 1) * math.abs(startPrice - endPrice)

processLevel(show, value, colorL, lineIdOther) =>
    float m = value
	r = startPrice + diff * m
    if show
		lineId = _draw_line(r, colorL)
        _draw_label(r, _label_txt(m, r), colorL)
        if _crossing_level(close, r)
            alert("Autofib: " + syminfo.ticker + " crossing level " + str.tostring(value))
        if not na(lineIdOther)
            linefill.new(lineId, lineIdOther, color = color.new(colorL, backgroundTransparency))
		lineId
    else
		lineIdOther

show_0 = input(true, "", inline = "Level0",group = "FİBONACCİ DÜZELTMESİ")
value_0 = input(0, "", inline = "Level0",group = "FİBONACCİ DÜZELTMESİ")
color_0 = input(#ff0000, "", inline = "Level0",group = "FİBONACCİ DÜZELTMESİ")

show_0_236 = input(true, "", inline = "Level0",group = "FİBONACCİ DÜZELTMESİ")
value_0_236 = input(0.236, "", inline = "Level0",group = "FİBONACCİ DÜZELTMESİ")
color_0_236 = input(#ff0000, "", inline = "Level0",group = "FİBONACCİ DÜZELTMESİ")

show_0_382 = input(true, "", inline = "Level1",group = "FİBONACCİ DÜZELTMESİ")
value_0_382 = input(0.382, "", inline = "Level1",group = "FİBONACCİ DÜZELTMESİ")
color_0_382 = input(#ff0000, "", inline = "Level1",group = "FİBONACCİ DÜZELTMESİ")

show_0_5 = input(true, "", inline = "Level1",group = "FİBONACCİ DÜZELTMESİ")
value_0_5 = input(0.5, "", inline = "Level1",group = "FİBONACCİ DÜZELTMESİ")
color_0_5 = input(#ff0000, "", inline = "Level1",group = "FİBONACCİ DÜZELTMESİ")

show_0_618 = input(true, "", inline = "Level2",group = "FİBONACCİ DÜZELTMESİ")
value_0_618 = input(0.618, "", inline = "Level2",group = "FİBONACCİ DÜZELTMESİ")
color_0_618 = input(#ff0000, "", inline = "Level2",group = "FİBONACCİ DÜZELTMESİ")

show_0_65 = input(false, "", inline = "Level2",group = "FİBONACCİ DÜZELTMESİ")
value_0_65 = input(0.65, "", inline = "Level2",group = "FİBONACCİ DÜZELTMESİ")
color_0_65 = input(#ff0000, "", inline = "Level2",group = "FİBONACCİ DÜZELTMESİ")

show_0_786 = input(true, "", inline = "Level3",group = "FİBONACCİ DÜZELTMESİ")
value_0_786 = input(0.786, "", inline = "Level3",group = "FİBONACCİ DÜZELTMESİ")
color_0_786 = input(#ff0000, "", inline = "Level3",group = "FİBONACCİ DÜZELTMESİ")

show_1 = input(true, "", inline = "Level3",group = "FİBONACCİ DÜZELTMESİ")
value_1 = input(1, "", inline = "Level3",group = "FİBONACCİ DÜZELTMESİ")
color_1 = input(#ff0000, "", inline = "Level3",group = "FİBONACCİ DÜZELTMESİ")

show_1_272 = input(false, "", inline = "Level4",group = "FİBONACCİ DÜZELTMESİ")
value_1_272 = input(1.272, "", inline = "Level4",group = "FİBONACCİ DÜZELTMESİ")
color_1_272 = input(#ff0000, "", inline = "Level4",group = "FİBONACCİ DÜZELTMESİ")

show_1_414 = input(false, "", inline = "Level4",group = "FİBONACCİ DÜZELTMESİ")
value_1_414 = input(1.414, "", inline = "Level4",group = "FİBONACCİ DÜZELTMESİ")
color_1_414 = input(#ff0000, "", inline = "Level4",group = "FİBONACCİ DÜZELTMESİ")

show_1_618 = input(true, "", inline = "Level5",group = "FİBONACCİ DÜZELTMESİ")
value_1_618 = input(1.618, "", inline = "Level5",group = "FİBONACCİ DÜZELTMESİ")
color_1_618 = input(#ff0000, "", inline = "Level5",group = "FİBONACCİ DÜZELTMESİ")

show_1_65 = input(false, "", inline = "Level5",group = "FİBONACCİ DÜZELTMESİ")
value_1_65 = input(1.65, "", inline = "Level5",group = "FİBONACCİ DÜZELTMESİ")
color_1_65 = input(#ff0000, "", inline = "Level5",group = "FİBONACCİ DÜZELTMESİ")

show_2_618 = input(true, "", inline = "Level6",group = "FİBONACCİ DÜZELTMESİ")
value_2_618 = input(2.618, "", inline = "Level6",group = "FİBONACCİ DÜZELTMESİ")
color_2_618 = input(#ff0000, "", inline = "Level6",group = "FİBONACCİ DÜZELTMESİ")

show_2_65 = input(false, "", inline = "Level6",group = "FİBONACCİ DÜZELTMESİ")
value_2_65 = input(2.65, "", inline = "Level6",group = "FİBONACCİ DÜZELTMESİ")
color_2_65 = input(#ff0000, "", inline = "Level6",group = "FİBONACCİ DÜZELTMESİ")

show_3_618 = input(true, "", inline = "Level7",group = "FİBONACCİ DÜZELTMESİ")
value_3_618 = input(3.618, "", inline = "Level7",group = "FİBONACCİ DÜZELTMESİ")
color_3_618 = input(#ff0000, "", inline = "Level7",group = "FİBONACCİ DÜZELTMESİ")

show_3_65 = input(false, "", inline = "Level7",group = "FİBONACCİ DÜZELTMESİ")
value_3_65 = input(3.65, "", inline = "Level7",group = "FİBONACCİ DÜZELTMESİ")
color_3_65 = input(#ff0000, "", inline = "Level7",group = "FİBONACCİ DÜZELTMESİ")

show_4_236 = input(true, "", inline = "Level8",group = "FİBONACCİ DÜZELTMESİ")
value_4_236 = input(4.236, "", inline = "Level8",group = "FİBONACCİ DÜZELTMESİ")
color_4_236 = input(#ff0000, "", inline = "Level8",group = "FİBONACCİ DÜZELTMESİ")

show_4_618 = input(false, "", inline = "Level8",group = "FİBONACCİ DÜZELTMESİ")
value_4_618 = input(4.618, "", inline = "Level8",group = "FİBONACCİ DÜZELTMESİ")
color_4_618 = input(#ff0000, "", inline = "Level8",group = "FİBONACCİ DÜZELTMESİ")

show_neg_0_236 = input(false, "", inline = "Level9",group = "FİBONACCİ DÜZELTMESİ")
value_neg_0_236 = input(-0.236, "", inline = "Level9",group = "FİBONACCİ DÜZELTMESİ")
color_neg_0_236 = input(#ff0000, "", inline = "Level9",group = "FİBONACCİ DÜZELTMESİ")

show_neg_0_382 = input(false, "", inline = "Level9",group = "FİBONACCİ DÜZELTMESİ")
value_neg_0_382 = input(-0.382, "", inline = "Level9",group = "FİBONACCİ DÜZELTMESİ")
color_neg_0_382 = input(#ff0000, "", inline = "Level9",group = "FİBONACCİ DÜZELTMESİ")

show_neg_0_618 = input(false, "", inline = "Level10",group = "FİBONACCİ DÜZELTMESİ")
value_neg_0_618 = input(-0.618, "", inline = "Level10",group = "FİBONACCİ DÜZELTMESİ")
color_neg_0_618 = input(#ff0000, "", inline = "Level10",group = "FİBONACCİ DÜZELTMESİ")

show_neg_0_65 = input(false, "", inline = "Level10",group = "FİBONACCİ DÜZELTMESİ")
value_neg_0_65 = input(-0.65, "", inline = "Level10",group = "FİBONACCİ DÜZELTMESİ")
color_neg_0_65 = input(#ff0000, "", inline = "Level10",group = "FİBONACCİ DÜZELTMESİ")


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

////////////////////////////////////////////////////////////////////////////////////////////////////
//━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  OTO TREND TEMELLİ FİBONACCİ  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
////////////////////////////////////////////////////////////////////////////////////////////////////

depthTooltip_ = "The minimum number of bars that will be taken into account when calculating the indicator."
depth_ = input.int(title="Depth", defval=10, minval=1, inline = "Pivots", tooltip=depthTooltip_,group = "TREND TEMELLİ FİBONACCİ")
reverse_ = input(false, "Reverse",group = "TREND TEMELLİ FİBONACCİ")
var extendLeft_ = input(false, "Extend Left    |    Extend Right", inline = "Extend Liness",group = "TREND TEMELLİ FİBONACCİ")
var extendRight_ = input(true, "", inline = "Extend Liness",group = "TREND TEMELLİ FİBONACCİ")
var extending_ = extend.none
if extendLeft_ and extendRight_
    extending_ := extend.both
if extendLeft_ and not extendRight_
    extending_ := extend.left
if not extendLeft_ and extendRight_
    extending_ := extend.right
prices_ = input(true, "Show Prices",group = "TREND TEMELLİ FİBONACCİ")
levels_ = input(true, "Show Levels", inline = "Levells",group = "TREND TEMELLİ FİBONACCİ")
levelsFormat_ = input.string("Values", "", options = ["Values", "Percent"], inline = "Levells",group = "TREND TEMELLİ FİBONACCİ")
labelsPosition_ = input.string("Left", "Labels Position", options = ["Left", "Right"],group = "TREND TEMELLİ FİBONACCİ")
var int backgroundTransparency_ = input.int(100, "Background Transparency", minval = 0, maxval = 100,group = "TREND TEMELLİ FİBONACCİ")
var float upperThreshold = 0.236
var float lowerThreshold = 1.0

var line lineLastHL = na
var line lineLastLH = na
var line lineLast_ = na

var float iLastH = 0
var float iLastL = 0
var float pLastH = 0
var float pLastL = 0

var float[] iPivotsH = array.new_float()
var float[] iPivotsL = array.new_float()
var float[] pPivotsH = array.new_float()
var float[] pPivotsL = array.new_float()

var isHighLast_ = false
var float startPrice_ = na
var float endPrice_ = na
var float diff_ = na

pivotlar(src, length, isHigh) =>
    l2 = length * 2
    c = nz(src[length])
    ok = true
    for i = 0 to l2
        if isHigh and src[i] > c
            ok := false

        if not isHigh and src[i] < c
            ok := false
    if ok
        [bar_index[length], c]
    else
        [int(na), float(na)]
[iH_, pH_] = pivotlar(high, depth_ / 2, true)
[iL_, pL_] = pivotlar(low, depth_ / 2, false)

countPivotsH = array.size(iPivotsH)
countPivotsL = array.size(iPivotsL)

if countPivotsH > 0 and countPivotsL > 0
    iLastH := array.get(iPivotsH, countPivotsH-1)
    iLastL := array.get(iPivotsL, countPivotsL-1)
    isHighLast_ := iLastH > iLastL
    iLastH := 1
    if isHighLast_
        if not na(iH_)
            pLastH := array.get(pPivotsH, countPivotsH-1)
            if pH_ > pLastH
                array.set(iPivotsH, countPivotsH-1, iH_)
                array.set(pPivotsH, countPivotsH-1, pH_)
            iH_ := na
    else
        if not na(iL_)
            pLastL := array.get(pPivotsL, countPivotsL-1)
            if pL_ < pLastL
                array.set(iPivotsL, countPivotsL-1, iL_)
                array.set(pPivotsL, countPivotsL-1, pL_)
            iL_ := na

if not na(iH_)
    array.push(iPivotsH, iH_)
    array.push(pPivotsH, pH_)

if not na(iL_)
    array.push(iPivotsL, iL_)
    array.push(pPivotsL, pL_)

pPivotsHCopy = array.copy(pPivotsH)
pPivotsLCopy = array.copy(pPivotsL)
iPivotsHCopy = array.copy(iPivotsH)
iPivotsLCopy = array.copy(iPivotsL)

if barstate.islast
    for j = bar_index to 0
        if array.size(iPivotsHCopy) == 0 or array.size(iPivotsLCopy) == 0
            break

        iLastH := array.pop(iPivotsHCopy)
        iLastL := array.pop(iPivotsLCopy)

        pLastH := array.pop(pPivotsHCopy)
        pLastL := array.pop(pPivotsLCopy)

        iPrevPivot = 0.0
        pPrevPivot = 0.0

        isHighLast_ := iLastH > iLastL

        if isHighLast_
            for i = array.size(iPivotsHCopy)-1 to 0
                if i < 0
                    break
                else if array.get(iPivotsHCopy, i) < iLastL
                    break
                if array.get(pPivotsHCopy, i) > pLastH
                    iLastH := array.pop(iPivotsHCopy)
                    pLastH := array.pop(pPivotsHCopy)
                else
                    array.remove(iPivotsHCopy, i)
                    array.remove(pPivotsHCopy, i)
        else
            for i = array.size(iPivotsLCopy)-1 to 0
                if i < 0
                    break
                else if array.get(iPivotsLCopy, i) < iLastH
                    break
                if array.get(pPivotsLCopy, i) < pLastL
                    iLastL := array.pop(iPivotsLCopy)
                    pLastL := array.pop(pPivotsLCopy)
                else
                    array.remove(iPivotsLCopy, i)
                    array.remove(pPivotsLCopy, i)

        if array.size(iPivotsHCopy) == 0 or array.size(iPivotsLCopy) == 0
            break

        isHighLast_ := iLastH > iLastL

        if isHighLast_
            iPrevPivot := array.get(iPivotsHCopy, array.size(iPivotsHCopy)-1)
            pPrevPivot := array.get(pPivotsHCopy, array.size(iPivotsHCopy)-1)
        else
            iPrevPivot := array.get(iPivotsLCopy, array.size(iPivotsLCopy)-1)
            pPrevPivot := array.get(pPivotsLCopy, array.size(iPivotsLCopy)-1)

        if isHighLast_
            startPrice_ := pPrevPivot
            endPrice_ := pLastL
            diff_ := math.abs(startPrice_ - endPrice_)
            if pLastH > endPrice_ + diff_ * lowerThreshold or pLastH < endPrice_ + diff_ * upperThreshold
                array.push(iPivotsLCopy, iLastL)
                array.push(pPivotsLCopy, pLastL)
                continue
            line.delete(lineLastHL)
            line.delete(lineLastLH)
            line.delete(lineLast_)
            lineLastHL := line.new(int(iPrevPivot), pPrevPivot, int(iLastL), pLastL, color=color.rgb(255, 82, 82, 100), width=1, style=line.style_dashed)
            lineLastLH := line.new(int(iLastL), pLastL, int(iLastH), pLastH, color=color.rgb(76, 175, 79, 100), width=1, style=line.style_dashed)
            lineLast_ := lineLastLH
        else
            startPrice_ := pPrevPivot
            endPrice_ := pLastH
            diff_ := math.abs(startPrice_ - endPrice_)
            if pLastL < endPrice_ - diff_ * lowerThreshold or pLastL > endPrice_ - diff_ * upperThreshold
                array.push(iPivotsHCopy, iLastH)
                array.push(pPivotsHCopy, pLastH)
                continue
            line.delete(lineLastHL)
            line.delete(lineLastLH)
            line.delete(lineLast_)
            lineLastLH := line.new(int(iPrevPivot), pPrevPivot, int(iLastH), pLastH, color=color.rgb(255, 82, 82, 100), width=1, style=line.style_dashed)
            lineLastHL := line.new(int(iLastH), pLastH, int(iLastL), pLastL, color=color.rgb(76, 175, 79, 100), width=1, style=line.style_dashed)
            lineLast_ := lineLastHL
        break

_draw_line_(price, col) =>
    var id = if show_ttfibo ==1 
        line.new(0, price, 0, price, color=col, width=1, extend=extending_,style = line.style_dashed)
    if not na(lineLast_)
        line.set_xy1(id, line.get_x1(lineLast_), price)
        line.set_xy2(id, line.get_x2(lineLast_), price)
    id

_draw_label_(price, txt, txtColor) =>
    if not na(price)
        x = labelsPosition_ == "Left" ? line.get_x1(lineLast_) : not extendRight_ ? line.get_x2(lineLast_) : bar_index
        labelStyle = labelsPosition_ == "Left" ? label.style_label_right : label.style_label_left
        align = labelsPosition_ == "Left" ? text.align_right : text.align_right
        labelsAlignStrLeft = txt + '\n ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏ \n'
        labelsAlignStrRight = '       ' + txt + '\n ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏  ‏ \n'
        labelsAlignStr = labelsPosition_ == "Left" ? labelsAlignStrLeft : labelsAlignStrRight
        var id = if show_ttfibo ==1 
            label.new(x=x, y=price, text=labelsAlignStr, textcolor=txtColor, style=labelStyle, textalign=align, color=#00000000,size=size.small)
        label.set_xy(id, x, price)
        label.set_text(id, labelsAlignStr)
        label.set_textcolor(id, txtColor)

_wrap_(txt) =>
    "(" + str.tostring(txt, format.mintick) + ")"

_label_txt_(level, price) =>
    if not na(price)
        l = levelsFormat_ == "Values" ? str.tostring(level) : str.tostring(level * 100) + "%"
        (levels_ ? l : "") + (prices_ ? _wrap_(price) : "")

_crossing_level_(sr, r) =>
    (r > sr and r < sr[1]) or (r < sr and r > sr[1])

diff_ := (isHighLast_ ? -1 : 1) * math.abs(startPrice_ - endPrice_)
offset = isHighLast_ ? line.get_y1(lineLastLH) - line.get_y2(lineLastLH) : line.get_y1(lineLastHL) - line.get_y2(lineLastHL)
offset := (isHighLast_ ? -1 : 1) * math.abs(offset)

processLevel_(show, value, colorL, lineIdOther) =>
    float m = value
	r = (endPrice_ - offset) + ((reverse_ ? -1 : 1) * diff_ * m)
    if show
		lineId = _draw_line_(r, colorL)
        _draw_label_(r, _label_txt_(m, r), colorL)
        if _crossing_level_(close, r)
            alert("Autofib: " + syminfo.ticker + " crossing level " + str.tostring(value))
        if not na(lineIdOther)
            linefill.new(lineId, lineIdOther, color = color.new(colorL, backgroundTransparency_))
		lineId
    else
		lineIdOther

show_00 = input(true, "", inline = "Levell0",group = "TREND TEMELLİ FİBONACCİ")
value_00 = input(0, "", inline = "Levell0",group = "TREND TEMELLİ FİBONACCİ")
color_00 = input(#000000, "", inline = "Levell0",group = "TREND TEMELLİ FİBONACCİ")

show_00_236 = input(true, "", inline = "Levell0",group = "TREND TEMELLİ FİBONACCİ")
value_00_236 = input(0.236, "", inline = "Levell0",group = "TREND TEMELLİ FİBONACCİ")
color_00_236 = input(#000000, "", inline = "Levell0",group = "TREND TEMELLİ FİBONACCİ")

show_00_382 = input(true, "", inline = "Levell1",group = "TREND TEMELLİ FİBONACCİ")
value_00_382 = input(0.382, "", inline = "Levell1",group = "TREND TEMELLİ FİBONACCİ")
color_00_382 = input(#000000, "", inline = "Levell1",group = "TREND TEMELLİ FİBONACCİ")

show_00_5 = input(true, "", inline = "Levell1",group = "TREND TEMELLİ FİBONACCİ")
value_00_5 = input(0.5, "", inline = "Levell1",group = "TREND TEMELLİ FİBONACCİ")
color_00_5 = input(#000000, "", inline = "Levell1",group = "TREND TEMELLİ FİBONACCİ")

show_00_618 = input(true, "", inline = "Levell2",group = "TREND TEMELLİ FİBONACCİ")
value_00_618 = input(0.618, "", inline = "Levell2",group = "TREND TEMELLİ FİBONACCİ")
color_00_618 = input(#000000, "", inline = "Levell2",group = "TREND TEMELLİ FİBONACCİ")

show_00_65 = input(false, "", inline = "Levell2",group = "TREND TEMELLİ FİBONACCİ")
value_00_65 = input(0.65, "", inline = "Levell2",group = "TREND TEMELLİ FİBONACCİ")
color_00_65 = input(color.rgb(0, 0, 0), "", inline = "Levell2",group = "TREND TEMELLİ FİBONACCİ")

show_00_786 = input(true, "", inline = "Levell3",group = "TREND TEMELLİ FİBONACCİ")
value_00_786 = input(0.786, "", inline = "Levell3",group = "TREND TEMELLİ FİBONACCİ")
color_00_786 = input(#000000, "", inline = "Levell3",group = "TREND TEMELLİ FİBONACCİ")

show_11 = input(true, "", inline = "Levell3",group = "TREND TEMELLİ FİBONACCİ")
value_11 = input(1, "", inline = "Levell3",group = "TREND TEMELLİ FİBONACCİ")
color_11 = input(#000000, "", inline = "Levell3",group = "TREND TEMELLİ FİBONACCİ")

show_11_272 = input(false, "", inline = "Levell4",group = "TREND TEMELLİ FİBONACCİ")
value_11_272 = input(1.272, "", inline = "Levell4",group = "TREND TEMELLİ FİBONACCİ")
color_11_272 = input(#000000, "", inline = "Levell4",group = "TREND TEMELLİ FİBONACCİ")

show_11_414 = input(false, "", inline = "Levell4",group = "TREND TEMELLİ FİBONACCİ")
value_11_414 = input(1.414, "", inline = "Levell4",group = "TREND TEMELLİ FİBONACCİ")
color_11_414 = input(#000000, "", inline = "Levell4",group = "TREND TEMELLİ FİBONACCİ")

show_11_618 = input(true, "", inline = "Levell5",group = "TREND TEMELLİ FİBONACCİ")
value_11_618 = input(1.618, "", inline = "Levell5",group = "TREND TEMELLİ FİBONACCİ")
color_11_618 = input(#000000, "", inline = "Levell5",group = "TREND TEMELLİ FİBONACCİ")

show_11_65 = input(false, "", inline = "Levell5",group = "TREND TEMELLİ FİBONACCİ")
value_11_65 = input(1.65, "", inline = "Levell5",group = "TREND TEMELLİ FİBONACCİ")
color_11_65 = input(#000000, "", inline = "Levell5",group = "TREND TEMELLİ FİBONACCİ")

show_22_618 = input(true, "", inline = "Levell6",group = "TREND TEMELLİ FİBONACCİ")
value_22_618 = input(2.618, "", inline = "Levell6",group = "TREND TEMELLİ FİBONACCİ")
color_22_618 = input(#000000, "", inline = "Levell6",group = "TREND TEMELLİ FİBONACCİ")

show_22_65 = input(false, "", inline = "Levell6",group = "TREND TEMELLİ FİBONACCİ")
value_22_65 = input(2.65, "", inline = "Levell6",group = "TREND TEMELLİ FİBONACCİ")
color_22_65 = input(color.rgb(0, 0, 0), "", inline = "Levell6",group = "TREND TEMELLİ FİBONACCİ")

show_33_618 = input(true, "", inline = "Levell7",group = "TREND TEMELLİ FİBONACCİ")
value_33_618 = input(3.618, "", inline = "Levell7",group = "TREND TEMELLİ FİBONACCİ")
color_33_618 = input(#000000, "", inline = "Levell7",group = "TREND TEMELLİ FİBONACCİ")

show_33_65 = input(false, "", inline = "Levell7",group = "TREND TEMELLİ FİBONACCİ")
value_33_65 = input(3.65, "", inline = "Levell7",group = "TREND TEMELLİ FİBONACCİ")
color_33_65 = input(#000000, "", inline = "Levell7",group = "TREND TEMELLİ FİBONACCİ")

show_44_236 = input(true, "", inline = "Levell8",group = "TREND TEMELLİ FİBONACCİ")
value_44_236 = input(4.236, "", inline = "Levell8",group = "TREND TEMELLİ FİBONACCİ")
color_44_236 = input(#000000, "", inline = "Levell8",group = "TREND TEMELLİ FİBONACCİ")

show_44_618 = input(false, "", inline = "Levell8",group = "TREND TEMELLİ FİBONACCİ")
value_44_618 = input(4.618, "", inline = "Levell8",group = "TREND TEMELLİ FİBONACCİ")
color_44_618 = input(#000000, "", inline = "Levell8",group = "TREND TEMELLİ FİBONACCİ")

show_neg_00_236 = input(false, "", inline = "Levell9",group = "TREND TEMELLİ FİBONACCİ")
value_neg_00_236 = input(-0.236, "", inline = "Levell9",group = "TREND TEMELLİ FİBONACCİ")
color_neg_00_236 = input(#000000, "", inline = "Levell9",group = "TREND TEMELLİ FİBONACCİ")

show_neg_00_382 = input(false, "", inline = "Levell9",group = "TREND TEMELLİ FİBONACCİ")
value_neg_00_382 = input(-0.382, "", inline = "Levell9",group = "TREND TEMELLİ FİBONACCİ")
color_neg_00_382 = input(#000000, "", inline = "Levell9",group = "TREND TEMELLİ FİBONACCİ")

show_neg_00_618 = input(false, "", inline = "Levell10",group = "TREND TEMELLİ FİBONACCİ")
value_neg_00_618 = input(-0.618, "", inline = "Levell10",group = "TREND TEMELLİ FİBONACCİ")
color_neg_00_618 = input(#000000, "", inline = "Levell10",group = "TREND TEMELLİ FİBONACCİ")

show_neg_00_65 = input(false, "", inline = "Levell10",group = "TREND TEMELLİ FİBONACCİ")
value_neg_00_65 = input(-0.65, "", inline = "Levell10",group = "TREND TEMELLİ FİBONACCİ")
color_neg_00_65 = input(#000000, "", inline = "Levell10",group = "TREND TEMELLİ FİBONACCİ")


lineIdi0 = processLevel_(show_neg_00_65, value_neg_00_65, color_neg_00_65, line(na))
lineIdi1 = processLevel_(show_neg_00_618, value_neg_00_618, color_neg_00_618, lineIdi0)
lineIdi2 = processLevel_(show_neg_00_382, value_neg_00_382, color_neg_00_382, lineIdi1)
lineIdi3 = processLevel_(show_neg_00_236, value_neg_00_236, color_neg_00_236, lineIdi2)
lineIdi4 = processLevel_(show_00, value_00, color_00, lineIdi3)
lineIdi5 = processLevel_(show_00_236, value_00_236, color_00_236, lineIdi4)
lineIdi6 = processLevel_(show_00_382, value_00_382, color_00_382, lineIdi5)
lineIdi7 = processLevel_(show_00_5, value_00_5, color_00_5, lineIdi6)
lineIdi8 = processLevel_(show_00_618, value_00_618, color_00_618, lineIdi7)
lineIdi9 = processLevel_(show_00_65, value_00_65, color_00_65, lineIdi8)
lineIdi10 = processLevel_(show_00_786, value_00_786, color_00_786, lineIdi9)
lineIdi11 = processLevel_(show_11, value_11, color_11, lineIdi10)
lineIdi12 = processLevel_(show_11_272, value_11_272, color_11_272, lineIdi11)
lineIdi13 = processLevel_(show_11_414, value_11_414, color_11_414, lineIdi12)
lineIdi14 = processLevel_(show_11_618, value_11_618, color_11_618, lineIdi13)
lineIdi15 = processLevel_(show_11_65, value_11_65, color_11_65, lineIdi14)
lineIdi16 = processLevel_(show_22_618, value_22_618, color_22_618, lineIdi15)
lineIdi17 = processLevel_(show_22_65, value_22_65, color_22_65, lineIdi16)
lineIdi18 = processLevel_(show_33_618, value_33_618, color_33_618, lineIdi17)
lineIdi19 = processLevel_(show_33_65, value_33_65, color_33_65, lineIdi18)
lineIdi20 = processLevel_(show_44_236, value_44_236, color_44_236, lineIdi19)
lineIdi21 = processLevel_(show_44_618, value_44_618, color_44_618, lineIdi20)

/////////////////////////////////////////////////////////////////////////////////
//━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  PİVOTLAR  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
/////////////////////////////////////////////////////////////////////////////////
// © JayRogers

////////////////////////////////////////////////////////////////////////////////
//
// ====== ABOUT THIS INDICATOR
//
//  - All your common Pivot types in one nifty bundle.
//
//    • Have up to three pivot sets.
//    • Each pivot set has it's own resolution option.
//    • Whatever flavour suits your tastes - each pivot set can be of a
//      different type if you truly wish.
//
// ====== SOURCES and CREDITS
//
//  - All included pivot calcs were sourced from:
//
//    • https://www.tradingview.com/support/solutions/43000521824-pivot-points-standard/
//    • Using the new 'time_close()' function, so thankyou Pine dev's <3
//
// ====== REASON FOR STUDY
//
//  - To practice making scalable code for working with similar datasets.
//
//    • all the reasons
//
// ====== DISCLAIMER
//
//    Any trade decisions you make are entirely your own responsibility.
//    I've made an effort to squash all the bugs, but you never know!
//
////////////////////////////////////////////////////////////////////////////////

////////////////////////////////////////////////////////////////////////////////
//                                                                            //
//                       ====== OPTION LIST VARS ======                       //
//                                                                            //
//    * Setting up option list variables outside of the actual input can      //
//      make them much easier to work with if any comparison checks are       //
//      required, and can help keep subsequent code clean and readable.       //
//                                                                            //
////////////////////////////////////////////////////////////////////////////////

// -- verbose resolution options.
i_res0 = '1 Hour'
i_res1 = '2 Hour'
i_res2 = '3 Hour'
i_res3 = '4 Hour'
i_res4 = '6 Hour'
i_res5 = '12 Hour'
i_res6 = '1 Day'
i_res7 = '5 Day'
i_res8 = '1 Week'
i_res9 = '1 Month'
i_res10 = '3 Month'
i_res11 = '6 Month'
i_res12 = '1 Year'
i_res13 = '15 Minute'
i_res14 = '30 minute'

// -- pivot options
i_piv0 = 'Traditional'


// -- line style options.
i_line0 = 'Solid'
i_line1 = 'Dotted'
i_line2 = 'Dashed'

////////////////////////////////////////////////////////////////////////////////
//                                                                            //
//                     ====== VAR and ARRAY PRESET ======                     //
//                                                                            //
////////////////////////////////////////////////////////////////////////////////

// -- Preset INT for maximum amount of S|R levels for any single pivot type.
// NOTE - this variable should only be changed if:
//  • you extend a pivot to have more than 5 levels of S|R
//  • you add a new pivot type wiith more than 5 levels of S|R
var int i_maxLevels = 5

// -- Preset INT for max length of pivot arrays [PP + S max + R max]
//  * NOTE: should not be changed.
var int i_maxLength = 1 + i_maxLevels * 2

// -- Initiate arrays to contain INT variables used in drawing function
//    settings, we fill these at the end of the INPUTS section.
var int[] i_settingsA = array.new_int(3, 0)
var int[] i_settingsB = array.new_int(3, 0)
var int[] i_settingsC = array.new_int(3, 0)

////////////////////////////////////////////////////////////////////////////////
//                                                                            //
//                            ====== INPUTS ======                            //
//                                                                            //
//                     * Using the new 'inline' feature *                     //
//                                                                            //
////////////////////////////////////////////////////////////////////////////////

// -- 1. PİVOT AYARLARI

INP_resolutionA = input.string(i_res6, ' ', inline='line1', group='1. PİVOT AYARLARI', options=[i_res13, i_res14, i_res0, i_res1, i_res2, i_res3, i_res4, i_res5, i_res6, i_res7, i_res8, i_res9, i_res10, i_res11, i_res12])
INP_showPivotsA = input.bool(true, ' 1. PİVOTU GÖSTER', inline='line1', group='1. PİVOT AYARLARI')
INP_supportsA = input.int(3, 'S#', inline='line2', group='1. PİVOT AYARLARI', minval=0, maxval=i_maxLevels)
INP_flavourA = i_piv0
INP_resistsA = input.int(3, ' R#', inline='line2', group='1. PİVOT AYARLARI', minval=0, maxval=i_maxLevels)

// -- 2. PİVOT AYARLARI

INP_resolutionB = input.string(i_res8, ' ', inline='line3', group='2. PİVOT AYARLARI', options=[i_res13, i_res14, i_res0, i_res1, i_res2, i_res3, i_res4, i_res5, i_res6, i_res7, i_res8, i_res9, i_res10, i_res11, i_res12])
INP_showPivotsB = input.bool(true, ' 2. PİVOTU GÖSTER', inline='line3', group='2. PİVOT AYARLARI')
INP_supportsB = input.int(3, 'S#', inline='line4', group='2. PİVOT AYARLARI', minval=0, maxval=i_maxLevels)
INP_flavourB = i_piv0
INP_resistsB = input.int(3, ' R#', inline='line4', group='2. PİVOT AYARLARI', minval=0, maxval=i_maxLevels)

// -- 3. PİVOT AYARLARI

INP_resolutionC = input.string(i_res9, ' ', inline='line5', group='3. PİVOT AYARLARI', options=[i_res13, i_res14, i_res0, i_res1, i_res2, i_res3, i_res4, i_res5, i_res6, i_res7, i_res8, i_res9, i_res10, i_res11, i_res12])
INP_showPivotsC = input.bool(true, ' 3. PİVOTU GÖSTER', inline='line5', group='3. PİVOT AYARLARI')
INP_supportsC = input.int(3, 'S#', inline='line6', group='3. PİVOT AYARLARI', minval=0, maxval=i_maxLevels)
INP_flavourC = i_piv0
INP_resistsC = input.int(3, ' R#', inline='line6', group='3. PİVOT AYARLARI', minval=0, maxval=i_maxLevels)

// -- ŞEKİL AYARLARI

INP_supportStyle = input.string(i_line0, ' ', inline='line8', group='ŞEKİL AYARLARI', options=[i_line0, i_line1, i_line2])
INP_supportColour_line = input.color(color.rgb(0, 255, 80), ' S Lines', inline='line8', group='ŞEKİL AYARLARI')
INP_supportColour_label = input.color(color.rgb(0, 255, 80), ' S Labels', inline='line8', group='ŞEKİL AYARLARI')

INP_pivotStyle = input.string(i_line0, ' ', inline='line9', group='ŞEKİL AYARLARI', options=[i_line0, i_line1, i_line2])
INP_pivotColour_line = input.color(color.rgb(0, 6, 255), ' P Lines', inline='line9', group='ŞEKİL AYARLARI')
INP_pivotColour_label = input.color(color.rgb(0, 6, 255), ' S Labels', inline='line9', group='ŞEKİL AYARLARI')

INP_resistStyle = input.string(i_line0, ' ', inline='line10', group='ŞEKİL AYARLARI', options=[i_line0, i_line1, i_line2])
INP_resistColour_line = input.color(color.rgb(255, 0, 0), ' R Lines', inline='line10', group='ŞEKİL AYARLARI')
INP_resistColour_label = input.color(color.rgb(255, 0, 0), ' S Labels', inline='line10', group='ŞEKİL AYARLARI')

// -- drawing settings for selection A
array.set(i_settingsA, 0, INP_showPivotsA ? 1 : 0)
array.set(i_settingsA, 1, INP_supportsA)
array.set(i_settingsA, 2, INP_resistsA)
// -- drawing settings for selection B
array.set(i_settingsB, 0, INP_showPivotsB ? 1 : 0)
array.set(i_settingsB, 1, INP_supportsB)
array.set(i_settingsB, 2, INP_resistsB)
// -- drawing settings for selection C
array.set(i_settingsC, 0, INP_showPivotsC ? 1 : 0)
array.set(i_settingsC, 1, INP_supportsC)
array.set(i_settingsC, 2, INP_resistsC)

////////////////////////////////////////////////////////////////////////////////
//                                                                            //
//                          ====== FUNCTIONS ======                           //
//                                                                            //
////////////////////////////////////////////////////////////////////////////////
f_getResolution(_inputResolution) =>
    //  string  _inputResolution : user selected resolution input
    // () Description:
    //  - Resolver for custom resolution input selection, converts input to
    //    compatible return string for security, output is also used for less
    //    verbose label text options.
    // Dependencies:
    //  - i_res1, i_res2, i_res3, i_res4, i_res5, i_res6
    //  - i_res7, i_res8, i_res9, i_res10, i_res11, i_res12
    // Notes:
    //  - i_res0 excluded as it's a token placeholder for default "60".

    string _r = _inputResolution  // a more ternary challenge friendly var
    string _default = '60'  // if i_res0 was input, or failure.

    // compare input to determine proper string return for security calls.
    _return = _r == i_res1 ? '120' : _r == i_res2 ? '180' : _r == i_res3 ? '240' : _r == i_res4 ? '360' : _r == i_res5 ? '720' : _r == i_res6 ? 'D' : _r == i_res7 ? '5D' : _r == i_res8 ? 'W' : _r == i_res9 ? 'M' : _r == i_res10 ? '3M' : _r == i_res11 ? '6M' : _r == i_res12 ? '12M' : _r == i_res13 ? '15' : _r == i_res14 ? '30' : _default
    _return

f_getLineStyle(_inputStyle) =>
    //  string  _inputStyle : user selected style input
    // () resolver for custom line style input selection, returns a usable
    //    line style type.
    // Dependencies:
    //  - i_line1, i_line2
    // Notes:
    //  * i_line0 omitted as we default to 'line.style_solid' anyway

    // compare input to determine proper line style to return.
    _return = _inputStyle == i_line1 ? line.style_dotted : _inputStyle == i_line2 ? line.style_dashed : line.style_solid
    _return

// -- helper function for checking if a value is inside a min-max range
f_isInsideRange(_val, _min, _max) =>
    _val >= _min and _val <= _max

f_getPivotTraditional(_prevHigh, _prevLow, _prevClose) =>
    //  float   _prevHigh | _prevLow | _prevClose : HTF security OHLC values
    // () calculates a pivot set and assigns to proper array indexes for return.
    // Notes:
    //  - f_renderPivotArray() expects float data in the following sequence..
    //
    //      Pivot     : [ 0 ]
    //      Supports  : [ 1 to i_maxLevels ]
    //      Resists   : [ ( i_maxLevels + 1 ) to i_maxLength ]

    // init empty array with predefined length of i_maxLength
    var float[] _array = array.new_float(i_maxLength, na)

    // pivot level, array index [0]
    _pivot = (_prevHigh + _prevLow + _prevClose) / 3
    array.set(_array, 0, _pivot)

    // support levels, array indexes [1] through [i_maxLevels]
    array.set(_array, 1, _pivot * 2 - _prevHigh)
    array.set(_array, 2, _pivot - (_prevHigh - _prevLow))
    array.set(_array, 3, _pivot * 2 - (2 * _prevHigh - _prevLow))
    array.set(_array, 4, _pivot * 3 - (3 * _prevHigh - _prevLow))
    array.set(_array, 5, _pivot * 4 - (4 * _prevHigh - _prevLow))

    // resistance levels, array indexes [i_maxLevels + 1] through [i_maxLength]
    array.set(_array, 1 + i_maxLevels, _pivot * 2 - _prevLow)
    array.set(_array, 2 + i_maxLevels, _pivot + _prevHigh - _prevLow)
    array.set(_array, 3 + i_maxLevels, _pivot * 2 + _prevHigh - 2 * _prevLow)
    array.set(_array, 4 + i_maxLevels, _pivot * 3 + _prevHigh - 3 * _prevLow)
    array.set(_array, 5 + i_maxLevels, _pivot * 4 + _prevHigh - 4 * _prevLow)

    _return = _array
    _return

f_getPivotSet(_flavour, _resolution) =>
    //  string  _flavour    : user input pivot type selection
    //  string  _resolution : user input resolution selection
    // () gets OHLC values from selected resolution, and returns requested
    //    pivot calculation array.

    // previous OHLC series for selected resolution
    [_prevOpen, _prevHigh, _prevLow, _prevClose] = request.security(syminfo.tickerid, _resolution, [open[1], high[1], low[1], close[1]], lookahead=barmerge.lookahead_on)

    var float _currOpen = na
    if ta.change(time(_resolution)) != 0
        _currOpen := open
        _currOpen

    // float array to contain S|R levels for return.
    var float[] _pivotSet = array.new_float(i_maxLength, na)

    _pivotSet := f_getPivotTraditional(_prevHigh, _prevLow, _prevClose)

    _pivotSet  // return the float array

f_renderPivotArray(_resolution, _settings, _floats, _lines, _labels) =>
    //  string  _resolution : user selected resolution input
    //  int[]   _settings   : pivot specific input settings
    //  float[] _floats     : pivot float array
    //  line[]  _lines      : line array to hold rendered lines
    //  label[] _labels     : label array to hold rendered labels
    //  
    // () Description:
    //  - Designed for scalability and plug-n-play functionality if any new
    //    pivot type is added, or extended.
    //  - If a f_pivotType() function properly assigns it's float values in the
    //    following format/order, it should be able to to render them with no
    //    hassle at all.
    //
    //      Pivot     : [ 0 ]
    //      Supports  : [ 1 to i_maxLevels ]
    //      Resists   : [ ( i_maxLevels + 1 ) to i_maxLength ]  

    // set up a few common vars for drawing using 'time' placement
    _xloc = xloc.bar_time
    _x1 = ta.valuewhen(ta.change(time(_resolution)), time, 0)  // time
    _x2 = time_close(_resolution)  // new! Thanks pine team <3

    // set up some temp vars for creating our lines and labels
    var line _line = na
    var label _labelLeft = na
    var label _labelPrice = na

    // init some readable settings variables to hold _settings data
    var bool _showPivot = false
    var int _maxSupports = i_maxLevels
    var int _maxResists = i_maxLevels

    // hand off the settings array to the vars
    _showPivot := array.get(_settings, 0) > 0 ? true : false
    _maxSupports := array.get(_settings, 1)
    _maxResists := array.get(_settings, 2)

    // if we want to show the particular pivot set
    // * the change() check was causing issues on extended hours intraday charts
    //   so i have removed it for now. I will look into other options for the
    //   next proper update.
    if _showPivot  // change( time( _resolution ) ) != 0 and _showPivot

        // clean up old lines and labels before drawing our new set
        for i = 1 to array.size(_lines) by 1  // loop and delete 1 by 1
            line.delete(array.get(_lines, i - 1))

        // the label array is dynamic length, so we..
        if array.size(_labels) > 0  // ..check to see if it has content..
            for i = 1 to array.size(_labels) by 1  // ..then loop it into the trash
                label.delete(array.shift(_labels))

        // check for properly populated float array
        if array.size(_floats) > 0 and not na(array.get(_floats, 0))

            // now loop through the floats
            for i = 1 to array.size(_floats) by 1

                // don't want to bother with [i - 1] throughout the entire loop
                _index = i - 1  // so set up a var

                // if we have a valid float at the current loop index
                if not na(array.get(_floats, _index))

                    // common variables used in all conditions
                    _activePrice = array.get(_floats, _index)
                    _priceString = str.tostring(_activePrice, ' #.##')

                    //  IDENTIFYING SUPPORT LEVELS IN ARRAY
                    //
                    // support index range : [ from 1 to i_maxLevels ]
                    if f_isInsideRange(_index, 1, i_maxLevels)

                        // only draw levels : [ from 1 to _maxSupports ]
                        if f_isInsideRange(_index, 1, _maxSupports)

                            // use index to set level numbers on labels
                            string _level = str.tostring(_index)

                            _line := if(show_pivot == 1)
                                line.new(_x1, _activePrice, _x2, _activePrice, _xloc, extend.none, style=f_getLineStyle(INP_supportStyle), color=INP_supportColour_line, width=1)

                            _labelLeft := if(show_pivot == 1) 
                                label.new(_x2, _activePrice, 'S' + _level + '-' + _resolution + _priceString, _xloc, style=label.style_label_left, color=#00000000, textcolor=INP_supportColour_label)
                            _labelLeft

                    //  IDENTIFYING RESISTANCE LEVELS IN ARRAY
                    //
                    // resistance index range : [ from (1 + i_maxLevels) to (2 * i_maxLevels) ]

                        // only draw levels : [ from (1 + i_maxLevels) to (_maxResists + i_maxLevels) ]
                    else if f_isInsideRange(_index, 1 + i_maxLevels, 2 * i_maxLevels)
                        if f_isInsideRange(_index, 1 + i_maxLevels, _maxResists + i_maxLevels)

                            // adjusted _index value to attain 1/2/3 sequence for string operation
                            int _adjust = _index - i_maxLevels
                            // dynamic line width based on adjusted _index value
                            int _width = _adjust > 3 ? _adjust - 2 : _adjust
                            // use adjusted index value to set proper level numbers on labels
                            string _level = str.tostring(_adjust)

                            _line := if(show_pivot == 1) 
                                line.new(_x1, _activePrice, _x2, _activePrice, _xloc, extend.none, style=f_getLineStyle(INP_resistStyle), color=INP_resistColour_line, width=1)

                            _labelLeft :=  if(show_pivot == 1)
                                label.new(_x2, _activePrice, 'R' + _level + '-' + _resolution + _priceString, _xloc, style=label.style_label_left, color=#00000000, textcolor=INP_resistColour_label)
                            _labelLeft
                    else

// PIVOT - No identification needed, always using index 0

                        _line :=  if(show_pivot == 1)
                            line.new(_x1, _activePrice, _x2, _activePrice, _xloc, extend.none, style=f_getLineStyle(INP_pivotStyle), color=INP_pivotColour_line, width=3)

                        _labelLeft := if(show_pivot == 1)
                            label.new(_x2, _activePrice, 'P' + '-' + _resolution + _priceString, _xloc, style=label.style_label_left, color=#00000000, textcolor=INP_pivotColour_label)
                        _labelLeft

                    // set our line to it's respective array index
                    array.set(_lines, _index, _line)

                    // push labels into array, order doesn't matter.
                    if not na(_labelLeft)
                        array.push(_labels, _labelLeft)
                    if not na(_labelPrice)
                        array.push(_labels, _labelPrice)

////////////////////////////////////////////////////////////////////////////////
//                                                                            //
//                   ====== SERIES, LINES and LABELS ======                   //
//                                                                            //
////////////////////////////////////////////////////////////////////////////////

// -- selected resolutions
string _resolutionA = f_getResolution(INP_resolutionA)
string _resolutionB = f_getResolution(INP_resolutionB)
string _resolutionC = f_getResolution(INP_resolutionC)

isimler(name) =>
    name == INP_resolutionA ? _resolutionA : name == INP_resolutionB ? _resolutionB : name == INP_resolutionC ? _resolutionC : na

// -- pivot series price data sets
float[] _pivotFloatsA = f_getPivotSet(INP_flavourA, _resolutionA)
float[] _pivotFloatsB = f_getPivotSet(INP_flavourB, _resolutionB)
float[] _pivotFloatsC = f_getPivotSet(INP_flavourC, _resolutionC)

// -- pivot lines array A, and companion labels array
var line[] _pivotLinesA = array.new_line(i_maxLength, na)
var label[] _pivotLabelsA = array.new_label()

// -- pivot lines array B, and companion labels array
var line[] _pivotLinesB = array.new_line(i_maxLength, na)
var label[] _pivotLabelsB = array.new_label()

// -- pivot lines array C, and companion labels array
var line[] _pivotLinesC = array.new_line(i_maxLength, na)
var label[] _pivotLabelsC = array.new_label()

////////////////////////////////////////////////////////////////////////////////
//                                                                            //
//                     ====== DRAWING and PLOTTING ======                     //
//                                                                            //
////////////////////////////////////////////////////////////////////////////////

// -- feed in our settings and data to the render function for set A
f_renderPivotArray(isimler(INP_resolutionA), i_settingsA, _pivotFloatsA, _pivotLinesA, _pivotLabelsA)

// -- feed in our settings and data to the render function for set B
f_renderPivotArray(isimler(INP_resolutionB), i_settingsB, _pivotFloatsB, _pivotLinesB, _pivotLabelsB)

// -- feed in our settings and data to the render function for set C
f_renderPivotArray(isimler(INP_resolutionC), i_settingsC, _pivotFloatsC, _pivotLinesC, _pivotLabelsC)

// -- PEANUT

////////////////////////////////////////////////////////////////////////////////////////////////
//━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  DESTEK DİRENÇ BÖLGELERİ  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
////////////////////////////////////////////////////////////////////////////////////////////////

// © ZenAndTheArtOfTrading / PineScriptMastery

// Get user input
atrMovement             = 1.0 //input.float(title="ATR Movement Required", defval=1.0, step=0.5, tooltip="Minimum ATR distance price must move to consider a pivot zone to be 'major'")
lookback                = 25 //input.int(title="High/Low Lookback", defval=25, step=5, tooltip="Lookback period for detecting swing highs/lows")
maxZoneSize             = 2.5 //input.float(title="Max Zone Size (Compared to ATR)", defval=2.5, step=0.5, tooltip="If a zone is larger than the current ATR multiplied by this, it is considered too large and ignored")
newStructureReset       = 25 //input.int(title="Zone Update Count Before Reset", defval=25, step=5, tooltip="The script draws two zones until they're violated - if the first zone is updated this many times, the second zone is reset")
drawPreviousStructure   = true //input.bool(title="Draw Previous Structure", defval=true, tooltip="This turns on/off drawing 'support-turned-resistance' and 'resistance-turned-support'")

// Get current ATR value
atr = ta.atr(14)

// Get highest body and lowest body for the current candle
highestBody = open > close ? open : close
lowestBody = open > close ? close : open

// Set up our persistent S&R variables (1 = the wick and 2 = the body)
var ress1 = 0.0
var ress2 = 0.0
var sup1 = 0.0
var sup2 = 0.0
var lookForNewResistance = true
var lookForNewSupport = true

// Set up our *previous* support & resistance variables (for drawing support-turned-resistance etc)
var previousress1 = 0.0
var previousress2 = 0.0
var previousSup1 = 0.0
var previousSup2 = 0.0

// Set up our ATR variables (for identifying significant declines/rallies to validate S&R zones)
var atrSaved = 0.0
var potentialR1 = 0.0
var potentialR2 = 0.0
var potentialS1 = 0.0
var potentialS2 = 0.0

// Detect fractal swing highs for resistance
// We're looking for this pattern: .|.
if high[1] == ta.highest(high, lookback) and high < high[1] and lookForNewResistance
    r1 = high[1]
    r2 = highestBody[2] > highestBody[1] ? highestBody[2] : highestBody > highestBody[1] ? highestBody : highestBody[1]
    if (r1 - r2) / atr <= maxZoneSize
        lookForNewResistance := false
        potentialR1 := r1
        potentialR2 := r2
        atrSaved := atr
    
// Detect fractal swing lows for support
// We're looking for this pattern: *|*
if low[1] == ta.lowest(low, lookback) and low > low[1] and lookForNewSupport
    s1 = low[1]
    s2 = lowestBody[2] < lowestBody[1] ? lowestBody[2] : lowestBody < lowestBody[1] ? lowestBody : lowestBody[1]
    if (s2 - s1) / atr <= maxZoneSize
        lookForNewSupport := false
        potentialS1 := s1
        potentialS2 := s2
        atrSaved := atr
        
// Check if potential resistance zone has already been violated. If it has, reset our potential R1 & R2
if close > potentialR1 and barstate.isconfirmed
    potentialR1 := na
    potentialR2 := na
    
// Check if potential support zone has already been violated. If it has, reset our potential S1 & S2
if close < potentialS1 and barstate.isconfirmed
    potentialS1 := na
    potentialS2 := na
        
// Check if we've had a significant decline since detecting swing high
if potentialR1 - low >= (atrSaved * atrMovement)
    previousress1 := na(previousress1) ? potentialR1 : previousress1 // Store previous resistance if we're not already drawing it
    previousress2 := na(previousress2) ? potentialR2 : previousress2
    ress1 := potentialR1
    ress2 := potentialR2
    potentialR1 := na
    potentialR2 := na
        
// Check if we've had a significant rally since detecting swing low
if high - potentialS1 >= (atrSaved * atrMovement)
    previousSup1 := na(previousSup1) ? potentialS1 : previousSup1 // Store previous support if we're not already drawing it
    previousSup2 := na(previousSup2) ? potentialS2 : previousSup2
    sup1 := potentialS1
    sup2 := potentialS2
    potentialS1 := na
    potentialS2 := na
        
// Declare support & resistance update counters 
// This is used for forcing a zone reset if a zone is not violated within a reasonable period of time
var supCount = 0
var resCount = 0
    
// If the previous resistance high has been violated then begin searching for a new resistance zone
if close >= ress1 and barstate.isconfirmed
    lookForNewResistance := true
    lookForNewSupport := true
    resCount := resCount + 1
    
// If the previous support low has been violated then begin searching for a new support zone
if close <= sup1 and barstate.isconfirmed
    lookForNewSupport := true
    lookForNewResistance := true
    supCount := supCount + 1
    
// If our current resistance zone has been violated, store its values to draw new *potential* support zone
// The idea being that once a major resistance zone is violated it often becomes future support
// But we only save previous S&R if we don't already have one saved (or our zone update count exceeds newStructureReset)
if (close > ress1 and na(previousress1) and barstate.isconfirmed) or previousress1 == 0.0 or supCount >= newStructureReset
    previousress1 := ress1
    previousress2 := ress2
    supCount := 0
    
// If our current support zone has been violated, store its values to draw new *potential* resistance zone
// The idea being that once a major support zone is violated it often becomes future resistance
// But we only save previous S&R if we don't already have one saved (or our zone update count exceeds newStructureReset)
if (close < sup1 and na(previousSup1) and barstate.isconfirmed) or previousSup1 == 0.0 or resCount >= newStructureReset
    previousSup1 := sup1
    previousSup2 := sup2
    resCount := 0
    
// If our resistance-turned-support zone has been violated, reset our saved resistance variables
if close < previousress2 and barstate.isconfirmed
    previousress1 := na
    previousress2 := na
    
// If our support-turned-resistance zone has been violated, reset our saved support variables
if close > previousSup2 and barstate.isconfirmed
    previousSup1 := na
    previousSup2 := na

ddb_cres_color1 = input.color(color.rgb(76, 175, 79, 50),"Direnç Bölgeleri", inline='crz', group="DESTEK DİRENÇ BÖLGELERİ")
ddb_cres_color2 = input.color(color.rgb(255, 82, 82, 60),"Direnç Bölgeleri", inline='crz', group="DESTEK DİRENÇ BÖLGELERİ")
ddb_csup_color1 = input.color(color.rgb(255, 82, 82, 50),"Destek Bölgeleri", inline='csz', group="DESTEK DİRENÇ BÖLGELERİ")
ddb_csup_color2 = input.color(color.rgb(76, 175, 80, 60),"Destek Bölgeleri", inline='csz', group="DESTEK DİRENÇ BÖLGELERİ")
ddb_psup_color1 = input.color(color.rgb(255, 82, 82, 50),"Önceki Destek Bölgeleri", inline='psz', group="DESTEK DİRENÇ BÖLGELERİ")
ddb_pres_color1 = input.color(color.rgb(76, 175, 79, 50),"Önceki Direnç Bölgeleri", inline='prz', group="DESTEK DİRENÇ BÖLGELERİ")
// Draw our current resistance zone
r1 = plot(ress1 == ress1[1] and show_rsz ? ress1 : na, color=close >= ress1 ? color.green : color.red, style=plot.style_linebr, title="R1",display = display.none)
r2 = plot(ress1 == ress1[1] and show_rsz ? ress2 : na, color=close >= ress1 ? color.green : color.red, style=plot.style_linebr, title="R2",display = display.none)
fill(r1, r2, color=close > ress1 ? ddb_cres_color1 : ddb_cres_color2,  title="Resistance Zone")

// Draw our current support zone
s1 = plot(sup1 == sup1[1] and show_rsz ? sup1 : na, color=close < sup1 ? color.red : color.green, style=plot.style_linebr, title="S1",display = display.none)
s2 = plot(sup1 == sup1[1] and show_rsz ? sup2 : na, color=close < sup1 ? color.red : color.green, style=plot.style_linebr, title="S2",display = display.none)
fill(s1, s2, color=close < sup1 ? ddb_csup_color1 : ddb_csup_color2, title="Support Zone")

// Draw our previous support zone (turned potential resistance)
ps1 = plot(previousSup1 == previousSup1[1] and previousSup1 != sup1 and drawPreviousStructure and show_rsz? previousSup1 : na, color=color.red, style=plot.style_linebr, title="PS1",display = display.none)
ps2 = plot(previousSup1 == previousSup1[1] and previousSup1 != sup1 and drawPreviousStructure and show_rsz ? previousSup2 : na, color=color.red, style=plot.style_linebr, title="PS2",display = display.none)
fill(ps1, ps2, color=ddb_psup_color1, title="Previous Support Zone")

// Draw our previous resistance zone (turned potential support)
pr1 = plot(previousress1 == previousress1[1] and previousress1 != ress1 and drawPreviousStructure and show_rsz ? previousress1 : na, color=color.green, style=plot.style_linebr, title="PR1",display = display.none)
pr2 = plot(previousress1 == previousress1[1] and previousress1 != ress1 and drawPreviousStructure and show_rsz ? previousress2 : na, color=color.green, style=plot.style_linebr, title="PR2",display = display.none)
fill(pr1, pr2, color=ddb_pres_color1, title="Previous Resistance Zone")

//////////////////////////////////////////////////////////////////////////////////////////////////
//━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  EKSTRALAR ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
//////////////////////////////////////////////////////////////////////////////////////////////
len = 300
max = ta.lowest(len)
min = max/2

textColor = input.color(defval = color.white, title = "TEXT COLOR")
// min = ta.lowest(len)
rsiPeriod = input.int(defval = 4, title = "RSI Period", inline = "RSI", group = "RSI")
rsiShow = input.bool(defval = true, title = "", inline = "RSI", group = "RSI")
rsiColor = input.color(defval = color.purple, title = "RSI COLOR", group = "RSI")
rsi = ta.rsi(close,rsiPeriod)

rsiMaPeriod = input.int(defval = 7, title = "RSI MA Period", group = "RSI")
rsiMa = ta.sma(rsi,rsiMaPeriod)

atrPeriod = input.int(defval = 14, title = "ATR Period", inline = "ATR", group = "ATR")
atrShow = input.bool(defval = true, title = "", inline = "ATR", group = "ATR")

atrColor = input.color(defval = color.red, title = "ATR COLOR", group = "ATR")


momPeriod = input.int(defval = 14, title = "MOM Period", inline = "MOM", group = "MOM")
momShow = input.bool(defval = true, title = "", inline = "MOM", group = "MOM")
momColor = input.color(defval = color.orange, title = "MOM COLOR", group = "MOM")
mom = ta.mom(close,momPeriod)


lensig = input.int(14, title="ADX Smoothing", minval=1, maxval=50, inline = "ADX", group = "ADX")
adxShow = input.bool(defval = true, title = "", inline = "ADX", group = "ADX")

[diplus, diminus, adx] = ta.dmi(17, lensig)
adxColor = input.color(defval = color.green, title = "ADX COLOR", group = "ADX")

ml = input.int(defval = 12, title = "Macd Line", inline = "MACD", group = "MACD")
sl = input.int(defval = 26, title = "signal Line", group = "MACD")
hl = input.int(defval = 9, title = "Hist Line", group = "MACD")
macdShow = input.bool(defval = true, title = "", inline = "MACD", group = "MACD")

[macdLine, signalLine, histLine] = ta.macd(close, ml, sl, hl)
macdLineColor = input.color(defval = color.blue, title = "MACD LINE COLOR", group = "MACD")
macdSignalColor =input.color(defval = color.orange, title = "MACD SIGNAL COLOR", group = "MACD")



scale(data)=>
    dataMax = ta.highest(data,len)
    dataMin = ta.lowest(data,len)
    scaleof = (data - dataMin) / (dataMax-dataMin) * (max - min) + min



last = 50
if rsiShow
    rS = label.new(bar_index, scale(rsi), text = "RSI : " + str.tostring(math.round_to_mintick(rsi)), textcolor = textColor, color = rsiColor )
    label.delete(rS[1])
plot(rsiShow ? scale(rsi) : na, show_last = last, offset = 0, color = rsiColor)
plot(rsiShow ?scale(rsiMa) : na, show_last = last, offset = 0, color = color.black)

if atrShow
    aS = label.new(bar_index - last, scale(atr), text = "ATR : " + str.tostring(math.round_to_mintick(atr)), textcolor = textColor, color = atrColor)
    label.delete(aS[1])
plot(atrShow ? scale(atr) : na, show_last = last, offset = -1 * last, color = atrColor)

if momShow
    mS = label.new(bar_index - last*2, scale(mom), text = "MOM : " + str.tostring(math.round_to_mintick(mom)), textcolor = textColor, color = momColor)
    label.delete(mS[1])
plot(momShow ? scale(mom) : na, show_last = last, offset = -2 * last, color = momColor)

if adxShow
    adS = label.new(bar_index - last*3, scale(adx), text = "ADX : " + str.tostring(math.round_to_mintick(adx)), textcolor = textColor, color = adxColor)
    label.delete(adS[1])
plot(adxShow ? scale(adx) : na, show_last = last, offset = -3 * last, color = adxColor)

if macdShow
    mcS = label.new(bar_index - last*4 - last/2, math.max(scale(macdLine)[last/2],scale(signalLine)[last/2]), text = "MACD" , textcolor = textColor, color = macdLineColor)
    label.delete(mcS[1])
plot(macdShow ? scale(macdLine) : na, show_last = last, offset = -4 * last, color = macdLineColor)
plot(macdShow ? scale(signalLine) : na, show_last = last, offset = -4 * last, color = macdSignalColor)


//////////////////////////////////////////////////////////////////////////////////////////////
//━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  AL SAT SİNYALİ  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
//////////////////////////////////////////////////////////////////////////////////////////////


// Inputs
a = input(1, title='Key Vaule. \'This changes the sensitivity\'')
c = input(10, title='ATR Period')
h = input(false, title='Signals from Heikin Ashi Candles')

xATR = ta.atr(c)
nLoss = a * xATR

kaynak = h ? request.security(ticker.heikinashi(syminfo.tickerid), timeframe.period, close, lookahead=barmerge.lookahead_off) : close

xATRTrailingStop = 0.0
iff_1 = kaynak > nz(xATRTrailingStop[1], 0) ? kaynak - nLoss : kaynak + nLoss
iff_2 = kaynak < nz(xATRTrailingStop[1], 0) and kaynak[1] < nz(xATRTrailingStop[1], 0) ? math.min(nz(xATRTrailingStop[1]), kaynak + nLoss) : iff_1
xATRTrailingStop := kaynak > nz(xATRTrailingStop[1], 0) and kaynak[1] > nz(xATRTrailingStop[1], 0) ? math.max(nz(xATRTrailingStop[1]), kaynak - nLoss) : iff_2

pos = 0
iff_3 = kaynak[1] > nz(xATRTrailingStop[1], 0) and kaynak < nz(xATRTrailingStop[1], 0) ? -1 : nz(pos[1], 0)
pos := kaynak[1] < nz(xATRTrailingStop[1], 0) and kaynak > nz(xATRTrailingStop[1], 0) ? 1 : iff_3

xcolor = pos == -1 ? color.red : pos == 1 ? color.green : color.blue

ema = ta.ema(kaynak, 1)
above = ta.crossover(ema, xATRTrailingStop)
below = ta.crossover(xATRTrailingStop, ema)

buy = kaynak > xATRTrailingStop and above
sell = kaynak < xATRTrailingStop and below

barbuy = kaynak > xATRTrailingStop
barsell = kaynak < xATRTrailingStop

plotshape(buy, title='Al', text='Al', style=shape.labelup, location=location.belowbar, color=color.new(color.green, 0), textcolor=color.new(color.white, 0), size=size.small)
plotshape(sell, title='Sat', text='Sat', style=shape.labeldown, location=location.abovebar, color=color.new(color.red, 0), textcolor=color.new(color.white, 0), size=size.small)

barcolor(barbuy ? color.rgb(9, 245, 16) : na)
barcolor(barsell ? color.red : na)

alertcondition(buy, 'UT Long', 'UT Long')
alertcondition(sell, 'UT Short', 'UT Short')

///////////////////////////////////////////////////
// TABLO AL SAT EKRANI //
///////////////////////////////////////////////////
// SMA
rsi_len = input.int(  14, title = "RSI Length",     group = "Indicators")
rsi_os  = input.float(15, title = "RSI Overbought", group = "Indicators")
rsi_ob  = input.float(85, title = "RSI Oversold",   group = "Indicators")

// TSI
tsi_long_len = input.int(   25, title = "TSI Long Length",   group = "Indicators")
tsi_shrt_len = input.int(   13, title = "TSI Short Length",  group = "Indicators")
tsi_ob       = input.float( 30, title = "TSI Overbought",    group = 'Indicators')
tsi_os       = input.float(-30, title = "TSI Oversold",      group = 'Indicators')

// ADX Params
adx_smooth   = input.int(  14, title = "ADX Smoothing",     group = 'Indicators')
adx_dilen    = input.int(  14, title = "ADX DI Length",     group = 'Indicators')
adx_level    = input.float(40, title = "ADX Level",         group = 'Indicators')

// SuperTrend
sup_atr_len = input.int(   10, "Supertrend ATR Length", group = 'Indicators')
sup_factor  = input.float(1, "Supertrend Factor",     group = 'Indicators')


/////////////
// SYMBOLS // 

u01 = input.bool(true,  title = "", group = 'Symbols', inline = 's01')
u02 = input.bool(true,  title = "", group = 'Symbols', inline = 's02')
u03 = input.bool(true,  title = "", group = 'Symbols', inline = 's03')
u04 = input.bool(true,  title = "", group = 'Symbols', inline = 's04')
u05 = input.bool(true,  title = "", group = 'Symbols', inline = 's05')
u06 = input.bool(true,  title = "", group = 'Symbols', inline = 's06')
u07 = input.bool(true,  title = "", group = 'Symbols', inline = 's07')
u08 = input.bool(true,  title = "", group = 'Symbols', inline = 's08')
u09 = input.bool(true,  title = "", group = 'Symbols', inline = 's09')
u10 = input.bool(true,  title = "", group = 'Symbols', inline = 's10')
u11 = input.bool(true,  title = "", group = 'Symbols', inline = 's11')
u12 = input.bool(true,  title = "", group = 'Symbols', inline = 's12')
u13 = input.bool(true,  title = "", group = 'Symbols', inline = 's13')
u14 = input.bool(true,  title = "", group = 'Symbols', inline = 's14')
u15 = input.bool(true,  title = "", group = 'Symbols', inline = 's15')
u16 = input.bool(true,  title = "", group = 'Symbols', inline = 's16')
u17 = input.bool(true,  title = "", group = 'Symbols', inline = 's17')
u18 = input.bool(true,  title = "", group = 'Symbols', inline = 's18')
u19 = input.bool(true,  title = "", group = 'Symbols', inline = 's19')
u20 = input.bool(true,  title = "", group = 'Symbols', inline = 's20')
u21 = input.bool(true,  title = "", group = 'Symbols', inline = 's21')
u22 = input.bool(true,  title = "", group = 'Symbols', inline = 's22')
u23 = input.bool(true,  title = "", group = 'Symbols', inline = 's23')
u24 = input.bool(true,  title = "", group = 'Symbols', inline = 's24')
u25 = input.bool(true,  title = "", group = 'Symbols', inline = 's25')
u26 = input.bool(true,  title = "", group = 'Symbols', inline = 's26')
u27 = input.bool(true,  title = "", group = 'Symbols', inline = 's27')
u28 = input.bool(true,  title = "", group = 'Symbols', inline = 's28')
u29 = input.bool(true,  title = "", group = 'Symbols', inline = 's29')
u30 = input.bool(true,  title = "", group = 'Symbols', inline = 's30')
u31 = input.bool(true,  title = "", group = 'Symbols', inline = 's31')
u32 = input.bool(true,  title = "", group = 'Symbols', inline = 's32')
u33 = input.bool(true,  title = "", group = 'Symbols', inline = 's33')
u34 = input.bool(true,  title = "", group = 'Symbols', inline = 's34')
u35 = input.bool(true,  title = "", group = 'Symbols', inline = 's35')
//u36 = input.bool(false, title = "", group = 'Symbols', inline = 's36')
//u37 = input.bool(false, title = "", group = 'Symbols', inline = 's37')
//u38 = input.bool(false, title = "", group = 'Symbols', inline = 's38')
//u39 = input.bool(false, title = "", group = 'Symbols', inline = 's39')
//u40 = input.bool(false, title = "", group = 'Symbols', inline = 's40')

s01 = input.symbol('MEXC:BTCUSDT.P',  group = 'Symbols', inline = 's01')
s02 = input.symbol('MEXC:KDAUSDT.P',  group = 'Symbols', inline = 's02')
s03 = input.symbol('MEXC:DOGEUSDT.P', group = 'Symbols', inline = 's03')
s04 = input.symbol('MEXC:SOLUSDT.P',  group = 'Symbols', inline = 's04')
s05 = input.symbol('MEXC:ZROUSDT.P',  group = 'Symbols', inline = 's05')
s06 = input.symbol('MEXC:AVAXUSDT.P',  group = 'Symbols', inline = 's06')
s07 = input.symbol('MEXC:HYPEUSDT.P', group = 'Symbols', inline = 's07')
s08 = input.symbol('MEXC:NMRUSDT.P',  group = 'Symbols', inline = 's08')
s09 = input.symbol('MEXC:PAXGUSDT.P',   group = 'Symbols', inline = 's09')
s10 = input.symbol('MEXC:HIFIUSDT.P',   group = 'Symbols', inline = 's10')
s11 = input.symbol('MEXC:ADAUSDT.P',  group = 'Symbols', inline = 's11')
s12 = input.symbol('MEXC:SUIUSDT.P',  group = 'Symbols', inline = 's12')
s13 = input.symbol('MEXC:WIFUSDT.P',   group = 'Symbols', inline = 's13')
s14 = input.symbol('MEXC:FARTCOINUSDT.P', group = 'Symbols', inline = 's14')
s15 = input.symbol('MEXC:RUNEUSDT.P', group = 'Symbols', inline = 's15')
s16 = input.symbol('MEXC:BMTUSDT.P',   group = 'Symbols', inline = 's16')
s17 = input.symbol('MEXC:TIAUSDT.P', group = 'Symbols', inline = 's17')
s18 = input.symbol('MEXC:WLDUSDT.P', group = 'Symbols', inline = 's18')
s19 = input.symbol('MEXC:ONDOUSDT.P', group = 'Symbols', inline = 's19')
s20 = input.symbol('MEXC:OPUSDT.P', group = 'Symbols', inline = 's20')
s21 = input.symbol('MEXC:FETUSDT.P', group = 'Symbols', inline = 's21')
s22 = input.symbol('MEXC:BOMEUSDT.P', group = 'Symbols', inline = 's22')
s23 = input.symbol('MEXC:JTOUSDT.P', group = 'Symbols', inline = 's23')
s24 = input.symbol('MEXC:PORTALUSDT.P', group = 'Symbols', inline = 's24')
s25 = input.symbol('MEXC:1INCHUSDT.P', group = 'Symbols', inline = 's25')
s26 = input.symbol('MEXC:JASMYUSDT.P', group = 'Symbols', inline = 's26')
s27 = input.symbol('MEXC:CELRUSDT.P', group = 'Symbols', inline = 's27')
s28 = input.symbol('MEXC:NEIROETHUSDT.P', group = 'Symbols', inline = 's28')
s29 = input.symbol('MEXC:SANDUSDT.P', group = 'Symbols', inline = 's29')
s30 = input.symbol('MEXC:ROSEUSDT.P', group = 'Symbols', inline = 's30')
s31 = input.symbol('MEXC:NOTUSDT.P', group = 'Symbols', inline = 's31')
s32 = input.symbol('MEXC:PEOPLEUSDT.P', group = 'Symbols', inline = 's32')
s33 = input.symbol('MEXC:ARKMUSDT.P', group = 'Symbols', inline = 's33')
s34 = input.symbol('MEXC:ETHUSDT.P', group = 'Symbols', inline = 's34')
s35 = input.symbol('MEXC:APEUSDT.P', group = 'Symbols', inline = 's35')

//s36 = input.symbol('avod', group = 'Symbols', inline = 's36')
//s37 = input.symbol('huner',   group = 'Symbols', inline = 's37')
//s38 = input.symbol('kcaer',   group = 'Symbols', inline = 's38')
//s39 = input.symbol('ıeyho',  group = 'Symbols', inline = 's39')
//s40 = input.symbol('asgyo',  group = 'Symbols', inline = 's40')

//////////////////
// CALCULATIONS //

// Get only symbol
only_symbol(s) => 
    array.get(str.split(s, ":"), 1)


id_symbol(s)=>
    switch s
        1  => only_symbol(s01)
        2  => only_symbol(s02)
        3  => only_symbol(s03)
        4  => only_symbol(s04)
        5  => only_symbol(s05)
        6  => only_symbol(s06)
        7  => only_symbol(s07)
        8  => only_symbol(s08)
        9  => only_symbol(s09)
        10 => only_symbol(s10)
        11 => only_symbol(s11)
        12 => only_symbol(s12)
        13 => only_symbol(s13)
        14 => only_symbol(s14)
        15 => only_symbol(s15)
        16 => only_symbol(s16)
        17 => only_symbol(s17)
        18 => only_symbol(s18)
        19 => only_symbol(s19)
        20 => only_symbol(s20)
        21 => only_symbol(s21)
        22 => only_symbol(s22)
        23 => only_symbol(s23)
        24 => only_symbol(s24)
        25 => only_symbol(s25)
        26 => only_symbol(s26)
        27 => only_symbol(s27)
        28 => only_symbol(s28)
        29 => only_symbol(s29)
        30 => only_symbol(s30)
        31 => only_symbol(s31)
        32 => only_symbol(s32)
        33 => only_symbol(s33)
        34 => only_symbol(s34)
        35 => only_symbol(s35)
        //36 => only_symbol(s36)
        //37 => only_symbol(s37)
        //38 => only_symbol(s38)
        //39 => only_symbol(s39)
        //40 => only_symbol(s40)
        => na


// for TSI
double_smooth(src, long, short) =>
	fist_smooth = ta.ema(src, long)
	ta.ema(fist_smooth, short)

// ADX
dirmov(len) =>
	up   =  ta.change(high)
	down = -ta.change(low)
	
	plusDM  = na(up)   ? na : (up > down and up > 0   ? up   : 0)
	minusDM = na(down) ? na : (down > up and down > 0 ? down : 0)
	
	truerange = ta.rma(ta.tr, len)
	
	plus  = fixnan(100 * ta.rma(plusDM, len)  / truerange)
	minus = fixnan(100 * ta.rma(minusDM, len) / truerange)
	
	[plus, minus]
	
adx_func(dilen, adxlen) =>
	[plus, minus] = dirmov(dilen)
	sum = plus + minus
	adx = 100 * ta.rma(math.abs(plus - minus) / (sum == 0 ? 1 : sum), adxlen)

screener_func() =>
    
    // RSI
    rsi = ta.rsi(close, rsi_len)
    
    // TSI
    pc = ta.change(close)

    double_smoothed_pc     = double_smooth(pc,           tsi_long_len, tsi_shrt_len)
    double_smoothed_abs_pc = double_smooth(math.abs(pc), tsi_long_len, tsi_shrt_len)
    
    tsi = 100 * (double_smoothed_pc / double_smoothed_abs_pc)
    
    // ADX 
    adx = adx_func(adx_dilen, adx_smooth)
    
    // Supertrend
    [sup_value, sup_dir] = ta.supertrend(sup_factor, sup_atr_len)
    
    [math.round_to_mintick(close), rsi, tsi, adx, sup_dir]

// Set Up Matrix
screenerMtx = matrix.new<float>(0, 6, na)

screenerFun(numSym, sym, flg) =>

    [cl, rsi, tsi, adx, sup] = request.security(sym, timeframe.period, screener_func())

    arr = array.from(numSym, cl, rsi, tsi, adx, sup)

    if flg 
        matrix.add_row(screenerMtx, matrix.rows(screenerMtx), arr)

// Security call
screenerFun(01, s01, u01),  screenerFun(02, s02, u02),  screenerFun(03, s03, u03),  screenerFun(04, s04, u04),  
screenerFun(05, s05, u05),  screenerFun(06, s06, u06),  screenerFun(07, s07, u07),  screenerFun(08, s08, u08),  
screenerFun(09, s09, u09),  screenerFun(10, s10, u10),  screenerFun(11, s11, u11),  screenerFun(12, s12, u12),
screenerFun(13, s13, u13),  screenerFun(14, s14, u14),  screenerFun(15, s15, u15),  screenerFun(16, s16, u16),  
screenerFun(17, s17, u17),  screenerFun(18, s18, u18),  screenerFun(19, s19, u19),  screenerFun(20, s20, u20),  
screenerFun(21, s21, u21),  screenerFun(22, s22, u22),  screenerFun(23, s23, u23),  screenerFun(24, s24, u24),  
screenerFun(25, s25, u25),  screenerFun(26, s26, u26),  screenerFun(27, s27, u27),  screenerFun(28, s28, u28),  
screenerFun(29, s29, u29),  screenerFun(30, s30, u30),  screenerFun(31, s31, u31),  screenerFun(32, s32, u32),  
screenerFun(33, s33, u33),  screenerFun(34, s34, u34),  screenerFun(35, s35, u35),  //screenerFun(36, s36, u36),
//screenerFun(37, s37, u37),  //screenerFun(38, s38, u38),  //screenerFun(39, s39, u39),  //screenerFun(40, s40, u40),

///////////
// PLOTS //

var tbl = table.new(position.top_right, 6, 41, frame_color=#151715, frame_width=1, border_width=2, border_color=color.new(color.white, 100))

if barstate.islast
    table.clear(tbl, 0, 0, 5, 40)

    table.cell(tbl, 0, 0, 'Symbol',     text_halign = text.align_center, bgcolor = #f7af06, text_color = color.white, text_size = size.normal)
    table.cell(tbl, 1, 0, 'Price',      text_halign = text.align_center, bgcolor = #f7af06, text_color = color.white, text_size = size.normal)
    table.cell(tbl, 2, 0, 'RSI',        text_halign = text.align_center, bgcolor = #f7af06, text_color = color.white, text_size = size.normal)
    table.cell(tbl, 3, 0, 'TSI',        text_halign = text.align_center, bgcolor = #f7af06, text_color = color.white, text_size = size.normal)
    table.cell(tbl, 4, 0, 'ADX',        text_halign = text.align_center, bgcolor = #f7af06, text_color = color.white, text_size = size.normal)
    table.cell(tbl, 5, 0, 'Supertrend', text_halign = text.align_center, bgcolor = #f7af06, text_color = color.white, text_size = size.normal)

    if matrix.rows(screenerMtx) > 0
        for i = 0 to matrix.rows(screenerMtx) - 1
            
            rsi_col  = matrix.get(screenerMtx, i, 2) > rsi_ob    ? color.red   : matrix.get(screenerMtx, i, 2) < rsi_os ? color.green : #aaaaaa 
            tsi_col  = matrix.get(screenerMtx, i, 3) > tsi_ob    ? color.red   : matrix.get(screenerMtx, i, 3) < tsi_os ? color.green : #aaaaaa  
            adx_col  = matrix.get(screenerMtx, i, 4) > adx_level ? color.green : #aaaaaa 
        
            sup_text = matrix.get(screenerMtx, i, 5) > 0 ? "SAT"      : "AL"
            sup_col  = matrix.get(screenerMtx, i, 5) < 0 ? color.green : color.red
            
            table.cell(tbl, 0, i + 1, id_symbol(matrix.get(screenerMtx, i, 0)),                                      text_halign = text.align_left,   bgcolor = #efa339, text_color = #3f3f3f, text_size = size.normal)
            table.cell(tbl, 1, i + 1, str.tostring(matrix.get(screenerMtx, i, 1)),         text_halign = text.align_center, bgcolor = #aaaaaa,    text_color = color.white, text_size = size.normal)
            table.cell(tbl, 2, i + 1, str.tostring(matrix.get(screenerMtx, i, 2), "#.##"), text_halign = text.align_center, bgcolor = rsi_col,    text_color = color.white, text_size = size.normal)
            table.cell(tbl, 3, i + 1, str.tostring(matrix.get(screenerMtx, i, 3), "#.##"), text_halign = text.align_center, bgcolor = tsi_col,    text_color = color.white, text_size = size.normal)
            table.cell(tbl, 4, i + 1, str.tostring(matrix.get(screenerMtx, i, 4), "#.##"), text_halign = text.align_center, bgcolor = adx_col   , text_color = color.white, text_size = size.normal)
            table.cell(tbl, 5, i + 1, sup_text,                                            text_halign = text.align_center, bgcolor = sup_col,    text_color = color.white, text_size = size.normal)



