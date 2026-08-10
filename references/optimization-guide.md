# H3 视频提示词精简优化标准 —— 从冗余到精准

> **来源**：基于《陪玩团成员炫酷宣传》v4.0（~5380 词）→ v4.1（~2235 词，**-58%**）的实战压缩经验提炼。
> **适用**：H3 Ref2VA 六段式提示词（也适用于 Base 系三字段格式）。
> **目标**：在不丢失任何信息的前提下，消除冗余，提升模型理解力。
> **定位**：本标准是 `mode-formats.md`、`style-guide.md`、`prompt-examples.md` 的**进阶补充**，解决前三者未覆盖的"信息冗余诊断与压缩"问题。前三者定义"写什么、怎么写"，本标准定义"写过的怎么精简"。

---

## 一、为什么冗余会损害提示词效果

| 问题 | 表现 | 后果 |
|------|------|------|
| **注意力稀释** | 同一规则重复 8+ 次 | 模型将重复内容降权，关键指令被淹没 |
| **上下文窗口浪费** | 5380 词中 ~3000 词是重复 | 有效信息密度低，模型"抓不住重点" |
| **修改困难** | 改一个角色配色要在 4-5 处同步修改 | 遗漏一处即产生矛盾指令 |
| **矛盾风险** | 不同位置描述同一属性时措辞微偏 | 模型收到矛盾信号，输出不稳定 |
| **理解力下降** | 模型需要整合散落在各处的同一信息 | 认知负担增加，执行精度下降 |

**核心结论**：提示词不是"说得越多越强调"，而是"说得越准越有效"。

---

## 二、六大压缩原则

### 原则 1：单一信息源（SSOT — Single Source of Truth）
> 中文别名：**一处定义，处处引用**

**每条信息只在一个最权威的位置完整定义一次，其他位置只引用不复述。**

#### 反模式
```
subject_definitions:  → 完整描述 S1 的刘海、眼睛、服装、配色、粒子、动作
summary:              → 再次完整列出 S1 的配色、粒子、动作
retention_analysis:   → 再次描述 S1 的刘海、眼睛、服装
detailed_description: → 再次完整描述 S1 的所有特征 + 动作 + 粒子
[Shot 8]:             → 再次完整描述 S1 的所有特征 + 动作 + 粒子
```
同一信息出现 **4-5 次**，词数膨胀 ~300%。

#### 正确做法
```
subject_definitions:  → 完整定义 S1 的所有属性（唯一权威源）
summary:              → "7 distinct real women" 概括，不逐人复述
retention_analysis:   → "fully_preserved per <Picture 1>"，不重复特征
detailed_description: → "(BLUNT BANGS, CREW-NECK, ICY-CYAN)" 短标签引用
[Shot 8]:             → 同上短标签 + 仅补充布局新信息
```
同一信息出现 **1 次定义 + N 次引用**。

#### 操作规则

| 字段 | 定位 | 允许操作 |
|------|------|----------|
| `subject_definitions` | **唯一权威定义源** | 完整描述所有属性 |
| `summary` | 高层概览 | 只写结构/数量/风格，不逐项复述 |
| `retention_analysis` | 保留声明 | `fully_preserved per <Picture N>` 即可 |
| `detailed_description` | 执行指令 | 用短标签引用 + 只写本镜头独有信息 |
| 末幕/群像镜头 | 布局指令 | 短标签引用 + 只写布局/汇聚等新信息 |

---

### 原则 2：引用标签化（Tag-Based Referencing）
> 中文别名：**用短名，不抄全文**

**用 2-4 个大写关键词替代整段复述，让模型通过标签回溯到定义源。**

#### 反模式
```
[Shot 8] TOP-FAR-LEFT: <Subject 1> from <Picture 1> — straight black long hair
with BLUNT BANGS, NARROW ELONGATED SINGLE-FOLD EYES, cold high-cold gaze, oval
face, pale-pink lips, fair skin, CLOSED BLACK CREW-NECK KNIT, ICY-CYAN rim-light,
frost-crystal shards — doing her headphone-sweep action.
```

#### 正确做法
```
[Shot 8] TOP-FAR-LEFT: <S1> icy-cyan frost-shards headphone-sweep
```

#### 标签设计规则

| 标签类型 | 格式 | 示例 |
|----------|------|------|
| 身份标签 | `<SN>` 或 `<Subject N>` | `<S1>` |
| 视觉锚标签 | 2-3 个最独特特征，大写 | `BLUNT BANGS, CREW-NECK` |
| 配色标签 | 颜色名，连字符 | `icy-cyan` |
| 粒子标签 | 粒子效果名 | `frost-shards` |
| 动作标签 | 签名动作缩写 | `headphone-sweep` |

**关键**：标签不需要包含全部信息，只需包含**足够回溯到定义源的唯一性锚点**。模型在读取 `[Shot 8] <S1> icy-cyan frost-shards headphone-sweep` 时，会自动回溯 `subject_definitions` 中 S1 的完整定义。

---

### 原则 3：共享规则合并（Shared Rule Consolidation）
> 中文别名：**共同规则只写一次**

**所有角色/镜头共享的规则写一次，放在 `[SHARED GUARDRAILS]` 中，永不重复。**

#### 反模式
```
[SHARED GUARDRAILS] ... THICK BRIGHT RIM-LIGHT ... (1次)
<Subject 1> ... THICK BRIGHT rim-light ... (2次)
<Subject 2> ... THICK BRIGHT rim-light ... (3次)
...
<Subject 7> ... THICK BRIGHT rim-light ... (8次)
summary: ... THICK BRIGHT rim-light ... (9次)
detailed_description: ... THICK BRIGHT rim-light ... (10次)
[Shot 1] ... THICK BRIGHT rim-light ... (11次)
```

#### 正确做法
```
[SHARED GUARDRAILS] Each wears a THICK BRIGHT CONTINUOUS signature-color
RIM-LIGHT OUTLINE — DOMINANT lighting, ALWAYS VISIBLE, NEVER fades.
```
全文只出现 **1 次**。各 Subject 只写自己的配色，不再重复 rim-light 规则。

#### 常见共享规则清单

以下规则如果适用于所有角色/镜头，**只在 `[SHARED GUARDRAILS]` 写一次**：

- 光照规则（rim-light / back-light / smoke）
- 全局调色（冷调 / 暖调 / 去饱和）
- 角色静默规则
- 无旁白规则
- 镜头距离规则（MEDIUM / NO close-up）
- 音乐驱动规则
- 速度 ramps 结构
- 文字规则（无文字 / 仅末幕有文字）

---

### 原则 4：反模式指令压缩（Anti-Pattern Compression）
> 中文别名：**防错指令集中放**

**防错指令集中为一个独立规则块，不分散到每个角色/镜头中重复。**

#### 反模式
```
<Subject 1> ... [SHOT ASSIGNMENT: Shot 1 + Shot 8. NEVER skip her, NEVER
replace her with another subject.]
<Subject 2> ... [SHOT ASSIGNMENT: Shot 2 + Shot 8. NEVER skip her, NEVER
replace her with another subject.]
...（重复 7 次）
[Shot 8] ... ANTI-DUPLICATE DIRECTIVE: It is ABSOLUTELY FORBIDDEN to show
the same woman at two positions... EXACTLY SEVEN... NO MORE, NO LESS...
```
反漏人指令出现 **20+ 次**，词数 ~800 词。

#### 正确做法
```
[APPEARANCE RULES] Shots 1-7: each shows ONLY its assigned <Subject N> —
NO skipping, NO substitution. Shot 8: ALL 7 appear SIMULTANEOUSLY —
EXACTLY SEVEN, NO duplicates, NO extras.
```
反漏人指令出现 **1 次**，~30 词。

#### 操作规则

| 防错类型 | 反模式做法 | 正确做法 |
|----------|-----------|----------|
| 防漏人 | 每人附 "NEVER skip" | 一个 `[APPEARANCE RULES]` 块覆盖全部 |
| 防重复 | 末幕逐位置写 "NO DUPLICATES" | 末幕开头一句 "EXACTLY SEVEN, NO duplicates" |
| 防换人 | 每人附 "NEVER replace" | 归入 `[APPEARANCE RULES]` |
| 防多/少人 | 末幕逐行强调 | 末幕一句 "NOT six, NOT eight, EXACTLY SEVEN" |

**核心**：模型不需要你重复 20 次"不要漏人"才会不漏人。一次清晰、绝对化的规则声明，远比 20 次分散重复有效。

---

### 原则 5：强调词降级（Emphasis De-escalation）
> 中文别名：**强调词是稀缺资源，少用更有效**

**减少 CRITICAL / ABSOLUTE / MUST / FORBIDDEN 的滥用；一次清晰声明 > 十次大写强调。**

#### 反模式
```
[MANDATORY APPEARANCE RULES] CRITICAL — THESE RULES ARE ABSOLUTE AND MUST
BE FOLLOWED WITHOUT EXCEPTION: ... It is ABSOLUTELY FORBIDDEN to show the
same woman twice... If a subject is not visible, that is a CRITICAL FAILURE.
```

#### 正确做法
```
[APPEARANCE RULES] Shots 1-7: each shows ONLY its assigned <Subject N>.
Shot 8: ALL 7 appear — EXACTLY SEVEN, NO duplicates.
```

#### 强调词使用指南

| 场景 | 推荐做法 |
|------|----------|
| 首次声明关键规则 | 用 1-2 个强调词（如 `ONLY`、`EXACTLY`） |
| 同一规则在后续出现 | 不加强调词，直接引用 |
| 禁止性规则 | 用 `NO` / `NEVER` 一次，不堆叠 `ABSOLUTELY FORBIDDEN` |
| 全局性约束 | 放入 `[SHARED GUARDRAILS]`，一处声明 |

**原则**：强调词是稀缺资源。每出现一次 `CRITICAL` / `ABSOLUTE`，都会稀释之前所有强调词的权重。全文保留 3-5 个最关键的强调点即可。

---

### 原则 6：信息分层架构（Information Layering）
> 中文别名：**分层组织，各管各的**

**按"全局规则 → 个体定义 → 执行指令 → 概览摘要"四层组织，每层只写本层独有信息。**

#### 四层架构

```
Layer 1 — 全局规则层 [SHARED GUARDRAILS]
  ├─ 光照 / 调色 / 静默 / 音乐 / 镜头距离 / 速度结构
  └─ 出现 1 次，全文不再重复

Layer 2 — 个体定义层 subject_definitions
  ├─ 每个角色的完整属性（外貌 / 配色 / 粒子 / 动作 / 镜头分配）
  └─ 每人出现 1 次，其他位置只引用

Layer 3 — 执行指令层 detailed_description
  ├─ 每镜头的运镜 / 音乐层 / 时间戳 / 独有转场
  └─ 用短标签引用 Layer 2，只写本镜头独有信息

Layer 4 — 概览摘要层 summary + retention_analysis
  ├─ summary: 结构/数量/风格概述，不逐项复述
  └─ retention: "fully_preserved per <Picture N>"，不重复特征
```

#### 分层信息分配表

| 信息类型 | Layer 1 | Layer 2 | Layer 3 | Layer 4 |
|----------|---------|---------|---------|---------|
| 光照规则 | ✅ 定义 | ❌ | ❌ | ❌ |
| 角色外貌 | ❌ | ✅ 定义 | 🔖 引用 | 🔖 "per Picture" |
| 配色/粒子/动作 | ❌ | ✅ 定义 | 🔖 引用 | ❌ |
| 运镜/转场 | ❌ | ❌ | ✅ 定义 | ❌ |
| 音乐层级 | ❌ | ❌ | ✅ 定义 | 🔖 概述 |
| 布局/汇聚 | ❌ | ❌ | ✅ 定义（末幕） | 🔖 概述 |
| 防错规则 | ✅ 定义 | ❌ | ❌ | ❌ |

✅ = 定义 ｜ 🔖 = 引用 ｜ ❌ = 不出现

---

## 三、字段级精简指南

### 3.1 `subject_definitions` — 唯一权威定义源

**目标**：每个角色的所有属性在这里完整定义一次，结构化、可扫描。

#### 每个角色的必含字段（按顺序）

```
<Subject N> from <Picture N>.
  [外貌] 刘海类型 + 眼睛类型 + 脸型 + 嘴唇 + 肤色 + 气质
  [服装] 服装款式 + 露肩情况
  [场景] 游戏设备 / UI 类型
  [配色] Color: XXXX
  [粒子] Particle: XXXX
  [动作] Action: XXXX
  [分配] Shot N (solo) + Shot 8 (POSITION)
```

#### 关键技巧

- **差异化锚点**：每个角色的刘海/服装描述中嵌入与其他角色的区分词
  - 例：`SHORT OBLIQUE LEFT-PARTED BANGS (shorter than S7's)`
  - 不需要单独写一大段对比，用括号短注即可
- **共享特征归组**：所有角色共有的特征（黑长直发、白肤、深色毛衣）放入 `[SHARED TRAITS]`，不在每人定义中重复
- **SHOT ASSIGNMENT 简化**：`Shot 3 (solo) + Shot 8 (TOP-CENTER-RIGHT)` 即可，不需要 `NEVER skip her, NEVER replace her`

---

### 3.2 `summary` — 结构概览，不是信息复述

**目标**：给模型一个全局画面感，不逐项复述已在 `subject_definitions` 中定义的细节。

#### summary 只写

- 视频类型 + 时长 + 风格
- 角色数量 + 引用方式
- 核心机制（speed-ramp / rim-light / music-driven）
- 全局约束（silent / cold grading）
- 末幕概述（ALL 7 together + 布局 + 文字）

#### summary 不写

- 逐人的配色/粒子/动作清单
- 逐人的外貌特征
- 逐镜头的运镜细节
- 任何已在 `subject_definitions` 或 `[SHARED GUARDRAILS]` 中定义的信息

---

### 3.3 `retention_analysis` — 保留声明，不是特征复述

**目标**：声明每个角色出现在哪些镜头 + 保留级别，不重复外貌特征。

#### 正确做法
```
<Subject 1> (Shot 1, Shot 8 TOP-FAR-LEFT): fully_preserved per <Picture 1>.
```
10 词，信息完整（谁 + 出现在哪 + 保留级别 + 参考图）。

---

### 3.4 `detailed_description` — 执行指令，引用为主

**目标**：每个镜头只写本镜头独有的新信息（运镜、转场、音乐层、时间戳），角色特征用短标签引用。

#### 每镜头只写的独有信息

| 信息 | 是否独有 | 写法 |
|------|----------|------|
| 运镜方式 | ✅ 独有 | 完整描述 |
| 转场方式 | ✅ 独有 | 完整描述 |
| 时间戳 | ✅ 独有 | `At MM:SS.mmm` |
| 音乐层 | ✅ 独有 | 新增乐器 |
| 角色特征 | ❌ 已定义 | 短标签引用 |
| 配色/粒子 | ❌ 已定义 | 短标签引用 |
| 动作 | ❌ 已定义 | 短标签引用 |
| 光照规则 | ❌ 共享规则 | 不写 |
| 速度结构 | ❌ 共享规则 | 不写 |

---

### 3.5 末幕/群像镜头 — 布局指令，引用为主

**目标**：末幕只写布局/汇聚/文字等独有信息，每个角色用短标签引用。

#### 末幕只写的独有信息

| 信息 | 写法 |
|------|------|
| 布局结构 | `TOP ROW 4 women (S1 far-left, S2 center-left, ...) BOTTOM ROW 3 women (...)` |
| 汇聚效果 | `seven-color particles CONVERGE to center, COLLIDE in CYBERPUNK ENERGY BURST` |
| 文字内容 | `title "高能集结" in NEON GLITCH FONT` |
| 人数约束 | `EXACTLY 7, NO duplicates` |
| 角色引用 | `<S1> icy-cyan frost-shards headphone-sweep`（短标签） |

---

## 四、压缩前后对比量化

以 7 人角色视频提示词为例：

| 维度 | 冗余版 | 精简版 | 压缩率 |
|------|--------|--------|--------|
| 英文代码块总词数 | ~5380 | ~2235 | **-58%** |
| 每人外貌特征出现次数 | 3-4 次 | 1 次（定义） + 短标签引用 | **-70%** |
| 每人配色/粒子/动作出现次数 | 4 次 | 1 次（定义） + 1 次（引用） | **-50%** |
| 共享规则重复次数 | 8-11 次 | 1 次 | **-90%** |
| 反漏人指令出现次数 | 20+ 次 | 2 次 | **-90%** |
| 强调词（CRITICAL/ABSOLUTE）出现次数 | 15+ 次 | 3-5 次 | **-70%** |
| 信息完整性 | 100% | 100% | **0% 损失** |

---

## 五、精简自检清单

撰写完成后逐项检查：

### 信息源检查
- [ ] 每条信息只在 **一个权威位置** 完整定义
- [ ] `summary` 没有逐人复述配色/粒子/动作清单
- [ ] `retention_analysis` 没有重复外貌特征描述
- [ ] `detailed_description` 中角色特征用短标签引用而非完整复述
- [ ] 末幕没有逐人重新描述完整外貌

### 共享规则检查
- [ ] 光照/调色/静默/音乐/镜头距离规则只在 `[SHARED GUARDRAILS]` 出现一次
- [ ] 速度 ramps 结构只定义一次
- [ ] 没有在任何角色定义或镜头描述中重复共享规则

### 防错指令检查
- [ ] 反漏人/防重复/防换人指令集中在一个 `[APPEARANCE RULES]` 块中
- [ ] 没有在每个角色定义末尾重复 "NEVER skip / NEVER replace"
- [ ] 末幕人数约束只写一次

### 强调词检查
- [ ] `CRITICAL` / `ABSOLUTE` / `FORBIDDEN` 全文不超过 5 次
- [ ] 同一规则没有用不同强调词重复声明
- [ ] `MUST` / `NEVER` 保留给最关键的 3-5 个约束

### 引用标签检查
- [ ] 每个角色有 2-3 个唯一性锚标签（如 `BLUNT BANGS, CREW-NECK`）
- [ ] 引用标签在全文一致（同一角色用同一套标签）
- [ ] 标签足够独特，能无歧义回溯到定义源

---

## 六、通用精简工作流

```
Step 1 — 定义层：在 subject_definitions 中完整定义每个角色（唯一权威源）
Step 2 — 共享层：将所有角色/镜头共享的规则提取到 [SHARED GUARDRAILS]
Step 3 — 防错层：将所有防漏人/防重复/防换人规则提取到 [APPEARANCE RULES]
Step 4 — 执行层：在 detailed_description 中用短标签引用角色，只写独有信息
Step 5 — 概览层：summary 写结构概述，retention 写保留声明，不复述细节
Step 6 — 末幕层：末幕用短标签引用角色，只写布局/汇聚/文字等独有信息
Step 7 — 自检层：用精简自检清单逐项检查，消除残留重复
```

---

## 七、一句话总结

> **精简 = 每条信息只说一次，其余位置只说"谁"和"在哪"，不说"是什么"。**
>
> 模型的注意力是稀缺资源。你每多写一句重复的话，就在稀释你最想让它执行的那句指令。
