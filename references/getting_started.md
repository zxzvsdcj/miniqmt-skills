# MiniQMT / XtQuant Getting Started

## 1) 环境与依赖

- Python: 64-bit `3.6` ~ `3.12`
- 终端: 已安装并登录 MiniQMT 客户端
- 包安装:

```bash
pip install xtquant
```

## 2) 路径与会话规则

### 客户端路径

```python
# 券商端
path = r"D:\\迅投极速交易终端 睿智融科版\\userdata_mini"

# 投研端
path = r"D:\\迅投极速交易终端 睿智融科版\\userdata"
```

### session_id 规则

- 不同策略进程使用不同 `session_id`
- 推荐 `session_id = int(time.time())`
- 相同 `session_id` 的重复 connect 建议间隔 > 3 秒
- 过度创建新 session 会在数据目录产生额外对接文件

## 3) 核心模块

- `xtdata`: 行情模块（历史/实时 K 线、分笔、财务、基础信息、板块）
- `xttrader`: 交易模块（下单、撤单、资产/持仓/成交查询、回调推送）

## 4) 最小行情流程（xtdata）

```python
from xtquant import xtdata

# 1. 下载历史数据到本地
xtdata.download_history_data("000001.SZ", period="1d", incrementally=True)

# 2. 获取行情数据
data = xtdata.get_market_data_ex(
    field_list=["open", "high", "low", "close", "volume"],
    stock_list=["000001.SZ"],
    period="1d",
    count=10,
)

print(data["close"])
```

## 5) 最小交易流程（xttrader）

```python
import time
from xtquant.xttrader import XtQuantTrader, XtQuantTraderCallback
from xtquant.xttype import StockAccount
from xtquant import xtconstant

path = r"D:\\迅投极速交易终端 睿智融科版\\userdata_mini"
session_id = int(time.time())

class MyCallback(XtQuantTraderCallback):
    def on_stock_order(self, order):
        print("order:", order.order_id, order.order_status)

    def on_stock_trade(self, trade):
        print("trade:", trade.order_id, trade.traded_price, trade.traded_volume)

trader = XtQuantTrader(path, session_id)
account = StockAccount("YOUR_ACCOUNT", "STOCK")

trader.register_callback(MyCallback())
trader.start()

if trader.connect() != 0:
    raise RuntimeError("connect failed")

if trader.subscribe(account) != 0:
    raise RuntimeError("subscribe failed")

order_id = trader.order_stock(
    account,
    "600000.SH",
    xtconstant.STOCK_BUY,
    100,
    xtconstant.FIX_PRICE,
    10.5,
    "demo_strategy",
    "demo",
)
print("order_id:", order_id)
```

## 6) 实时行情回调流程

```python
from xtquant import xtdata


def on_data(datas):
    for stock, rows in datas.items():
        print(stock, rows[0].get("lastPrice"))

seq = xtdata.subscribe_quote("000001.SZ", period="tick", count=0, callback=on_data)
print("seq:", seq)

# 使用回调模式时需阻塞
xtdata.run()
```

## 7) 运行顺序建议

1. 启动并登录 MiniQMT
2. 准备 `path` + `session_id`
3. 行情任务先补历史数据，再读数据
4. 交易任务先 `start -> connect -> subscribe`
5. 回调模式使用 `run()` 或 `run_forever()` 保持进程存活

## 8) 下一步阅读

- 想查函数签名：`api.md`
- 只做行情：`xtdata_api.md`
- 只做交易：`xttrader_api.md`
- 看字段定义：`data_structures.md`
- 复制模板直接跑：`examples.md`
- 处理报错：`troubleshooting.md`
