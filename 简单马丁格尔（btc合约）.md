
> 策略名称

简单马丁格尔（btc合约）

> 策略作者

忠^L

> 策略描述

！！！！！！！温馨提示，本策略仅供学习使用，用于实盘必定爆仓！！！！！！！！！！
！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！
简单马丁格尔
原理就是输了加倍，直到赢到期望的收益为止
因为是回测用的，所有下单都是市价单，实盘没跑过！！

2022.4.19修改:根据EMA144判断走低于均线则优先做空，高于均线则做多



> 源码 (javascript)

``` javascript
/*
本策略仅供学习使用，用于实盘后果自负 
*/


var n = 0.001 //初始下单数
var MarginLevel = 50 //合约杠杆 
var profit = 0.1 //期望收益 ，不能小于手续费 


//取随机数 
function sum(m, n) {
    var num = Math.floor(Math.random() * (m - n) + n);
    return num;
}

function main() {
    exchange.SetContractType("swap")
    exchange.SetMarginLevel(MarginLevel)
    var position = []
    while (true) {
        position = exchange.GetPosition()
        if (position.length == 0) {
            
            var records = exchange.GetRecords(PERIOD_H1)
            // 判断K线bar数量是否满足指标计算周期
            var ema = TA.EMA(records, 144)
            var ema144 = ema[ema.length - 1]
            var ticker = exchange.GetTicker()
            var nowPrice = ticker.Last
            Log(ema144)

            
            if (nowPrice <ema144) {
                exchange.SetDirection("sell")
                exchange.Sell(-1, n, "开空")
            }
            if (nowPrice > ema144) {
                exchange.SetDirection("buy")
                exchange.Buy(-1, n, "开多")
            }

        }
        if (position.length > 0) {

            if (position[0].Type == 0) {
                //盈利大于期望 
                if (position[0].Profit > profit) {
                    exchange.SetDirection("closebuy")
                    exchange.Sell(-1, position[0].Amount)
                }
                //负盈利大于保证金 则加仓

                if (position[0].Profit < position[0].Margin * -1) {
                    exchange.SetDirection("buy")
                    exchange.Buy(-1, position[0].Amount)
                }
            }
            if (position[0].Type == 1) {
                if (position[0].Profit > profit) {
                    exchange.SetDirection("closesell")
                    exchange.Buy(-1, position[0].Amount)
                }
                if (position[0].Profit < position[0].Margin * -1) {
                    exchange.SetDirection("sell")
                    exchange.Sell(-1, position[0].Amount)
                }
            }
            Sleep(60000)
        }
    }

}
```

> 策略出处

https://www.fmz.com/strategy/270689

> 更新时间

2022-04-24 23:05:35
