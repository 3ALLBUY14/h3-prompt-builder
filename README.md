# h3-prompt-builder

MiniMax H3 视频提示词生成 skill（适用于 WorkBuddy / Claude Code 等 AI agent）。

- 覆盖 5 种生成模式：T2VA / I2VA / FL2VA / L2VA / Ref2VA
- 10 种风格模板，以及运镜 / 对白 / 音频规则
- 数字人对口型防火星语（短音频音色参考优先、台词归属三选一）
- 冗余压缩优化标准（六大压缩原则 + 字段级精简指南）
- 官方使用手册增量提炼：完整输入规格与硬限制、三段式提示词公式、镜头拆解规则、视频编辑模式（角色/背景/台词替换与高精度指令）、出海多语种对白、商用场景十类选型、文字乱码解法
- 常见坑对策与质量自检清单
- 基础规格：时长 4–15s、分辨率 768p/1440p、24fps、原生双声道

## 目录

- `SKILL.md` — 主流程指南
- `references/mode-formats.md` — 各模式格式规范
- `references/prompt-examples.md` — 提示词示例
- `references/style-guide.md` — 风格模板
- `references/optimization-guide.md` — 提示词精简优化标准
- `references/lipsync-guide.md` — 数字人对口型实战指南
- `references/official-updates.md` — 官方使用手册增量提炼（2026-09）

## 安装

将本仓库内容放入 agent 的 skills 目录，例如：

```
~/.workbuddy/skills/h3-prompt-builder/
```

版本：v2.3.0
