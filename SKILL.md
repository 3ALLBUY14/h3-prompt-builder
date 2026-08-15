---
name: h3-prompt-builder
description: "MiniMax H3视频提示词生成全流程指南。整合5种模式选择、8种风格模板、运镜/对白/音频规则、数字人对口型防火星语（短音频音色参考优先）、冗余压缩优化、常见坑与质量自检。当用户需要写H3视频提示词、制作H3视频、或提到T2VA/I2VA/FL2VA/L2VA/Ref2VA时使用。"
version: 2.2.0
---

# MiniMax H3 视频提示词生成指南

为任意 AI agent 提供 MiniMax H3 视频提示词的完整生成流程。从需求分析到最终输出，覆盖五种生成模式、八种风格模板、运镜/对白/音频规则、冗余压缩优化、常见坑对策与质量自检清单。

## H3 基础规格

| 参数 | 值 |
|------|-----|
| 时长 | 4-15 秒 |
| 分辨率 | 768p / 1440p（推荐 1440p） |
| 帧率 | 24fps |
| 音频 | 原生双声道 |
| TTS 语言 | 11 种（中/英/日/韩/法/德/西等） |

## 工作流

```
1. 需求分析 → 2. 模式选择 → 3. 风格匹配 → 4. 提示词撰写 → 5. 冗余压缩 → 6. 质量自检 → 7. 交付
```

### 步骤 1：需求分析

确认以下要素（缺项主动询问用户）：

- **主体**：谁/什么出现在画面中
- **场景**：在哪里发生
- **事件**：发生什么动作
- **风格**：视觉调性（写实/动画/3D/纸艺等）
- **时长**：目标秒数（4-15秒）
- **素材**：用户已有什么素材（图片/视频/音频）
- **对白**：是否需要台词/旁白
- **音乐**：是否需要背景音乐
- **循环**：是否需要无缝循环

### 步骤 2：模式选择

根据用户素材情况选择生成模式：

```
用户有素材吗？
├─ 无素材，纯文字描述 → T2VA（文生视频）
├─ 有 1 张首帧图 → I2VA（图生视频）
├─ 有首帧+尾帧 2 张图 → FL2VA（首尾帧）
├─ 有 1 张尾帧图 → L2VA（尾帧）
└─ 有多图/视频/音频（≤12个） → Ref2VA（全能参考）
```

| 模式 | 技术名 | 检查点 | 输出结构 | 适用场景 |
|------|--------|--------|----------|----------|
| T2VA | Text to Video Audio | H3-Base-FL2VA | 三字段 | 纯创意构思、无素材 |
| I2VA | Image to Video Audio | H3-Base-FL2VA | 指令行+三字段 | 让静态图动起来 |
| FL2VA | First-Last to Video Audio | H3-Base-FL2VA | 指令行+三字段 | 描述起止状态间的过渡 |
| L2VA | Last to Video Audio | H3-Base-FL2VA | 指令行+三字段 | 已知结尾反推开头 |
| Ref2VA | Full Reference | H3-Base-Ref2VA | 六段结构 | 多素材参考、角色/音色/动作引用 |

**Ref2VA 素材上限**：≤9 图 + ≤3 视频 + ≤3 音频，总计 ≤12 个，每段 2-15 秒，音频不可单独使用。

### 步骤 3：风格匹配

根据内容类型匹配最佳风格模板（详见 `references/style-guide.md`）：

| 风格 | 最佳场景 | 核心特征 |
|------|----------|----------|
| 极简产品广告 | 电商产品、新品发布 | Apple风、三张锚定照片、精确节拍 |
| 3D动画短片 | 剧情动画、品牌故事 | 皮克斯风、六列镜头表、六项自检 |
| 纸艺定格科普 | 科学教育、知识讲解 | 分层纸片、逐帧手工感、轻量旁路 |
| 品牌宣传短片 | 新品发布、社媒推广 | 叙事脊柱、品牌安全、精确节拍 |
| 音乐美学MV | 歌词贴字、情绪短片 | 三卡分离、五大衔接锁、硬切剪辑 |
| 双人游戏开场 | 游戏概念、角色菜单 | 14维度首图框架、风格确认前置 |
| 纸拼贴讲解 | 知识表达、社交B-roll | 高级半调纸拼贴、停格组装、色彩语义 |
| 手绘实拍融合 | 创意短片、手绘实验 | 11段固定结构、连续变形、慢半拍追拍 |

如果用户需求不匹配上述任何风格，按通用提示词规则撰写即可。

### 步骤 4：提示词撰写

按所选模式的格式规范撰写。完整格式规范见 `references/mode-formats.md`。

#### Base 系格式（T2VA / I2VA / FL2VA / L2VA）

```
[指令行]（I2VA/FL2VA/L2VA 特有，T2VA 无；必须是第一行，后空一行）

integrated_multimodal_description:
[Shot 1] [风格句] [景别] [画面内容+动作+运镜+对白+音效]
[Shot 2] At MM:SS.mmm, the camera cuts to...

overall_soundscape:
[1-4 句环境音总结]

non_diegetic_music:
N/A（或 1-3 句配乐描述）
```

#### Ref2VA 六段格式

```
subject_definitions:
<Subject 1> is the [角色] in <Picture 1>, with [外观细节].
<Audio 1> is the voice-timbre reference for <Subject 1> (S1).

summary:
[reference generation + audio reference] The target video shows [一句话概述]. <Picture 1> provides [用途], and <Audio 1> guides the voice timbre.

retention_analysis:
<Subject 1> (appears in [Shot 1], [Shot 2]): fully_preserved - [保留项].
<Audio 1>: reference - [参考方式].

detailed_description:
[风格句]. [Shot 1] [景别] [动作+音效]. <Subject 1> (S1) says: <d>[Chinese] 台词</d>

overall_soundscape:
[1-4 句声音总结]

non_diegetic_music:
N/A（或配乐描述）
```

#### Ref2VA 多主体高级模板（3+ 主体适用）

当有 3 个以上主体时，使用以下结构确保信息不冗余：

```
subject_definitions:
[SHARED GUARDRAILS] [全局规则：光照/调色/静默/音乐/镜头距离/速度结构，出现1次]
[APPEARANCE RULES] [防漏人/防重复/防换人规则，集中1次]
<Subject 1> from <Picture 1>. [差异化锚点 + 唯一性特征]. Color: XXX. Particle: XXX. Action: XXX. Shot N (solo) + Shot M (POSITION).
<Subject 2> from <Picture 2>. ...
[SHARED TRAITS] All share: [共有特征]. Distinguished by: [区分维度].
<Prop 1> from <Picture N>. [道具定义]（如有）
<Picture M> — scene reference: [场景定义]（如有）

summary:
[结构概述：时长 + 风格 + 主体数量 + 核心机制 + 全局约束 + 末幕概述]
（不逐人复述细节）

retention_analysis:
<Subject 1> (Shot N, Shot M POSITION): fully_preserved per <Picture 1>.
（不重复外貌特征）

detailed_description:
[风格句].
[Shot 1] [运镜 + 独有信息] <Subject 1> (短标签): [动作 + 音效]
[Shot 8] [布局指令] <S1> 短标签, <S2> 短标签, ...

overall_soundscape:
[1-4 句声音总结]

non_diegetic_music:
N/A（或配乐描述）
```

**关键规则**：
- `[SHARED GUARDRAILS]` 和 `[APPEARANCE RULES]` 各只出现一次，全文不重复
- 每个角色的完整定义只在 `subject_definitions` 中出现一次
- `detailed_description` 和末幕用 2-4 个大写关键词短标签引用（如 `<S1> icy-cyan frost-shards headphone-sweep`）
- 详见 `references/optimization-guide.md`

#### 关键帧对齐指令行（必须精确逐字）

- **I2VA**: `For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.`
- **FL2VA**: `How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot N) aligns with the S.SS-second mark of the target video.`
- **L2VA**: `How the reference pictures align with the target video — <Picture 1> (from [Shot N]) aligns with the S.SS-second mark of the target video.`

`N` 为最后一个 shot 的序号，`S.SS` 为有效视频时长（两位小数）。

### 步骤 5：冗余压缩

撰写完成后，对提示词进行冗余压缩。完整精简优化标准见 `references/optimization-guide.md`。

#### 核心原则

1. **单一信息源（SSOT）**：每条信息只在一个权威位置完整定义，其他位置用短标签引用
2. **共享规则合并**：所有角色/镜头共享的规则提取到 `[SHARED GUARDRAILS]`，全文只写一次
3. **防错指令集中**：防漏人/防重复/防换人指令提取到 `[APPEARANCE RULES]`，不逐人重复
4. **强调词降级**：`CRITICAL`/`ABSOLUTE`/`FORBIDDEN` 全文不超过 5 次
5. **信息分层**：全局规则 → 个体定义 → 执行指令 → 概览摘要，每层只写本层独有信息

#### 何时需要压缩

- 主体数 ≥ 3 时，**必须**执行冗余压缩
- 主体数 1-2 但提示词超过 3000 词时，建议执行
- 简单 T2VA/I2VA（< 1500 词）可跳过

#### 多主体字符管理

| 主体数 | 典型词数 | 策略 |
|--------|---------|------|
| 1-2 | 2000-4000 | 正常撰写 |
| 3-5 | 4000-6000 | 精简每个 Subject 外观，合并共享特征 |
| 7-8+ | 6000-7000 | 极度精简：仅保留唯一性锚点，用短标签引用，共享规则全部提取 |

### 步骤 6：核心写作规则

#### 镜头与切换

- `[Shot 1]` 开头先写整体风格与初始构图，**不加时间戳**
- 后续镜头：`[Shot 2] At 00:03.500, the camera cuts to...`，时间严格递增且在总时长内
- 普通切换：the camera cuts to / the shot cuts to / the shot transitions to
- 跨场/特效切换（用户明确要求才用）：cross-dissolve / fade / wipe
- 只变距离或轻微角度时优先用运镜而非切镜

#### 运镜三维度

运镜 = **运动类型 + 幅度 + 速度**，写成镜头内自然英语动作，不在句尾堆标签。

| 维度 | 可选值 |
|------|--------|
| 运动类型 | Zoom In/Out, Push In/Pull Out, Pan Left/Right, Truck Left/Right, Tilt Up/Down, Pedestal Up/Down, Arc Shot, Tracking Shot, Static Shot, Shake Slightly/Strongly, POV, Roll Clockwise/Counterclockwise |
| 幅度 | with small amplitude / with large amplitude（中等省略） |
| 速度 | at slow speed / at fast speed（正常省略） |

示例：`The camera pushes in with small amplitude at slow speed toward the folded letter in her hands.`

环绕运镜写成 `truck left + pan right`，不要直接说"环绕运镜"。

#### 对白与台词

- 说话人稳定 ID：`(S1)` `(S2)`，跨镜头不变；不发声的角色不给 ID
- 多人齐说：`(S1,S2)`
- 对白格式：`<d>[语言] 原文内容</d>`，逐字保留，不翻译
- 首次出现给出身份信息（类型、年龄、性别、音高、音色、语速、口音）
- 画外音：`says in an off-screen voiceover: <d>...</d> while his/her lips remain completely closed.`
- 跨镜头对白：连接点用 `<scenetrans>`，注明 `continues seamlessly across the cut` 等
- 结尾截断台词：`<cutoff>`
- 画面文字：英文双引号包裹，逐字保留，如 `A sign reading "营业中"`

#### 数字人对口型专项规则（防火星语）

台词归属必须三选一，禁止归属缺省（详见 `references/lipsync-guide.md`）：

| 归属方式 | 画面要求 | 要点 |
|----------|----------|------|
| 口型同步 | 角色露脸，嘴部清晰可见 | `speaks to the camera` + `<d>` 台词；手持产品时用胸前持物中近景，不用纯手部特写 |
| 明确画外音 | 露脸不说话或完全出镜均可 | `off-screen voiceover` + `lips remain completely closed` 双保险 |
| 无台词 | 任意画面（纯产品 B-roll） | 不放 `<d>` 标签 |

配套规则：

- reference 模式（音色参考）下 `<d>` 台词按时间分段覆盖全程，空档会被火星语填充
- **音色参考素材首选短音频（≤8s）**：参考音频时长 ≪ 视频时长时模型只提取音色，`<d>` 时间线唯一主导，零冲突零杂交；内容任意（无需念台词）；长参考（≈视频时长）会映射时间结构引发双重起音/音节杂交/吞字，需 VAD 对齐兜底（详见 lipsync-guide §7）
- **切点 VAD 对齐（长参考专属）**：长参考下模型同时参考 `<d>` 分段时间线与音频节奏线，切点与音频实际句子边界错位 ≥0.3s 会在该边界触发双重起音（火星语一遍+正常重念一遍），≤0.25s 可扛住；换音频后必须 VAD 重新实测对齐（语速不均匀，按字符数估算不可靠）。短参考天然免疫
- **字数-节奏匹配（长参考专属）**：`<d>` 各句字数 ≤ 参考音频对应句实际字数（多 1 字句末超载随机吞字）；句中后段避免轻读虚词（都/也/就/会/了），需要语气词用双字实义词（照样/真的/完全）。短参考无此约束
- 音频时长 ≤ 15s，超限会被强制压缩导致中段口胡，只能重录；新 TTS 音频必须实测时长（可能超限），首尾静音可剪（15s 上限约束视频与 fully_copy 音轨，不约束参考音频）
- 嘴部约束只用正面描述：`lips barely parting for each word` / `lips rest softly together`；负面句式（"never showing teeth"）会被反向聚焦导致呲牙
- CTA 避免四字连读（"限时抢购"易口胡），改用"抢购吧"等短促词

特效字规范：

- 字体：`medium-weight elegant sans-serif`（不用 bold），描边 `subtle semi-transparent dark stroke`，背板 `semi-transparent dark blur backdrop`
- 低饱和色板：柔金 #BFAA82（品牌）/ 暖白 #E8E2D8（内容）/ 灰绿 #9AAA9C（卖点）/ 柔砖红 #BC7A68（CTA）；避免 #D4AF37、#D4735A 等高饱和色
- 长英文串拆分弹出（"AOIAOI礼盒"→ 先弹"AOI"再弹中文）；叠加文字与 `<d>` 台词同内容时改格式（"、"→"+"），防止重复渲染两行
- 颈部描述极简：只写 choker 等饰品，不写肤色/光线/边界

#### 音频规则

- **overall_soundscape**：1-4 句，总结环境音、物理动作声、非语言人声。台词/唱歌/剧情内音乐不写这里。全程静音才写 `N/A`
- **non_diegetic_music**：1-3 句，描述配器、速度、节奏、动态变化。不用抽象情绪词。无配乐写 `N/A`
- 角色可闻的唱歌/乐器/收音机音乐是剧情内事件，写进 multimodal description
- Ref2VA 中角色首次台词须标注音色来源：`, using the voice timbre referenced from <Audio N>, says:`，后续可省略

#### 台词字数检查

中文语速约 4.5 字/秒：**台词字数 ÷ 4.5 ≤ 镜头时长**

| 镜头时长 | 最大台词字数 |
|----------|-------------|
| 3 秒 | 13 字 |
| 4 秒 | 18 字 |
| 5 秒 | 22 字 |
| 6 秒 | 27 字 |
| 8 秒 | 36 字 |
| 10 秒 | 45 字 |

### 步骤 7：常见坑与对策

| 常见问题 | 对策 |
|----------|------|
| 只写一段话没分段 | 按三段公式拆开：参考素材说明 + 核心创意 + 画面过程说明 |
| 上传素材但没说用途 | 补充素材标签和用途说明（`<Picture 1>` 是 XX 参考） |
| 既用音乐又写"不要BGM" | 删掉一个，或按场景分别说明 |
| 想一镜到底却写了很多分镜 | 保持一段连贯情节，删掉分镜结构 |
| 想保持主角脸部一致但没传图 | 必须上传人物参考图并标注 |
| 纯文生时提示词太短 | 写齐：主体外观 + 场景细节 + 动作 + 风格 |
| 台词超长导致口型问题 | 台词字数 ÷ 4.5 ≤ 镜头时长，超了删字 |
| 无BGM写中文"无背景音乐" | 写 `non_diegetic_music: N/A` |
| 用了非官方语法 `@图片1` | 改用官方标签 `<Picture 1>`/`<Subject 1>`/`<Audio 1>` |
| 循环视频没有收尾句 | 结尾加 `The final action returns to the starting pose, allowing seamless looping.` |
| Ref2VA 漏标音色来源 | 首次台词必须标注 `using the voice timbre referenced from <Audio N>` |
| Base 指令行不在第一行 | 指令行必须是提示词第一行，后空一行再写三字段 |
| 数字人念火星语 | 台词归属三选一：口型同步（露脸嘴清晰）/ 明确画外音（+嘴唇闭合）/ 无台词；禁止归属缺省 |
| reference 模式台词空档出火星语 | `<d>` 台词按时间分段覆盖全程，不留空档 |
| 音节杂交（"用满28天"→"坚满28天"）：长参考内容与台词相似但不一致，模型混读 | 首选改用短音频参考（≤8s）根治；长参考则重录使内容与 `<d>` 完全一致 |
| reference 句边界双重起音（火星语一遍+正常一遍）：长参考下切点与音频实际句界错位 | 首选短音频参考根治；长参考则 VAD 实测句界后重新对齐切点 |
| 换配音音频后口胡复发：沿用旧音频切点 | 短音频参考天然免疫；长参考必须重新 VAD 对齐（语速不均匀，按字符数估算不可靠） |
| 句末随机吞字（每次吞哪个字不同）：长参考下 `<d>` 字数 > 音频对应句节奏容量 | 首选短音频参考根治；长参考则缩字至少 1 字留余量 |
| 固定吞弱读虚词（都/也/就/会/了）：轻读字句末被淡化 | 换双字实义词（照样/真的/完全），重配音以 `<d>` 为准改音频无用 |
| "不露齿"约束反而呲牙 | 删负面词，只写正面状态："lips rest softly together" |
| 音频超 15s 中段加速口胡 | 音频控制在 14-15s，重录而非靠提示词补救 |
| 产品特写 B-roll 念火星语 | 纯产品镜头不放 `<d>`，台词移到人物镜头或明确画外音 |
| 手持产品特写火星语 | 改胸前持物中近景（面部+嘴部清晰入镜），不用纯手部特写 |
| CTA 四字连读口胡 | 改短促词："抢购吧" |
| 特效字长英文被误写（AOIAOI→AOI） | 拆分显示：先弹英文短词，再弹中文 |
| 叠加文字与台词重复渲染两行 | 格式区分：台词"、"，叠加"+" |
| 特效字配色俗气 | 低饱和莫兰迪色（#BFAA82/#E8E2D8/#9AAA9C/#BC7A68）+ medium-weight 字体 |
| 颈部区域光线/肤色异常 | 颈部极简：只写 choker 等饰品，不写肤色/光线/边界 |

### 步骤 8：质量自检清单

撰写完成后逐项检查：

- [ ] 模式正确：素材情况与所选模式匹配
- [ ] 格式完整：Base 系三字段齐全 / Ref2VA 六段齐全
- [ ] 指令行精确：I2VA/FL2VA/L2VA 指令行逐字正确，位于第一行
- [ ] 镜头时间戳：`[Shot 1]` 无时间戳，后续递增且在总时长内
- [ ] 运镜规范：只用官方词表，三维度自然融入句子
- [ ] 对白格式：`<d>[语言] 原文</d>`，说话人 ID 稳定
- [ ] 画外音：含嘴唇闭合说明
- [ ] 音色来源：Ref2VA 首次台词已标注
- [ ] 台词字数：每句 ÷ 4.5 ≤ 镜头时长
- [ ] 环境音：overall_soundscape 有内容或 N/A
- [ ] 背景音乐：non_diegetic_music 有描述或 N/A
- [ ] 画面文字：英文双引号包裹
- [ ] 循环收尾：循环视频末尾有收尾句
- [ ] 官方标签：用 `<Picture N>`/`<Subject N>`/`<Audio N>` 而非 `@图片N`
- [ ] 台词归属（数字人）：口型同步 / 明确画外音 / 无台词，三选一写清楚，无归属缺省
- [ ] 台词覆盖：reference 模式下 `<d>` 台词覆盖全程无空档
- [ ] 音色参考素材：首选短音频（≤8s，内容任意、音色干净）；长参考（≈视频时长）须 VAD 对齐切点
- [ ] 音频时长：≤ 15s
- [ ] 嘴部描述：全部正面句式，无 "never/no teeth" 等负面词
- [ ] 特效字：与台词格式区分、长文字拆分、低饱和色板 + medium-weight 字体

### 精简检查（多主体场景必查）
- [ ] 每条信息只在一个权威位置完整定义，其他位置只引用不复述
- [ ] `summary` 没有逐人复述配色/粒子/动作清单
- [ ] `retention_analysis` 没有重复外貌特征描述
- [ ] `detailed_description` 中角色特征用短标签引用而非完整复述
- [ ] 共享规则只在 `[SHARED GUARDRAILS]` 出现一次
- [ ] 防错指令集中在一个 `[APPEARANCE RULES]` 块中
- [ ] `CRITICAL`/`ABSOLUTE`/`FORBIDDEN` 全文不超过 5 次
- [ ] 每个角色有 2-3 个唯一性锚标签

### 步骤 9：交付

- 将完整提示词放入代码块中交付
- 如有多个镜头，附分镜表概览
- 提醒用户台词字数需人工复核

## 参考文件

- `references/mode-formats.md` — 五种模式官方格式完整速查（含指令行精确措辞、运镜词表、共享写作规则、官方完整示例）
- `references/style-guide.md` — 十种风格专项详细指南（含核心流程、关键设计、质量自检要点）
- `references/prompt-examples.md` — 实际案例参考库（模特展示、循环讲解、直播带货、电竞天团、多幕叙事等场景）
- `references/optimization-guide.md` — 提示词精简优化标准（六大压缩原则、字段级精简指南、自检清单、工作流）
- `references/lipsync-guide.md` — 数字人对口型实战指南（防火星语：短音频音色参考机制、台词归属三选一、五大原则、坑位清单、嘴部正面描述词库、特效字配色规范、reference 模式时间线工程）

## 跨风格通用方法论

无论使用哪种风格模板，都遵循以下共通原则：

| 原则 | 说明 |
|------|------|
| 英文为主体 | 描述用英文，对白/歌词/可见文字保留原文 |
| 镜头编号制 | `[Shot 1]` 开头，后续加时间戳 |
| 运镜三维度 | 运动类型 + 幅度 + 速度 |
| 说话人 ID | `(S1)` `(S2)` 跨镜一致 |
| 对白标签 | `<d>[语言] 内容</d>` |
| 音画分离 | 画面描述 + 环境音 + 背景音乐分字段 |
| 风格锁 | 一旦确认风格，贯穿所有后续产物 |
| 叙事脊柱 | 先选叙事结构，再写分镜 |
| 精确节拍 | 按秒/帧规划时间线，不写模糊描述 |
| 动效语言 | 先定义运动规则，再生成视频 |
