# MiniQMT References Index

## Quick Routing

| Task | Read First | Then |
|---|---|---|
| 环境配置、路径、会话启动 | `getting_started.md` | `troubleshooting.md` |
| 查某个 API 在哪里、怎么调 | `api.md` | `xtdata_api.md` / `xttrader_api.md` |
| 行情模块 (xtdata) | `xtdata_api.md` | `examples.md` |
| 交易模块 (xttrader) | `xttrader_api.md` | `examples.md` |
| 字段含义/对象结构/常量值 | `data_structures.md` | `xtdata_api.md` / `xttrader_api.md` |
| 快速抄可运行模板 | `examples.md` | `troubleshooting.md` |
| 报错排查与生产注意事项 | `troubleshooting.md` | `getting_started.md` |

## Reference Files

- `getting_started.md`: 环境要求、路径规则、最小可运行流程。
- `api.md`: 完整 API 接口索引（函数名级别）。
- `xtdata_api.md`: 行情模块接口、参数、返回数据形态。
- `xttrader_api.md`: 交易模块接口、回调和查询流。
- `data_structures.md`: 行情字段、财务字段、交易对象和常量。
- `examples.md`: 实战模板（订阅、下单、批量数据、复权）。
- `troubleshooting.md`: 常见问题、根因、修复策略。

## Search Shortcuts

Use ripgrep for pinpoint lookup:

```bash
rg "subscribe_quote\(" references
rg "order_stock\(" references
rg "query_stock_positions\(" references
rg "XtQuantTraderCallback" references
rg "askPrice|bidPrice|askVol|bidVol" references
```

## Canonical Source

This reference set is consolidated from the full XtQuant/MiniQMT documentation (xtdata + xttrader), including API interfaces, data structures, field definitions, and practical examples.
