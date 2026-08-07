# H3 五模式官方格式速查

来源：官方英文指南 VIDEO_PROMPT_WRITING_GUIDE_base_en.md + VIDEO_PROMPT_WRITING_GUIDE_ref_en.md，并与中文 PDF《视频提示词使用说明.pdf》核对（2026-08-04）。

## 1. 模式总览

| 模式 | 技术名 | 检查点 | 说明 |
|---|---|---|---|
| 文生视频 | T2VA | H3-Base-FL2VA | 纯文本构建完整视听时间轴 |
| 图生视频 | I2VA | H3-Base-FL2VA | T2VA 主体 + 首帧指令 + 从首帧向前发展 |
| 首尾帧 | FL2VA | H3-Base-FL2VA | T2VA 主体 + 首尾帧指令 + 首帧到尾帧的连续路径 |
| 尾帧 | L2VA | H3-Base-FL2VA | T2VA 主体 + 尾帧指令 + 从合理前置状态收敛到尾帧 |
| 全能参考 | Ref2VA | H3-Base-Ref2VA | 六节格式，多素材（图/视频/音频）参考 |

帧率：24fps 下 17k+5 帧网格，时长向上取整对齐。Ref2VA 素材上限：9 图 3 视频 3 音频共 12 个，每段 2-15 秒，音频不可单独使用。

## 2. Base 系格式（T2VA / I2VA / FL2VA / L2VA）

### 2.1 第一部分：开头指令行

T2VA 无指令行，直接写三字段。其余模式的指令行**必须是提示词第一行，之后空一行再写三字段**。

- I2VA 固定句式：
  ```
  For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.
  ```
- FL2VA 固定句式：
  ```
  How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot N) aligns with the S.SS-second mark of the target video.
  ```
- L2VA 固定句式：
  ```
  How the reference pictures align with the target video — <Picture 1> (from [Shot N]) aligns with the S.SS-second mark of the target video.
  ```

`N` 为实际最后一个 shot 的序号，`S.SS` 为有效视频时长（恰好两位小数）。

### 2.2 第二部分：三核心字段

```
integrated_multimodal_description: [Shot 1] ...

overall_soundscape: ...

non_diegetic_music: ...
```

- integrated_multimodal_description：沿时间轴描述画面、动作、镜头、说话人、台词、唱歌、剧情内声音。
- overall_soundscape：全片环境音、物理动作声、非语言人声总结。
- non_diegetic_music：角色听不到、仅观众可闻的背景音乐。

### 2.3 各模式关键帧叙事公式

- **I2VA**（从图出发向前发展）：`<Picture 1>` 是 0.00 秒的实际首帧，属于 `[Shot 1]`。先建立图中风格、主体、构图、场景锚点，再描述接下来的动作；人物身份、服装、色彩、关键物体、空间关系保持一致。结构：**首帧锚定 → 动作启动 → 连续发展 → 结果或反应**。
- **FL2VA**（描述首尾帧之间的路径）：图1是开头、图2是结尾。正文不要重复两张静态图描述，而是补全连接两者的运动路径：主体如何移动、姿态如何变化、物体如何被操作、构图如何演变、光影如何过渡。**默认单镜头**（用户明确要求才多镜头），尾帧必须由最后一个 `[Shot N]` 在视频结尾精确到达。结构：**首帧状态 → 可观察的中间变化 → 差异逐渐收窄 → 尾帧状态**。
- **L2VA**（反推开场、结尾落图）：`<Picture 1>` 是视频尾帧，属于最后一个 `[Shot N]`，**不属于 Shot 1**。先根据用户意图和尾帧反推合理的前置状态，再描述人物、物体、镜头、场景如何逐步接近参考图。结构：**合理前置状态 → 明确的动作与过渡路径 → 末镜头逐渐收敛 → 尾帧落位**。

## 3. Ref2VA 六节格式（全能参考）

六个带标签的节，顺序固定：

```
subject_definitions:
<Subject 1> is the young woman in <Picture 1>, ...（外观细节）
<Audio 1> is the voice-timbre reference for <Subject 1> (S1), ...

summary:
[task type 前缀] The target video shows ...（一句话概述 + 参考关系）

retention_analysis:
<Subject 1> (appears in [Shot 1], [Shot 3]): fully_preserved - ...
<Audio 1>: reference - vocal timbre guides dialogue without copying the signal.

detailed_description:
[风格句]. [Shot 1] [景别] [动作+音效]. <Subject 1> (S1) says: <d>[Language] 台词</d>

overall_soundscape:
[1-4 句全片声音总结]

non_diegetic_music:
N/A（或 1-3 句配乐描述）
```

### 标签体系

`<Subject N>`（定义的主体）、`<Picture N>`（图片）、`<Video N>`（视频）、`<Audio N>`（音频）。标签一经分配，在全部节中含义不变。

### summary 任务类型前缀

用 ` + ` 组合，不重复：
- `reference generation`（提供参考引导）
- `video editing`（直接编辑源视频）
- `video continuation`（续写源视频）
- `keyframe completion`（作为具体帧）
- `audio reuse`
- `audio reference`

例：`[reference generation + audio reference]`、`[video continuation + keyframe completion]`。仅有视频/音频素材不自动产生对应任务类型；只提供运镜/节奏的参考视频属于 reference generation。

### retention_analysis 保留级别

视觉：
- `fully_preserved`（定义角色完整保留）
- `partially_preserved`（部分特征改变或仅部分保留）
- `attribute_transfer`（特征迁移到不同主体）
- `weak_reference`（仅风格/类别/构图/氛围的大致相似）

音频：
- `fully_copy`（1:1 作为最终完整音轨）
- `partially_copy`（部分复制或增删替换）
- `reference`（仅音色引导）
- `weak_reference`

### 音色来源标注

detailed_description 中角色首次台词须标注音色来源：`<Subject 1> (S1), using the voice timbre referenced from <Audio 1>, says: <d>...`，后续台词可省略。

## 4. 共享写作规则（所有模式通用）

### 4.1 镜头与切换

- `[Shot 1]` 开头先写整体风格与初始构图（Cinematic / live-action / 2D-animated / 3D CG / claymation / watercolor / vintage film 等），**不加时间戳**。
- 后续镜头：`[Shot 2] At 00:03.500, the camera cuts to...`，时间严格递增且在总时长内。
- 普通切换动词：the camera cuts to / the shot cuts to / the shot transitions to / the shot changes to / the shot switches to；用户明确要求时才用 cross-dissolve、fade、wipe。
- 切换应引入主体/空间/状态/视角/时间的新信息；只变距离或轻微角度时优先用运镜。

### 4.2 运镜词表（motion type + amplitude + speed）

- Motion type：Zoom In / Zoom Out（变焦）、Push In / Pull Out（前后移动）、Pan Left / Pan Right（水平摇）、Truck Left / Truck Right（水平移）、Tilt Up / Tilt Down（垂直摇）、Pedestal Up / Pedestal Down（整体升降）、Arc Shot（环绕）、Tracking Shot（跟拍）、Static Shot（静止）、Shake Slightly / Shake Strongly（抖动）、POV（主观视角）、Roll Clockwise / Roll Counterclockwise（滚转）。
- Amplitude：with small amplitude / with large amplitude。
- Speed：at slow speed / at fast speed。
- 中等幅度与正常速度通常省略；运镜写成镜头内自然英语动作，不要在句尾堆标签。例：`The camera pushes in with small amplitude at slow speed toward the folded letter in her hands.`

### 4.3 说话人与台词

- 说话/唱歌/画外音主体用稳定 ID `(S1)` `(S2)`，跨镜头不变；从不发声的角色不给 ID；多人齐说用复合 ID `(S1,S2)`。
- 说话人首次出现时给出足够身份信息（类型、年龄、性别、是否出镜、音高、音色、语速、口音）。说话人描述、ID、动作、语气放 `<d>` 外；`<d>` 内只有语言标签和逐字原文，不翻译不改写：
  `The young woman with a quiet, breathy voice (S1) says: <d>[English] I get off at the next station.</d>`
- 画外音固定短语 `says in an off-screen voiceover`，且每个画外音 `<d>` 块后必须写明嘴唇闭合：
  `The man (S1) says in an off-screen voiceover: <d>...</d> while his lips remain completely closed.`
- 台词跨镜头：在两处连接点用 `<scenetrans>`，并明说音频连续，可用：continues seamlessly across the cut / continues uninterrupted into the next shot / carries over from the previous shot / remains audible across the transition。
- 视频结尾截断台词用 `<cutoff>`。
- 画面可见文字（横幅、招牌、标签、字幕、霓虹字）用英文双引号包裹并逐字保留：`A red neon sign reading "营业中" glows above the doorway.`

### 4.4 overall_soundscape

1-4 句连续英文段落，总结全片环境音、物理动作声、非语言人声（风雨、交通、脚步、衣物、撞击、呼吸、笑声、喘息）。台词、唱歌、剧情内音乐已属 multimodal description，不要重复。仅当用户明确要求全程完全静音才写 `N/A`。

### 4.5 non_diegetic_music

1-3 句描述角色听不到的配乐：聚焦配器、速度、节奏、动态变化；不用抽象情绪词，不解释配乐的情感功能。角色可闻的唱歌/乐器/收音机/电视/手机音乐是剧情内事件，写进 multimodal description。无配乐写 `N/A`。

### 4.6 时长与台词

- Ref2VA 每段 2-15 秒。中文语速约 4.5 字/秒：台词字数 ÷ 4.5 ≤ 镜头时长（4 秒镜头最多 18 字）。
- 描述用英文，台词保留原文语言放在 `<d>[Language] ...</d>` 中。

## 5. 官方完整示例

### Case 1: T2VA
```
integrated_multimodal_description: [Shot 1] Live-action, cinematic, a medium-wide shot frames a baker opening the shutters of a small street bakery before sunrise. The camera pushes in with small amplitude at slow speed as the middle-aged baker with a calm, slightly raspy voice (S1) places a fresh loaf on the wooden counter and says: <d>[English] First batch of the morning.</d> [Shot 2] At 00:05.000, the camera cuts to a close-up of steam rising from the sliced bread while the baker's final words carry over from the previous shot.

overall_soundscape: Wooden shutters scrape open over a quiet street as trays clink softly inside the bakery. The doorbell rings once, followed by light footsteps and the crisp sound of bread being sliced.

non_diegetic_music: A soft acoustic-guitar pattern at a moderate tempo, joined by sparse upright-bass notes and a gentle fade at the end.
```

### Case 2: I2VA
```
For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, the young woman shown in <Picture 1> remains beside the rain-covered train window, preserving her appearance, clothing, seat position, and the carriage layout. The camera trucks right with small amplitude at slow speed as she lifts her gaze from the folded letter toward the passing city lights. Her reflection moves across the glass while the quiet, breathy young woman (S1) says: <d>[English] I get off at the next station.</d> She folds the letter along its existing crease.

overall_soundscape: The train wheels produce a steady metallic rhythm beneath a low ventilation hum. Rain ticks against the window while paper rustles softly in her hands.

non_diegetic_music: Sustained cello notes at a slow tempo with widely spaced piano tones, gradually decreasing in volume.
```

### Case 3: FL2VA（8 秒单镜头）
```
How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot 1) aligns with the 8.00-second mark of the target video.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, a rain-soaked cyclist begins in the position and framing established by Picture 1, holding a closed black umbrella beside a silver bicycle. The camera pulls out with small amplitude at slow speed as she releases the bicycle handle, raises the umbrella above her shoulder, and presses the runner upward until the canopy opens. Water rolls from the expanding fabric while she steps beneath it, rotates the handle into the final angle, and settles into the pose, spacing, and composition established by Picture 2 at the end of the shot.

overall_soundscape: Rain falls steadily on the pavement, followed by the metallic click of the umbrella runner and the soft snap of the canopy opening. Water drips from the bicycle frame as distant traffic passes.

non_diegetic_music: N/A
```

### Case 4: L2VA（6 秒单镜头）
```
How the reference pictures align with the target video — <Picture 1> (from [Shot 1]) aligns with the 6.00-second mark of the target video.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, a close shot begins with an intact drinking glass near the edge of a dark wooden table, while the same hand and sleeve visible in <Picture 1> approach from the right. The camera pushes in with small amplitude at slow speed as the fingertips strike the rim. The glass tips, falls, and hits the floor with a sharp impact; cracks spread through it as fragments slide outward. Toward the end, the moving pieces lose momentum and settle into the exact broken arrangement, hand position, camera angle, lighting, and final composition established by <Picture 1>.

overall_soundscape: Fingertips tap the glass before it scrapes across the tabletop, falls, and breaks with a sharp crash. Small fragments scatter and gradually stop sliding across the floor.

non_diegetic_music: A low electronic pulse at a slow tempo, ending immediately after the glass breaks.
```
