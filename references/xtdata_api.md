# xtdata API 详细参考

## Table of Contents

- [1. 通用规则](#1-通用规则)
- [2. 订阅与反订阅](#2-订阅与反订阅)
- [3. 行情读取接口](#3-行情读取接口)
- [4. 历史数据下载接口](#4-历史数据下载接口)
- [5. 财务数据接口](#5-财务数据接口)
- [6. 基础信息接口](#6-基础信息接口)
- [7. 交易日历与辅助接口](#7-交易日历与辅助接口)
- [8. 返回数据形态与处理建议](#8-返回数据形态与处理建议)

## 1. 通用规则

### 股票代码格式

- 格式：`{代码}.{市场}`
- 示例：`000001.SZ`、`600000.SH`、`000300.SH`

### period 周期

- 分笔：`tick`
- 分钟线：`1m` `3m` `5m` `15m` `30m` `1h`
- 日周月季半年年：`1d` `1w` `1mon` `1q` `1hy` `1y`

### dividend_type 复权

- `none`：不复权
- `front`：前复权
- `back`：后复权
- `front_ratio`：等比前复权
- `back_ratio`：等比后复权

## 2. 订阅与反订阅

### `subscribe_quote`

```python
subscribe_quote(stock_code, period='1d', start_time='', end_time='', count=0, callback=None)
```

- `stock_code`：单个标的代码
- `count`：`-1` 全部，`0` 仅订阅不取历史
- `callback`：回调函数 `on_data(datas)`
- 返回：订阅号 `seq`（成功 > 0，失败 -1）

### `subscribe_whole_quote`

```python
subscribe_whole_quote(code_list, callback=None)
```

- `code_list` 可传：
  - 市场代码：`['SH', 'SZ']`
  - 指定标的：`['600000.SH', '000001.SZ']`

### `unsubscribe_quote`

```python
unsubscribe_quote(seq)
```

- `seq` 为订阅时返回值

### `run`

```python
run()
```

- 阻塞当前线程，持续接收回调

## 3. 行情读取接口

### `get_market_data_ex`（推荐）

```python
get_market_data_ex(field_list=[], stock_list=[], period='1d', start_time='', end_time='', count=-1, dividend_type='none', fill_data=True)
```

- 推荐优先使用该接口
- `field_list=[]` 表示读取全部字段
- `fill_data=True` 时按时间轴补齐空缺
- 返回：
  - K线周期：`dict[field] -> DataFrame`
  - tick 周期：`dict[stock_code] -> ndarray/list-like`

### `get_market_data`（旧接口）

```python
get_market_data(field_list=[], stock_list=[], period='1d', start_time='', end_time='', count=-1, dividend_type='none', fill_data=True)
```

- 能力与 `get_market_data_ex` 接近，建议迁移到 `get_market_data_ex`

### `get_local_data`

```python
get_local_data(field_list=[], stock_list=[], period='1d', start_time='', end_time='', count=-1, dividend_type='none', fill_data=True, data_dir=None)
```

- 从本地数据目录读取
- 常用于批量历史数据读取与离线处理

### `get_full_tick`

```python
get_full_tick(code_list)
```

- 返回：`dict[stock_code] -> tick_data`
- 适合获取最新盘口快照（如买一卖一）

### `get_divid_factors`

```python
get_divid_factors(stock_code, start_time='', end_time='')
```

- 返回：`pd.DataFrame`
- 用于复权因子与除权除息处理

## 4. 历史数据下载接口

### `download_history_data`

```python
download_history_data(stock_code, period, start_time='', end_time='', incrementally=None)
```

- `incrementally=True`：增量下载
- `incrementally=False`：全量下载
- `incrementally=None`：由时间参数推断

### `download_history_data2`

```python
download_history_data2(stock_list, period, start_time='', end_time='', callback=None, incrementally=None)
```

- 批量下载
- `callback` 进度字段常见：`finished` `total` `stockcode` `message`

## 5. 财务数据接口

### `get_financial_data`

```python
get_financial_data(stock_list, table_list=[], start_time='', end_time='', report_type='report_time')
```

常见 `table_list`：

- `Balance`（资产负债表）
- `Income`（利润表）
- `CashFlow`（现金流量表）
- `Capital`（股本表）
- `Holdernum`（股东数）
- `Top10holder`（十大股东）
- `Top10flowholder`（十大流通股东）
- `Pershareindex`（每股指标）

### `download_financial_data`

```python
download_financial_data(stock_list, table_list=[])
```

### `download_financial_data2`

```python
download_financial_data2(stock_list, table_list=[], start_time='', end_time='', callback=None)
```

## 6. 基础信息接口

### `get_instrument_detail`

```python
get_instrument_detail(stock_code, iscomplete=False)
```

常用返回字段：

- `InstrumentID` `InstrumentName` `ExchangeID`
- `PreClose` `UpStopPrice` `DownStopPrice`
- `FloatVolume` `TotalVolume`
- `PriceTick` `VolumeMultiple`
- `CreateDate` `OpenDate`

### `get_instrument_type`

```python
get_instrument_type(stock_code)
```

常见返回布尔字段：`index` `stock` `fund` `etf` ...

### 板块/指数相关

```python
download_sector_data()
get_sector_list()
get_stock_list_in_sector(sector_name, real_timetag=False)
get_index_weight(index_code)
download_index_weight()
```

## 7. 交易日历与辅助接口

```python
get_trading_dates(market, start_time='', end_time='', count=-1)
get_trading_calendar(market, start_time='', end_time='')
get_holidays()
download_holiday_data()
get_trading_time(market, date='')
get_cb_info(stockcode)
download_cb_data()
get_etf_info()
download_etf_info()
get_ipo_info(start_time, end_time)
```

### 连接管理

```python
connect(ip='', port=0, session_id=0)
reconnect(ip='', port=0)
```

## 8. 返回数据形态与处理建议

### K线读取常见模式

```python
data = xtdata.get_market_data_ex(
    field_list=["open", "high", "low", "close", "volume"],
    stock_list=["000001.SZ", "600000.SH"],
    period="1d",
    count=20,
)

close_df = data["close"]
```

### tick快照常见模式

```python
tick = xtdata.get_full_tick(["000001.SZ"])
t = tick["000001.SZ"]
last = t["lastPrice"]
bid1 = t["bidPrice"][0]
ask1 = t["askPrice"][0]
```

### 使用建议

- 历史数据读取前先补数据：`download_history_data*`
- 大量标的优先批量下载：`download_history_data2`
- 实时处理使用订阅回调 + 轻量逻辑
- 重计算逻辑在回调外执行，避免阻塞数据线程
