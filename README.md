# miniqmt-skills

MiniQMT / XtQuant 技能库，覆盖：

- `xtdata` 行情 API（历史、实时、财务、板块、交易日历）
- `xttrader` 交易 API（下单、撤单、查询、回调）
- 字段字典与常量速查
- 实战模板与排错指南

## 安装

```bash
git clone https://github.com/nnquant/miniqmt-skills.git
cp -r miniqmt-skills ~/.claude/skills/miniqmt-skill
```

或手动复制到：`~/.claude/skills/miniqmt-skill`

## 文档结构

- `SKILL.md`：主技能入口（触发描述 + 工作流）
- `references/index.md`：导航索引
- `references/getting_started.md`：环境与最小启动
- `references/api.md`：完整接口索引
- `references/xtdata_api.md`：行情接口详情
- `references/xttrader_api.md`：交易接口详情
- `references/data_structures.md`：字段与对象结构
- `references/examples.md`：可运行示例
- `references/troubleshooting.md`：常见问题排查
