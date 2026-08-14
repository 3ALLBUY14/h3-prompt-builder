# H3 数字人对口型实战指南（防火星语专项）

来源：奥义护肤礼盒三版本（专业广告版 / 灵动版 / 产品特写转场版）Ref2VA 实战调优总结。适用于数字人口播、人物+音频对口型场景。

## 1. 音频策略选择

| 策略 | 含义 | 火星语风险 | 适用 |
|------|------|-----------|------|
| `fully_copy` | 原音频 1:1 作为最终音轨 | 低（音频内容固定） | 保留原始录音；BGM/SFX 后期加 |
| `reference` | 音频作音色参考，模型生成新音轨 | 高（生成语音可能跑偏） | 需要模型叠加 BGM/SFX、或内容重构 |

reference 模式下必须用 `<d>` 台词引导语音内容，否则模型自由发挥 → 火星语。

## 2. 台词归属三选一（防火星语核心规则）

| 归属方式 | 画面要求 | 标准写法 |
|----------|----------|----------|
| 口型同步 | 角色露脸，嘴部清晰可见 | `<Subject 1> (S1), using the voice timbre referenced from <Audio 1>, speaks to the camera... She says: <d>[Chinese] ...</d>` |
| 明确画外音 | 露脸不说话（嘴部放松、表情自然）或完全出镜均可 | `says in an off-screen voiceover: <d>...</d> while her lips remain completely closed.` |
| 无台词 | 任意画面（纯产品 B-roll 常用） | 不放任何 `<d>` 标签，镜头只描述画面+音效 |

**唯一会触发火星语的形态：归属缺省**——画面有角色、`<d>` 台词存在、但没有写清楚谁在说/以什么方式说。模型默认让角色开口，嘴部又不可见或太模糊，只能瞎生成口型和语音。

画外音要生效需双保险：

1. **归属显式**：明确写 off-screen voiceover，不要一笔带过
2. **嘴部状态正面描述**：`lips remain completely closed`，配合低头看产品、微笑等自然动作，让模型知道这张脸不在说话

## 3. 五大实战原则

1. **台词归属显式声明**：口型同步 or 明确画外音，二选一写清楚；露脸镜头配画外音完全可行，前提是标注够明确
2. **正面描述 > 负面约束**：不写"不要露齿/不说话"，写"嘴唇轻合/嘴部放松"；负面词会被模型反向聚焦
3. **`<d>` 台词全程覆盖**：reference 模式下模型为整段视频生成语音，`<d>` 之间空档会被火星语填充
4. **低饱和 > 高饱和**：特效字用柔金 #BFAA82、暖白 #E8E2D8、灰绿 #9AAA9C、柔砖红 #BC7A68 + medium-weight 字体
5. **文字叠加 ≠ 台词格式**：亮点词与 `<d>` 写法区分开，长串中英混合拆分弹出

## 4. 完整坑位清单

### 火星语类

| 现象 | 原因 | 修复 |
|------|------|------|
| 产品特写 B-roll 镜头念火星语 | 镜头无人物，台词归属缺省 | 台词移到人物镜头，或明确画外音+嘴唇闭合；纯产品镜头不放 `<d>` |
| 手持产品特写火星语 | 纯手部特写，嘴部只在画面边缘模糊可见，归属又不明确 | 改为胸前持物中近景，面部+嘴部清晰入镜做口型同步；或双保险画外音 |
| reference 模式中段空档火星语 | `<d>` 台词没有覆盖全程 | 台词按时间分段覆盖全程，不留空档 |
| 纯音频驱动（无 `<d>`）快语速段失同步 | 波形驱动精度不足 | 加 `<d>` 文本锚点，文本+音频双重引导 |

### 口型/嘴部类

| 现象 | 原因 | 修复 |
|------|------|------|
| "不露齿"约束反而呲牙 | 负面描述反向聚焦 | 删负面词，改正面："lips barely parting for each word" + "smiles softly with lips gently together" |
| 音频超 15s 中段加速口胡 | H3 上限 15s，超限强制压缩 | 音频控制在 14-15s，重录而不是靠提示词救 |
| "限时抢购"四字连读口胡 | 连读发音难 | CTA 用短促词："抢购吧" |

### 文字渲染类

| 现象 | 原因 | 修复 |
|------|------|------|
| "AOIAOI礼盒"渲染成"AOI礼盒" | 长串英文字母识别不准 | 拆分显示：先弹"AOI"，再弹"护肤礼盒" |
| "精华·面霜·眼霜"渲染两行 | `<d>` 台词与叠加文字内容重复，模型把两者都渲染 | 格式区分：台词"精华、面霜、眼霜"，叠加"精华 + 面霜 + 眼霜" |

### 视觉质感类

| 现象 | 原因 | 修复 |
|------|------|------|
| 特效字配色俗气 | 高饱和色（#D4AF37/#D4735A）+ bold 粗体 | 低饱和莫兰迪色 + medium-weight elegant sans-serif + subtle semi-transparent dark stroke |
| 颈部区域异常（光线/肤色怪异） | 颈部过度描述引发模型聚焦 | 颈部极简：只写 choker 等饰品，不写肤色/光线/边界 |

## 5. 嘴部/口型正面描述词库

固定组合（直接复用）：

- 嘴唇形态：`small delicate cherry lips with a narrow lip width`
- 说话时：`small, refined mouth movements that maintain her delicate cherry-lip appearance, lips barely parting for each word, maintaining a soft, delicate lip shape`
- 静止时：`lips that rest softly together`
- 微笑时：`smiles softly with lips gently together`
- 画外音时：`while her lips remain completely closed`

禁止出现的负面句式（会被反向聚焦）：never showing teeth / no teeth / 不要露齿 / 不要大嘴 / 不呲牙 / no wide mouth

## 6. 特效字规范

### 字体样式

- 字体：`medium-weight elegant sans-serif`（不用 bold）
- 描边：`subtle semi-transparent dark stroke`
- 阴影：`gentle soft shadow`
- 背板：`semi-transparent dark blur backdrop`
- 字号：约画面高度的 4%

完整示例句式：`Soft muted gold (#BFAA82) text reading "AOI" in a medium-weight elegant sans-serif font with a subtle semi-transparent dark stroke, gentle soft shadow, and a semi-transparent dark blur backdrop, slides in from the left edge of the frame and settles in the upper-left area with a soft glow.`

### 推荐色板（低饱和莫兰迪系）

| 颜色 | 色值 | 用途 |
|------|------|------|
| 柔金 soft muted gold | #BFAA82 | 品牌/效果类亮点词 |
| 暖白 warm white | #E8E2D8 | 产品内容类 |
| 灰绿 muted sage | #9AAA9C | 成分/卖点类 |
| 柔砖红 muted terracotta | #BC7A68 | CTA 促单类 |

避免：#D4AF37（高饱和金）、#D4735A（高饱和珊瑚红）等刺眼色值。

### 渲染防错

- 长英文串拆分弹出（AOIAOI → "AOI" + "护肤礼盒"）
- 叠加文字与 `<d>` 台词同内容时改格式（"、"→"+"）
- 中文文字渲染不完美属常态，重要文字建议剪映/PR 后期叠加

## 7. 质量自检补充清单

- [ ] 台词归属：口型同步 / 明确画外音 / 无台词，三选一写清楚，无归属缺省
- [ ] reference 模式：`<d>` 台词按时间分段覆盖全程
- [ ] 音频时长 ≤ 15s
- [ ] 嘴部描述：全部正面句式，无负面词
- [ ] 画外音镜头：有 "lips remain completely closed" 说明
- [ ] 特效字：与台词格式区分、长文字拆分
- [ ] 特效配色：低饱和色板 + medium-weight 字体
- [ ] 颈部描述极简：只写饰品

## 8. 实战案例参考

奥义护肤礼盒（15s 数字人口播，Ref2VA，音频 14.68s）三版本结构：

| 版本 | 镜头结构 | 产品展示方式 | 台词归属 |
|------|----------|--------------|----------|
| 专业广告版 | 3 镜头：人物中景 → 捧产品中近景 → 人物中近景 | 角色胸前持物，面部+嘴部清晰可见 | 全部口型同步 |
| 灵动版 | 单镜头：arc + handheld + push-in | 桌上礼盒 | 全部口型同步 |
| 产品特写转场版 | 3 镜头：人物 → 纯产品 B-roll（闪光切入/闪粉溶解）→ 人物 | 特写 B-roll + 粒子/光晕特效 | 人物镜头口型同步，产品镜头无台词 |
