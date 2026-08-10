# H3 实际案例参考库

从实际项目中提取的代表性案例片段，供撰写提示词时参考结构和措辞。

---

## 案例 1：Ref2VA 多模特展示（8模特高级商业广告片）

**场景**：8位旗袍模特，每人独立镜头，15秒高级商业广告片，H3原生音频

**设计要点**：
- 每位模特用独立 `<Subject N>` 定义，包含发型、五官、旗袍花纹、配饰、手包等完整外观
- 每镜仅一个流畅运镜动作，禁止旋转/甩镜/快切叠加
- 每镜聚焦一个服饰细节（刺绣/盘扣/胸针/耳饰/面料/发型）
- 硬切但保持运镜方向一致，视觉连贯不跳跃
- 旁白短促精炼（4字句），分散在不同镜头中

**subject_definitions 片段**：
```
<Subject 1> is the young East Asian woman in <Picture 1>, with fair porcelain skin, an oval-heart face with high cheekbones, bold crimson-red matte lipstick, defined arched eyebrows, and dark black hair styled in a low chignon bun with loose tendrils framing her face. She wears a sleeveless form-fitting high-neck qipao in deep teal-emerald green silk with gold landscape patterns depicting mountains and pavilions, a black-piped mandarin collar with an ornate gold flower-shaped metal clasp featuring a red center, and a side slit. She wears white pearl stud earrings and holds a small structured cognac-brown leather top-handle handbag.
```

**detailed_description 片段**：
```
[Shot 1] A slow push-in glides from a medium-full shot toward <Subject 1> standing in three-quarter profile against the beige wall. The camera moves forward at a steady, luxurious pace, narrowing from full-body to medium framing. The teal-green silk with gold landscape patterns gradually fills the frame, the fabric's subtle sheen shifting as the camera closes in. The gold mountain and pavilion motifs on the bodice catch the warm window light. The gold flower-shaped clasp with its red center glints at her collar. She holds still, composed, her gaze soft and confident. A gentle silk-rustle sound is heard. <d>[Chinese] 翠色入梦。</d>
```

**关键经验**：
- 多模特场景每个 Subject 必须有唯一的"最强识别点"（如唯一披肩波浪长发、左脸泪痣等）
- 旁白台词字数严格控制：4字句约0.9秒，9字句约2秒
- overall_soundscape 要涵盖所有镜头的声音元素

---

## 案例 2：Ref2VA 循环讲解视频（IP角色+音色克隆）

**场景**：猫耳女仆少女IP角色，循环讲解视频，使用音频素材做音色克隆

**设计要点**：
- 角色外观定义详尽（猫耳、发饰、服装、比例）
- 音色来源在首次台词时标注
- 循环视频末尾必须有收尾句
- 3-4个动作节拍，总时长10-15秒

**完整示例**：
```
subject_definitions:
<Subject 1> is the young woman in <Picture 1>, with short black hair and blunt bangs, black cat ears with white inner fur, a white ruffled headband with red tassels on both sides, a red and white Chinese-style maid outfit with gold embroidery and frog buttons, large brown eyes with rosy cheeks, and chibi proportions.
<Audio 1> is the voice-timbre reference for <Subject 1> (S1), containing a spoken Chinese vocal layer.

summary:
[reference generation + audio reference] The target video shows <Subject 1> standing behind a tech-style desk, introducing three video generation modes by summoning two glowing panels. <Picture 1> provides the character's appearance, and <Audio 1> guides the voice timbre.

retention_analysis:
<Subject 1> (appears in [Shot 1]): fully_preserved - identity, hairstyle, cat ears, maid outfit, and chibi proportions retained throughout.
<Audio 1>: reference - vocal timbre guides dialogue without copying the signal.

detailed_description:
The target video uses a 2D-animated style with bright, clean lighting.
[Shot 1] A medium shot establishes <Subject 1> (S1) standing behind a tech-style desk. She raises her right hand in a "three" gesture. A semi-transparent panel materializes on the left, displaying image icons and text lines. A crisp panel-pop sound is heard. <Subject 1> (S1), using the voice timbre referenced from <Audio 1>, says: <d>[Chinese] H3 有三种生成模式！</d> She points her left index finger at the left panel, then turns toward the right side. A second panel lights up on the right, showing a play button and music-note icons. A soft panel-switch sound is heard. <Subject 1> (S1) says: <d>[Chinese] 文生视频、图生视频、还有全能参考。</d> She spreads both hands in a merging gesture. Both panels emit a soft glow simultaneously. A gentle panel-fusion glow sound is heard. <Subject 1> (S1) says: <d>[Chinese] 图片锁脸、视频锁动作、音频定情绪！</d> The final action returns to the starting pose, allowing seamless looping.

overall_soundscape:
Quiet indoor desk environment with ambient room tone.

non_diegetic_music:
N/A
```

**关键经验**：
- 首次台词必须标注 `using the voice timbre referenced from <Audio 1>`
- 循环视频末尾必须有 `The final action returns to the starting pose, allowing seamless looping.`
- 不需要BGM时明确写 `N/A`
- 3句台词分别约13字、15字、14字，均在合理时长内

---

## 案例 3：T2VA 纯文生视频

**场景**：面包师清晨开店，纯文字描述无素材

**完整示例**：
```
integrated_multimodal_description: [Shot 1] Live-action, cinematic, a medium-wide shot frames a baker opening the shutters of a small street bakery before sunrise. The camera pushes in with small amplitude at slow speed as the middle-aged baker with a calm, slightly raspy voice (S1) places a fresh loaf on the wooden counter and says: <d>[English] First batch of the morning.</d> [Shot 2] At 00:05.000, the camera cuts to a close-up of steam rising from the sliced bread while the baker's final words carry over from the previous shot.

overall_soundscape: Wooden shutters scrape open over a quiet street as trays clink softly inside the bakery. The doorbell rings once, followed by light footsteps and the crisp sound of bread being sliced.

non_diegetic_music: A soft acoustic-guitar pattern at a moderate tempo, joined by sparse upright-bass notes and a gentle fade at the end.
```

**关键经验**：
- T2VA 无指令行，直接写三字段
- `[Shot 1]` 开头先写风格（Live-action, cinematic）
- 运镜自然融入句子（pushes in with small amplitude at slow speed）
- 跨镜头对白用 `carry over from the previous shot` 衔接
- 环境音与配乐分开写在不同字段

---

## 案例 4：I2VA 图生视频

**场景**：火车上雨中窗边女子，1张首帧图

**完整示例**：
```
For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, the young woman shown in <Picture 1> remains beside the rain-covered train window, preserving her appearance, clothing, seat position, and the carriage layout. The camera trucks right with small amplitude at slow speed as she lifts her gaze from the folded letter toward the passing city lights. Her reflection moves across the glass while the quiet, breathy young woman (S1) says: <d>[English] I get off at the next station.</d> She folds the letter along its existing crease.

overall_soundscape: The train wheels produce a steady metallic rhythm beneath a low ventilation hum. Rain ticks against the window while paper rustles softly in her hands.

non_diegetic_music: Sustained cello notes at a slow tempo with widely spaced piano tones, gradually decreasing in volume.
```

**关键经验**：
- 指令行必须是第一行，后空一行
- `<Picture 1>` 是0.00秒的实际首帧
- 保持图中人物外观、服装、座位、车厢布局一致
- 从图中状态出发，向前发展动作

---

## 案例 5：FL2VA 首尾帧

**场景**：雨中骑车人撑伞，首帧=持伞关闭，尾帧=撑伞完成

**完整示例**：
```
How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot 1) aligns with the 8.00-second mark of the target video.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, a rain-soaked cyclist begins in the position and framing established by Picture 1, holding a closed black umbrella beside a silver bicycle. The camera pulls out with small amplitude at slow speed as she releases the bicycle handle, raises the umbrella above her shoulder, and presses the runner upward until the canopy opens. Water rolls from the expanding fabric while she steps beneath it, rotates the handle into the final angle, and settles into the pose, spacing, and composition established by Picture 2 at the end of the shot.

overall_soundscape: Rain falls steadily on the pavement, followed by the metallic click of the umbrella runner and the soft snap of the canopy opening. Water drips from the bicycle frame as distant traffic passes.

non_diegetic_music: N/A
```

**关键经验**：
- 默认单镜头，不拆分多镜头
- 不重复描述两张静态图，而是写连接两者的运动路径
- 尾帧必须在最后一个 Shot 结尾精确到达
- 无配乐写 `N/A`

---

## 案例 6：L2VA 尾帧

**场景**：玻璃杯摔碎，尾帧=碎玻璃最终状态

**完整示例**：
```
How the reference pictures align with the target video — <Picture 1> (from [Shot 1]) aligns with the 6.00-second mark of the target video.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, a close shot begins with an intact drinking glass near the edge of a dark wooden table, while the same hand and sleeve visible in <Picture 1> approach from the right. The camera pushes in with small amplitude at slow speed as the fingertips strike the rim. The glass tips, falls, and hits the floor with a sharp impact; cracks spread through it as fragments slide outward. Toward the end, the moving pieces lose momentum and settle into the exact broken arrangement, hand position, camera angle, lighting, and final composition established by <Picture 1>.

overall_soundscape: Fingertips tap the glass before it scrapes across the tabletop, falls, and breaks with a sharp crash. Small fragments scatter and gradually stop sliding across the floor.

non_diegetic_music: A low electronic pulse at a slow tempo, ending immediately after the glass breaks.
```

**关键经验**：
- `<Picture 1>` 是尾帧，属于最后一个 `[Shot N]`，不属于 Shot 1
- 先描述合理的前置状态（完整杯子在桌边）
- 再描述动作路径（手指推→杯子倒→摔碎→碎片滑出）
- 末镜头逐渐收敛到尾帧状态

---

## 案例 7：Ref2VA 7 人电竞天团宣传（精简优化标杆案例）

**场景**：7 个真人女性角色，每人专属配色 + 粒子效果 + 签名动作，音乐驱动 speed-ramp 节奏，末幕 7 人汇聚。17 秒，Ref2VA 模式，7 张参考图。

**精简价值**：此案例从 v4.0（~5380 词）精简到 v4.1（~2235 词），**词数减少 58%，信息零丢失**，是冗余压缩的最佳实践标杆。

**设计要点**：
- 使用 `[SHARED GUARDRAILS]` 统一光照/调色/静默/音乐/速度结构规则（全文 1 次）
- 使用 `[APPEARANCE RULES]` 集中防漏人/防重复/防换人指令（全文 1 次，替代 v4.0 中 20+ 次重复）
- 每个角色用差异化锚点区分（刘海类型 + 服装领型 + 配色）
- `detailed_description` 和末幕用短标签引用（如 `<S1> icy-cyan frost-shards headphone-sweep`）
- 全部角色 SILENT，纯视觉 + 音乐驱动

**subject_definitions 片段（精简版）**：
```
[SHARED GUARDRAILS] 7 DISTINCT real women, each from her own <Picture N>. Each
wears a THICK BRIGHT CONTINUOUS signature-color RIM-LIGHT OUTLINE — DOMINANT
lighting, ALWAYS VISIBLE, NEVER fades. Each has a UNIQUE PARTICLE EFFECT. ENTIRE
VIDEO: steely cold cinematic grading. ALL subjects SILENT. MUSIC-DRIVEN: dark
cinematic-trap / electronic, 140 BPM half-time, D minor. Each solo shot = one
musical cycle: BEAT 1 (0.4s) SPEED-RAMP FAST ENTRY, BEAT 2 (0.8s) SLOW-MOTION,
BEAT 3-4 (1.0s) NORMAL-SPEED SETTLE.

[APPEARANCE RULES] Shots 1-7: each shows ONLY its assigned <Subject N> — NO
skipping, NO substitution. Shot 8: ALL 7 appear SIMULTANEOUSLY — EXACTLY SEVEN,
NO duplicates, NO extras.

<Subject 1> from <Picture 1>. BLUNT BANGS (only one with blunt bangs). NARROW
ELONGATED SINGLE-FOLD EYES, cold high-cold gaze. Oval face, pale-pink lips, fair
skin. CLOSED BLACK CREW-NECK KNIT (only crew-neck). Color: ICY-CYAN. Particle:
FROST-CRYSTAL shards. Action: grips headphone cup, SWEEPS it back. Shot 1 (solo)
+ Shot 8 (TOP-FAR-LEFT).

<Subject 2> from <Picture 2>. SHORT OBLIQUE LEFT-PARTED BANGS (shorter than
S7's). LARGE ROUND DEER-LIKE EYES. Color: COOL-VIOLET. Particle: VIOLET
LIGHTNING arcs. Action: REVERSE-CROSS arm snap. Shot 2 + Shot 8 (TOP-CENTER-LEFT).
```

**detailed_description 片段（精简版 vs 冗余版对比）**：
```
// 精简版（~60 词）：
[Shot 1] SPEED-RAMP on <Subject 1> (BLUNT BANGS, CREW-NECK, ICY-CYAN): BEAT 1
camera AGGRESSIVE push-in + handheld drift, rim-light + frost-crystal shards
visible. BEAT 2 SLOW-MOTION: headphone-sweep action, cold side-gaze. BEAT 3-4
holds pose. Icy-cyan back-light + vertical cold-steam smoke. Music: sub-bass +
trap snare + pad + arpeggio.

// 冗余版（~180 词，其中 ~100 词是对 subject_definitions 的复述）：
[Shot 1] A MUSIC-DRIVEN AGGRESSIVE SPEED-RAMP on <Subject 1> at MEDIUM distance
— this shot MUST show <Subject 1> from <Picture 1> (BLUNT BANGS, CREW-NECK
sweater, ICY-CYAN rim-light): ON BEAT 1, sub-bass + trap snare punches in as
the camera AGGRESSIVELY pushes in... (后续 150 词省略)
```

**末幕片段（精简版）**：
```
[Shot 8] EXACTLY 7 DIFFERENT WOMEN, NO duplicates. TOP ROW (left→right):
<S1> icy-cyan frost-shards headphone-sweep, <S2> cool-violet lightning
reverse-cross, <S3> forest-green wind-swirl mouse-flick, <S4> amber-orange
solar-flare controller-spin. MIDDLE: seven-color particles CONVERGE to center,
title "高能集结" in NEON GLITCH FONT. BOTTOM ROW: <S5> neon-pink glitch
keyboard-flip, <S6> champagne pearl chair-spin, <S7> crimson fire-ember
hair-flip + fist-clench. Music: FULL BEAT DROP.
```

**retention_analysis 片段（精简版）**：
```
<Subject 1> (Shot 1, Shot 8 TOP-FAR-LEFT): fully_preserved per <Picture 1>.
<Subject 2> (Shot 2, Shot 8 TOP-CENTER-LEFT): fully_preserved per <Picture 2>.
...（7 行，每行 10 词，共 ~70 词）
```

**关键经验**：
- `[SHARED GUARDRAILS]` + `[APPEARANCE RULES]` 将共享规则从重复 11 次降到 1 次
- 短标签引用将末幕从每人 ~30 词降到每人 ~8 词（-73%）
- 防漏人指令从 20+ 次、~800 词降到 1 次、~30 词（-96%）
- 详见 `references/optimization-guide.md` 中的完整压缩前后量化对比

---

## 案例 8：Ref2VA 多幕叙事转场（哥特裙水下梦境 → 海岸夜景）

**场景**：2 位哥特裙模特，3 幕叙事（水下梦境 → 水母遮挡过场 → 海岸夜景现实），17 秒，4 张参考图。

**设计要点**：
- 使用 `<Prop 1>` 标签定义道具（黑蕾丝伞），与角色标签分离
- 一张 `<Picture 1>` 在不同 Shot 中被重新解读为不同场景（水下遗迹 → 海岸礁石）
- 物理转场驱动幕间切换（气泡爆发 → 水母遮挡 → 场景切换）
- `<Picture 4>` 作为"人设参考"引导性格气质、表情风格、互动关系
- 跨幕角色一致性通过 `fully_preserved per <Picture 3>` 保证

**subject_definitions 片段**：
```
<Subject 1> from <Picture 3>, <Picture 4>. Pale skin, fine almond-shaped dark
eyes, composed neutral expression. Long straight black hair with BLUNT BANGS,
white lace maid-style headband. Black long-sleeve Gothic Lolita dress: white
lace Peter Pan collar with large black bow, white corset lace-up, three-tier
white lace ruffle hem. White over-the-knee stockings. Black Mary Jane shoes.

<Subject 2> from <Picture 3>, <Picture 4>. Pale skin, blue-tinted irises, soft
gentle expression. Long straight light-golden-blonde hair, BLUNT BANGS, small
black formal hat with burgundy bow. Black STRAPLESS Gothic Lolita dress with
white lace sweetheart neckline, cross-lace corset front.

<Prop 1> from <Picture 2>. Black Chantilly-style lace parasol: Victorian rose
floral patterns, scalloped ruffled borders, 8-rib black metal frame.

<Picture 1> — scene reference: foggy abandoned Gothic courtyard, reinterpreted
as a submerged underwater ruin in Shots 1-2, then as a nighttime rocky seashore
in Shot 3.
```

**summary 片段**：
```
[reference generation] A 17-second three-shot dream narrative: a giant clam shell
rests in a rich underwater ruin (<Picture 1>). <Subject 1> and <Subject 2>
(<Picture 3>) pry it open, revealing a radiant pearl — bubbles erupt as
transition. Both models float weightlessly holding the pearl, circle each other,
lean in to kiss it — a jellyfish drifts between them as second transition. After
it clears, both sit fully clothed but soaking wet on a moonlit rocky shore,
roasting marshmallows over a campfire, the parasol resting beside them.
```

**detailed_description 转场技巧**：
```
[Shot 1] ... At 00:05, a massive burst of silver air bubbles erupts from inside
the shell — the bubble cloud fills the entire frame, gradually whites out the
image — the camera drifts upward through the bubble cloud, dissolving into pure
silver-white luminescence.

[Shot 2] At 00:06.000, the bubble cloud thins... At the exact instant their lips
are about to touch the pearl, a luminous jellyfish drifts into frame — its
tentacles drag across the lens, the frame fully consumed by jellyfish glow.

[Shot 3] At 00:12.000, the jellyfish glow fades — the scene has transformed.
Nighttime rocky seashore, playing at natural real-time speed...
```

**retention_analysis 片段**：
```
<Subject 1> (Shot 1, Shot 2, Shot 3): fully_preserved per <Picture 3>.
<Subject 2> (Shot 1, Shot 2, Shot 3): fully_preserved per <Picture 3>.
<Prop 1> (Shot 1, Shot 3): fully_preserved per <Picture 2>.
<Picture 1> (scene reference): fully_preserved — courtyard architecture
reinterpreted as underwater ruin (Shots 1-2) then nighttime seashore (Shot 3).
<Picture 4> (character design reference): fully_preserved — both models'
personality and interaction style guided throughout.
```

**关键经验**：
- `<Prop N>` 标签让道具定义与角色定义分离，retention 中独立声明
- 场景重构：一张 Picture 可在不同 Shot 中被重新解读（courtyard → underwater ruin → seashore）
- 物理转场（气泡/水母）是比 cross-dissolve 更自然的幕间切换方式
- `<Picture 4>` 作为"人设参考"扩展了 Picture 的用途：不仅定义外观，还引导性格和互动
- 跨幕叙事不需要 `<scenetrans>` 标签，因为幕间有物理转场事件自然衔接

---

## 风格化案例参考

### 旗袍模特6模特系列

用户实践中开发了多种风格变体，均为 Ref2VA 模式：

| 风格名 | 色调 | BGM | 运镜特点 |
|--------|------|-----|----------|
| 游园入梦 | 新中式诗意电影 | 钢琴+弦乐 | 缓推+横移，柔和 |
| 民国旧梦 | 王家卫复古胶片 | 弦乐+环境 | 固定+缓推，怀旧 |
| 高动态时尚大片 | Vogue/MV高对比 | 电子+鼓点 | 快推+弧形，动感 |
| 雨巷烟雨·江南水墨 | 去饱和黑白+淡蓝赭石 | 竹笛+古琴 | 缓横移+微仰，水墨 |
| 赛博国潮·霓虹东方 | 高对比霓虹品红青 | 电子合成+古筝120BPM | 快推+甩镜，赛博 |

### 直播带货 Ref2VA 案例

**场景**：NB启动器直播带货，人物图+直播间截图+软件界面截图+LOGO

**设计要点**：
- 人物图作为 `<Subject 1>` 外观参考
- 直播间截图作为场景参考
- 软件界面截图作为画面内可见内容
- LOGO作为品牌元素
- 旁白需突出产品卖点，台词字数严格控制

### 循环讲解 IP 系列

**场景**：猫耳女仆少女IP，循环讲解视频，多变表情版

**设计要点**：
- 每个循环变体使用不同的表情和手势组合
- 末尾必须回到起始姿势
- 音色克隆音频标注在首次台词
- 背景保持不变（如粉色圆形背景）

---

## 提示词字符数管理

| 场景 | 典型字符数 | 管理策略 |
|------|-----------|----------|
| T2VA 简单场景 | 800-1,500 | 无需特别管理 |
| I2VA/FL2VA/L2VA | 1,000-2,000 | 无需特别管理 |
| Ref2VA 单主体 | 2,000-4,000 | 注意 detailed_description 长度 |
| Ref2VA 多主体（3-5个） | 4,000-6,000 | 精简每个 Subject 的外观描述 |
| Ref2VA 8+主体 | 6,000-7,000 | 极度精简，仅保留最强识别点 |

**精简策略**：
1. 合并相似主体的描述模板，只写差异点
2. retention_analysis 可用简短列表格式
3. overall_soundscape 控制在2句以内
4. 非关键运镜细节省略幅度和速度修饰
5. 旁白台词精简到最少字数
