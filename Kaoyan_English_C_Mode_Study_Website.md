**考研英语 C 模式学习网站**

**项目交接文档 · 产品 × 内容 × 工程 × 生产运维**

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><p><strong>交接目标</strong></p>
<p>让新的 ChatGPT 账号、Codex 会话或开发者无需翻旧聊天，就能从当前真实仓库状态继续工作；同时避免覆盖线上成品、重复讨论已冻结决策、误把技术生成成功当成艺术验收完成。</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

| **快照日期**     | 2026-10-03                                             |
|------------------|--------------------------------------------------------|
| **GitHub 仓库**  | dannhitruong852-jpg/taptap                             |
| **线上入口**     | dannhitruong852-jpg.github.io/taptap/kaoyan-reader-v1/ |
| **当前线上内容** | 2002–2026 · 25 年 · 172 篇                             |
| **核心音频路线** | Chatterbox · C 模式 · 静态预生成                       |
| **外部服务**     | GitHub Pages / Actions + Supabase 词群本同步           |

**最重要的一句话**

**线上 \`gh-pages\` 已经不是“纯部署分支”，它比旧开发分支前进 282 个提交，并承载 2002–2026 的生产内容与后续源码；任何迁移都必须先保护它，不能用旧 \`kaoyan-reader-v1\` 分支直接覆盖。**

**READ FIRST**

# **0. 接手者先读：当前真实状态**

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><p><strong>P0 · 禁止直接覆盖 gh-pages</strong></p>
<p>2026-10-03 核对：`gh-pages` 相对 `kaoyan-reader-v1` 为 ahead 282 / behind 0，merge base 正是旧开发分支 HEAD。生产分支上还混有后续生产源码、测试、工作流以及其他 Pages 资产（例如 2026-10-02 发布的浙大 MAP 打印文件）。新账号第一步是做快照/备份和分支治理，不是继续批量生成。</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

| **对象**   | **2026-10-03 快照**                   | **交接含义**                                                          |
|------------|---------------------------------------|-----------------------------------------------------------------------|
| 旧开发分支 | kaoyan-reader-v1 @ 7dbbd4da1296…      | 停在 2026-09-16；只看到内容/音频至 2012，已明显落后线上。             |
| 线上分支   | gh-pages @ 1992d6bec9de…              | 当前 HEAD 是共享 Pages 的后续提交；Reader 最近可见变更在 2026-09-29。 |
| 分支关系   | gh-pages ahead 282 / behind 0         | 线上分支已成为事实上的最新生产+源码载体；不要反向覆盖。               |
| 线上目录   | 2002–2026 · 25 年 · 172 篇            | 内容和音频目录均覆盖到 2026。                                         |
| 源材料     | 2000–2026 年真题 PDF + 英语二词汇宝典 | 2000–2001 已有源 PDF，但当前 catalog 从 2002 起。                     |
| 词群本同步 | Supabase 项目 ACTIVE_HEALTHY          | 已是正式外部依赖，迁移账号时要同步权限。                              |

## **本文件的“事实优先级”**

1\. GitHub 当前仓库状态与生产文件，是第一事实来源。

2\. \`PRODUCTION_RULES.md\` 与 Production V2 规范，是生产操作的约束。

3\. \`docs/standards/\` 下的双语高亮与播放延迟标准，是验收底线。

4\. \`kaoyan-reader-v1/content/catalog.json\` 决定当前可见内容清单。

5\. 旧交接文档与聊天记录只用于解释历史，不得覆盖当前仓库事实。

## **建议新账号的阅读顺序**

| **顺序** | **必须阅读**                                   | **目的**                                                 |
|----------|------------------------------------------------|----------------------------------------------------------|
| 1        | PRODUCTION_RULES.md                            | 先知道什么不能做，尤其是不可 force-push / 不可删旧内容。 |
| 2        | docs/production/C_MODE_PRODUCTION_V2.md        | 掌握唯一 canonical 生产流程。                            |
| 3        | docs/standards/BILINGUAL_HIGHLIGHT_STANDARD.md | 理解 6+ 难词中英联动的编辑与发布 gate。                  |
| 4        | docs/standards/PLAYBACK_LATENCY_STANDARD.md    | 理解缓存、Web Audio、全库预载与延迟目标。                |
| 5        | kaoyan-reader-v1/content/catalog.json          | 确认当前年份、篇数、路由和 manifest。                    |
| 6        | 本交接文档                                     | 补齐产品原则、迁移步骤、风险与决策背景。                 |

**PRODUCT**

# **1. 产品定义与用户价值**

这是一个面向考研英语真题精读的手机优先学习网站。它不是刷题系统，也不是“上传 PDF 后即时 AI 解析”的工具；核心价值是把历年真题文章预处理成可以直接阅读、对照翻译、识别重点词、逐句听读和反复复习的静态学习资料。

理想用户路径只有一条：打开网址 → 选年份 → 选题型/文章 → 看英文/中文 → 看难词 → 点播放。用户不希望注册、配置模型、购买 TTS API、反复授权，也不希望自己整理 PDF。后续加入的词群本与跨设备同步应保持“可选增强”，不能把原本零门槛的主流程变复杂。

## **产品原则**

- 手机优先：Android / vivo 等移动浏览器体验优先于桌面。

- 理解优先：翻译、词汇与声音都服务于理解真题，而不是炫技。

- 运行时轻量：正文、译文、导演信息和音频原则上预生成，页面负责读取与播放。

- 内容忠实：只保留考试真正需要阅读的正文，不混入题干、选项、解析或作文。

- 可重复验证：内容、音频、发布都要有 machine-readable gate，不能靠“看起来差不多”。

## **明确不做**

- 不做在线实时 TTS，不依赖商业按字/按分钟 TTS 作为核心运行路径。

- 不把浏览器 \`speechSynthesis\` 重新作为正式方案；历史上在 vivo/Android 兼容性不足。

- 不做 Writing（小作文/大作文/图表/范文）内容。

- 不把选择题选项、答案解析、Directions 混进阅读页。

- 不使用可识别真人声纹克隆；名人姓名只能作为“气质锚点”，不能冒充。

**CONTENT**

# **2. 内容范围与编辑规则**

## **四类正文范围**

| **题型**              | **最终网站保留**       | **必须剔除 / 特殊处理**                            |
|-----------------------|------------------------|----------------------------------------------------|
| 完型 / Use of English | 恢复后的完整文章正文   | 必须按正确答案补回空；选项只用于恢复，最终不可见。 |
| 阅读 Part A           | Text 1–4 文章正文      | 删除题干、问题、A/B/C/D、答案解析。                |
| Part B / 新题型       | 真正需要阅读的正文     | 删除备选项、小标题列表、人名匹配列表、Directions。 |
| Translation           | 要求考生翻译的英文原文 | 删除题目说明；最终页面仍按句配中文学习译文。       |

当前线上 catalog：2002–2004 每年 6 篇（无 Part B）；2005–2026 每年 7 篇。

## **翻译标准：信、达、雅**

“信”优先于“雅”。不能漏译、不能擅自增加意思、不能改变因果/否定/比较/限定范围；“达”要求中文顺畅而不是搬运英语句法；“雅”只在准确基础上追求自然、好读。英文句子与中文译句保持一一对应。

批量翻译后必须进行第二轮校对，至少覆盖：漏译、否定、比较、主从关系、代词指代、专有术语、语义范围、生硬直译。不能为了后续高亮方便而重写一条本来更自然的译文。

## **词汇体系：1–9 级**

项目使用自己的 1–9 级学习标尺；6 级及以上是重点，页面进行突出显示。它不是官方分级。等级应跨年份尽量稳定，可结合《大纲词汇背诵宝典（英语二）》、词频、超纲程度、构词复杂度和语境义，而不是“某一篇临时觉得难就加级”。

## **双语难词高亮生产标准**

当前标准已经从“英文单边加粗”升级为中英联动。每个 level ≥ 6 的英语词汇 occurrence 要么有一个经过审核的中文对应 span，要么有明确的 reviewed exception；发布前必须满足完整覆盖。映射采用精确字符 offset，不允许运行时模糊猜测。

> required_occurrences == mapped_occurrences + reviewed_exceptions

- Canonical mapping：\`content-pipeline/curated/bilingual-highlights/\<year\>.json\`。

- Reader-ready mapping：\`kaoyan-reader-v1/content/\<year\>/bilingual-highlights.json\`。

- 禁止：运行时 fuzzy matching、运行时 AI 翻译、字典自动猜中文对应、为高亮而修改译文。

- 2003–2006 已锁定的 required inventory：71 / 85 / 24 / 28，共 208 个 occurrence。

**AUDIO DIRECTION**

# **3. C 模式声音系统**

正式声音定位：理解导向的美式日常交流式朗读。它不是有声书、新闻播音、纪录片腔、广播剧，也不是教学慢速英语。目标感觉是：一个真正读懂文章的人，面对面把内容讲给另一个人听。

## **C 模式 8 条不可丢的原则**

1\. 理解优先于表演：听者应更容易听出重点、逻辑、态度与结构。

2\. neutral ≠ flat：旁白也必须有自然重音、停顿、轻重、句尾走势和信息焦点。

3\. 角色稳定、韵律变化：一篇文章不要为了“丰富”频繁换演员。

4\. 导演顺序固定：语义理解 → 话语功能 → 信息焦点 → 逻辑关系 → 韵律曲线 → 情绪/强度 → 速度/停顿/重音。

5\. 微反差密度：任意连续约 2–3 句应有可感知但克制的波峰；硬 validator 不允许 3 句全为 low。

6\. 强表演必须有文本依据：对白、强转折、讽刺、笑点、冲突、强评价、惊讶等才加强。

7\. 长句允许句内变化：按语义转折和信息焦点调整节奏、重音和语气。

8\. 自然交谈速度：复杂处自然稍慢，简单/动作/轻松处稍快，不做教学慢速。

## **机器约束 / 典型参数**

| **参数**       | **约束 / 说明**                                                              |
|----------------|------------------------------------------------------------------------------|
| 默认口音       | 历史 C 总纲默认 en-US，但当前文章可以显式覆盖；例如 2026 Text 3 已是 en-GB。 |
| 节奏           | natural_conversational。                                                     |
| contrast       | low / micro / strong；连续 3 句不能全部 low。                                |
| rate           | 常用约 0.92–1.06，硬范围 0.80–1.10。                                         |
| 人工停顿       | V4 manifest 约束 artificial_pause_ms = 0。                                   |
| 后处理变速     | V4 manifest 约束 post_tempo = false。                                        |
| 新音频首部空白 | 非意图 leading silence 通常目标 ≤ 150 ms。                                   |

## **15 槽位演员体系**

| **ID** | **人格定位**        | **使用原则**                                                                         |
|--------|---------------------|--------------------------------------------------------------------------------------|
| 01     | 理性思想型男声      | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |
| 02     | 学术解释女声        | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |
| 03     | 纪录片型男声        | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |
| 04     | 冷静纪录片女声      | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |
| 05     | 演讲 / 交流型男声   | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |
| 06     | 年轻自然女声        | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |
| 07     | 温暖故事女声        | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |
| 08     | 科技 / 干幽默男声   | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |
| 09     | 权威评论男声        | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |
| 10     | 锐利评论女声        | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |
| 11     | 少女 / 儿童感角色声 | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |
| 12     | 青年男性角色        | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |
| 13     | 年长男性角色        | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |
| 14     | 成熟英式男声        | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |
| 15     | 清晰英式女声        | 由系统按文章语义与说话者分配；普通文章通常 1–4 人，仅真实说话者变化/对白才优先换声。 |

11 号角色声避免使用未经适当授权的真实未成年人素材；可用成年表演者或合法生成的 childlike register。

## **TTS 技术路线**

正式路线锁定为原版英文 Chatterbox，利用 voice reference、CFG、exaggeration 等参数在生产阶段生成静态音频。网站运行时只读音频文件。历史 CMU ARCTIC reference 主要用于工程验证，不等于最终 15 人完整演员库。

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><p><strong>艺术验收 ≠ 技术生成成功</strong></p>
<p>当前 V4 manifest 可见 technical QA 与强制对齐通过，但 `voice`、`c_direction` 仍可能标记 `pending`。后续任何人都不能因为文件存在、技术 QA 绿灯或 Pages 可播放，就宣称声音艺术验收全部完成。</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

**PRODUCTION INVENTORY**

# **4. 当前线上内容资产**

## **已发布目录规模**

| **范围**  | **年份数** | **每年结构**                        | **篇数** |
|-----------|------------|-------------------------------------|----------|
| 2002–2004 | 3          | 1 完型 + 4 阅读 + 1 翻译            | 18       |
| 2005–2026 | 22         | 1 完型 + 4 阅读 + 1 Part B + 1 翻译 | 154      |
| 合计      | 25         | —                                   | 172      |

线上 \`gh-pages/kaoyan-reader-v1/content/\` 与 \`audio/\` 均已经出现 2002–2026 年目录。源文件库存实际上覆盖 2000–2026，因此 2000–2001 是“有源材料但未进入当前 live catalog”的明确缺口，是否补齐应作为后续产品决策，而不是误以为已经发布。

## **Catalog 数据契约**

当前 \`content/catalog.json\` 的每条 article 记录至少包含：\`id\`、\`year\`、\`section_type\`、\`title\`、\`content\`、\`manifest\`、\`sentences\`。Reader 不应该写死某个年份；年份/题型/文章选择都应从 catalog 驱动。

> 例：2026-text3 → content ./content/2026/c/text3.json → manifest ./audio/2026/v4/c-text3/manifest.json

## **文章 JSON 的核心字段**

| **层级** | **关键字段**                                                          | **用途**                   |
|----------|-----------------------------------------------------------------------|----------------------------|
| Article  | year / section_type / article_id / source_sha256 / source_pages       | 来源追溯、路由、原文校验。 |
| Article  | accent / primary_actor_id / article_context / editorial_status        | 声音与编辑上下文。         |
| Sentence | id / paragraph / en / zh / vocab                                      | 双语阅读与词汇。           |
| Sentence | discourse_function / prosody_focus / contrast_level / strong_evidence | C 模式导演。               |
| Segment  | speaker_role / actor_id / emotion / intensity / rate                  | 句内角色与朗读参数。       |
| Segment  | audio_path / generation_fingerprint / qa_status                       | 静态音频寻址与 QA。        |

## **V4 音频 Manifest 的核心证据**

现代 V4 manifest 通常以“句子”为播放单元，包含 Opus/MP3 路径、时长、文件哈希、actor sequence、generation fingerprint、technical QA、word-level timing 与 alignment status。2026 Text 3 样本中可见 \`alignment_status = passed\`，对齐模型为 \`torchaudio-WAV2VEC2_ASR_BASE_960H-ctc-v1\`。

需要注意：文章 JSON 的 \`audio_status\`/segment \`qa_status\` 可能仍保留 \`not_rendered\` / \`pending-render\`，而对应 V4 manifest 已经实际存在。这说明当前存在“内容元数据 vs 已产出的音频资产”状态漂移，必须纳入后续治理。

**FRONTEND**

# **5. Reader 前端架构与现有功能**

Reader 是原生静态 Web App，核心页面位于 \`kaoyan-reader-v1/\`。目前不只是最初的 2002 Text 1 页面；生产分支上已经发展出多年份目录、双语难词、Web Audio、持久缓存、连续播放、词群本和跨设备同步。

## **主要模块**

| **模块 / 文件**                                   | **职责**                                                                      |
|---------------------------------------------------|-------------------------------------------------------------------------------|
| index.html                                        | 移动端页面骨架、年份/题型/文章选择、连续播放、难词 6+、词群本入口与同步按钮。 |
| app.js                                            | 主应用装配、内容加载、渲染、交互协调。                                        |
| catalog.js                                        | catalog 筛选、文章切换、manifest 路由。                                       |
| article-bundle-store.js                           | 文章 bundle 的 RAM 常驻与切换。                                               |
| bilingual-text.js                                 | 中英双语与高亮相关渲染。                                                      |
| web-audio-player.js / hybrid-audio-player.js      | 优先 Web Audio，失败回退 HTMLAudio。                                          |
| audio-cache.js / decoded-audio-store.js           | 压缩音频持久缓存 + 解码后 RAM working set。                                   |
| full-library-cache.js / full-library-bootstrap.js | 全 catalog 后台持久化。                                                       |
| continuous-playback.js                            | 播完自动进入下一篇，跨年份继续。                                              |
| inline-phrase-highlights.js                       | 划词、语义/双语对应、词群学习释义。                                           |
| phrase-book-cloud-sync.js                         | 词群本跨设备同步。                                                            |
| phrase-audio-window.js                            | 词群音频片段窗口/即时播放相关。                                               |
| progress.js / ui-history.js                       | 本地进度与 UI 历史状态。                                                      |

## **关键交互**

- 年份 / 题型 / 文章三级选择。

- 英文 + 中文逐句阅读；6 级以上词汇双语突出。

- 逐句播放、文章前后切换、当前句跟读/高亮。

- “连续播放”默认关闭，本机记忆；自然播完后进入 catalog 下一篇，最后一篇停止，不循环。

- 向下滚动时播放器可隐藏，向上恢复；移动端阅读优先。

- 词群本：选择英文词群、联动中文、按本篇/全年查看、模糊译文复习、跨设备同步。

## **UI 视觉与设备约束**

整体保持米白/浅色阅读背景，英文偏 serif、中文灰色 sans-serif，卡片圆角、手机优先。不要为了桌面端“更像管理后台”而牺牲移动阅读密度。原设计要求播放器向下滚动隐藏、向上恢复。

**PERFORMANCE**

# **6. 性能、缓存与播放基线**

## **用户侧目标**

| **场景**                    | **目标 / 标准**                                                         |
|-----------------------------|-------------------------------------------------------------------------|
| 已驻留文章切换              | 现代手机用户感知通常 ≤ 50 ms（不含异常主线程阻塞）。                    |
| 已解码 Web Audio 句子启动   | 请求到 AudioBufferSourceNode.start() 目标 ≤ 50 ms。                     |
| 持久缓存 HTMLAudio fallback | 正常条件下 click-to-audible 目标 100–150 ms。                           |
| 自动句间衔接                | 下一句已解码时，不增加人为 transport gap；transport 贡献目标 \< 50 ms。 |

## **必须保留的缓存架构**

- 文章 JSON、manifest、双语映射在页面生命周期内通过 ArticleBundleStore 驻留 RAM。

- 先渲染当前文章，再限并发预载其余文章；切回已驻留文章不应重新请求网络。

- 静态资源同时进入 CacheStorage，支持刷新后本地恢复。

- Web Audio 是“准备好后立即播放”的优先路径；HTMLAudio/Blob 是必要 fallback。

- 进入文章后先准备当前句 + 后 3 句，再处理其余；前后文章在后台准备。

- 全库缓存默认自动遍历 catalog，压缩音频并发下载默认 4，不把整库全部 decode 到 RAM。

- 缓存版本身份包含媒体路径 + manifest fingerprint/hash；版本变化不能错误复用旧缓存。

- 应用自身不做 LRU/年龄自动清理；允许浏览器/OS 自己回收。

## **历史测试记录（不是今天重新执行的 CI 证明）**

2026-09-28 的仓库文档记录：\`npm test\` 曾有 117 项通过，\`npm ci && npm run test:dom\` 曾有 7 项真实应用 DOM 测试通过，覆盖连续播放跨篇/跨年、最终停止、开关记忆、暂停/恢复和加载中的取消。接手者必须在新基线上重新运行，而不是把这条历史记录当成当前必然全绿。

**EXTERNAL DEPENDENCY**

# **7. 词群本与 Supabase 同步**

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><p><strong>迁移时不能只带 GitHub</strong></p>
<p>当前词群本跨设备同步已经连接 Supabase。若旧账号失去 Supabase 组织/项目访问，新账号虽然能打开网页，但无法完整维护同步功能。需要把 Supabase 权限一并交接。</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

## **当前 Supabase 快照**

| **项目项**    | **当前值 / 状态**                                                      |
|---------------|------------------------------------------------------------------------|
| Project       | kaoyan-phrase-book-sync                                                |
| Project ref   | beeayqhmdgehnkqzxlwm                                                   |
| Region        | ap-northeast-1                                                         |
| 状态          | ACTIVE_HEALTHY（2026-10-03 核对）                                      |
| Edge Function | phrase-book-sync · ACTIVE · version 1 · verify_jwt=false               |
| Endpoint      | https://beeayqhmdgehnkqzxlwm.supabase.co/functions/v1/phrase-book-sync |
| 表            | public.phrase_book_sync_entries · RLS enabled                          |

## **同步安全模型**

前端不做账号登录。浏览器首次使用会随机生成 24 字节同步密钥并保存为 base64url 字符串；用户可以“复制同步码 / 输入同步码”在另一台设备合并词群本。Edge Function 将同步码做 SHA-256 后作为 \`sync_hash\` 存储，数据库不会保存明文同步码。

Edge Function 使用服务端 \`SUPABASE_SERVICE_ROLE_KEY\` 访问表；这个 key 绝对不能写进前端、GitHub public 文件、Word 或聊天。函数 \`verify_jwt=false\`，安全性主要依赖高熵同步码、自定义输入校验、数据量限制与 CORS。同步码本身等价于 bearer secret，泄露后别人可以读取/合并对应词群本。

| **数据字段**            | **作用**                                                     |
|-------------------------|--------------------------------------------------------------|
| sync_hash               | 同步码 SHA-256；复合主键的一部分。                           |
| signature               | articleId + sentenceId + 英文起止 offset；复合主键另一部分。 |
| payload                 | 词群条目 JSONB。                                             |
| updated_at / deleted_at | 基于时间版本合并与 tombstone 删除。                          |

Supabase Security Advisor 当前给出 INFO：该表启用了 RLS 但没有 policy。按现有设计，直接 Data API 访问应被阻断、由 Edge Function 的 service role 访问；不要为了消掉提示而随意开放 anon/authenticated policy。

## **一个需要新账号明确决定的架构漂移**

\`inline-phrase-highlights.js\` 中存在可选的浏览器 \`Translator\` API fallback，用于用户临时选择词群时生成学习释义；它不是 canonical 文章译文，也不是正文生产流程。但这与项目最初“运行时不生成 AI 内容”的纯静态原则存在边界变化。建议新账号先确认：保留为可选客户端增强，还是彻底关闭，避免产品原则含糊。

**REPOSITORY**

# **8. GitHub、分支与生产发布**

## **仓库与线上地址**

**仓库：**[<u>github.com/dannhitruong852-jpg/taptap</u>](https://github.com/dannhitruong852-jpg/taptap)

**Reader：**[<u>dannhitruong852-jpg.github.io/taptap/kaoyan-reader-v1/</u>](https://dannhitruong852-jpg.github.io/taptap/kaoyan-reader-v1/)

## **为什么现在必须先治理分支**

历史上 \`kaoyan-reader-v1\` 是开发分支、\`gh-pages\` 是发布分支。但实际迭代过程中，大量后续源码、production_v2、测试、workflow、2013–2026 内容/音频都直接进入了 \`gh-pages\`。截至本次核对，\`gh-pages\` 比旧开发分支多 282 个提交，而旧开发分支并没有这些资产。

同时 \`gh-pages\` 还是整个仓库的共享 Pages 分支，2026-10-02 的最新 HEAD 是另一个“ZJU MAP A4 打印文件”发布。因此“把 reader 仓库目录部署到 gh-pages”的动作必须是增量、受保护的 overlay；不能把整个分支当成单一项目目录重写。

## **Canonical Production V2**

仓库已经建立唯一 canonical 生产流程。它的状态机必须严格按顺序前进：

> draft → validated → frozen → rendering → merged → release_candidate → published

| **阶段**      | **要求**                                                                      |
|---------------|-------------------------------------------------------------------------------|
| manifest      | 先创建并提交 batch manifest；明确 years、expected_articles、source ref。      |
| canary        | 第一个年份先做 canary，验证共享生产逻辑。                                     |
| validate      | 所有内容/结构 contract 全批次验证通过。                                       |
| freeze        | 冻结内容 snapshot、SHA-256 fingerprints、freeze_id 和 render matrix。         |
| render        | 只消费 frozen artifact，并行单位为 (year, article, shard)。                   |
| merge         | 每年独立合并，不能有 missing segments。                                       |
| release guard | 从当前 gh-pages 生产基线起步，只 overlay 新资产；检查旧年份/文章/路由不消失。 |
| publish       | 同一 workflow publish=true，再次 gate，普通 fast-forward push。               |
| verify Pages  | 必须验证 Pages workflow 的 head_sha 正好等于新生产 SHA。                      |

## **Release Guard 的不可破坏不变量**

- \`existingYears ⊆ candidateYears\`。

- \`existingArticleIds ⊆ candidateArticleIds\`。

- 已有文章的 \`year\` / \`content\` / \`manifest\` 路由不能静默改变。

- 没有单独审核过的 deletion migration，就不能删历史内容。

- 发布候选必须从“当前生产”构建，不从任意工作分支复制整个树。

## **绝对禁止的生产操作**

| **禁止项**                                  | **原因**                                                              |
|---------------------------------------------|-----------------------------------------------------------------------|
| 把旧开发分支 force-push 到 gh-pages         | 会直接丢 2013–2026 与后续功能/工作流/共享 Pages 资产。                |
| 为每个年份临时发明新的 \*-once.yml 当主流程 | 无法累积治理，容易重复历史事故。                                      |
| 内容未 freeze 就大规模 TTS fan-out          | 渲染过程中内容变化会导致资产不可追溯。                                |
| 用 batch-only catalog 替换生产 catalog      | 会让旧年份从线上“消失”。                                              |
| 忽略失败 gate 手动继续                      | 破坏生产状态机和回滚证据。                                            |
| 仅看 render 成功就宣布完成                  | 还需要合并、Reader tests、release guard、Pages deployment 与艺术 QA。 |

**CODE MAP**

# **9. 目录结构与关键文件**

| **路径**                         | **用途 / 交接说明**                                           |
|----------------------------------|---------------------------------------------------------------|
| kaoyan-reader-v1/                | 生产 Reader：UI、catalog、内容、音频、缓存、词群本、测试。    |
| content-pipeline/                | PDF/curated 内容、双语高亮、词汇与方向等内容生产。            |
| voice-pipeline/                  | C 模式 schema、actor、Chatterbox 生成、对齐/QA。              |
| production_v2/                   | 当前 canonical 批量生产 CLI 与 release guard。                |
| .github/workflows/               | CI、Production V2、历史一次性修复/审计工作流。                |
| batch-manifests/                 | 生产批次 manifest；应作为可审计输入。                         |
| docs/production/                 | 当前生产 SOP。                                                |
| docs/standards/                  | 双语高亮与播放延迟等正式标准。                                |
| docs/superpowers/specs/ / plans/ | 设计与实施历史；用于理解演进，但现状以生产规则为准。          |
| reports/                         | 生产/发布/QA 报告与回滚证据。                                 |
| downloads/                       | 共享 Pages 上的其他下载资产；说明 gh-pages 并非 Reader 专属。 |

**ACCOUNT MIGRATION**

# **10. 新账号迁移：必须带走什么**

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><p><strong>原则</strong></p>
<p>不要试图“迁移聊天记忆”。把项目事实固化到仓库、这份 Word、源 PDF 与外部服务权限中。新账号只需要重新建立连接并按接手清单校准，就不依赖旧账号继续存在。</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

## **A. 文件与上下文包**

| **必须带走** | **具体内容**                                 | **备注**                          |
|--------------|----------------------------------------------|-----------------------------------|
| 本交接文档   | 本 DOCX                                      | 新账号第一份资料。                |
| 源真题 PDF   | 2000–2009 英语真题；2010–2026 英语二真题     | 建议在新 ChatGPT 项目中重新上传。 |
| 词汇宝典     | 大纲词汇背诵宝典（英语二）.pdf               | 词汇等级参考，不是考试年份。      |
| GitHub 仓库  | 完整 repo + 所有分支/工作流/大文件           | 这是实际工程真相。                |
| 生产规范     | PRODUCTION_RULES + Production V2 + standards | 也已经在 repo 内。                |

## **B. 外部账号 / 权限**

| **系统**               | **要迁移 / 重新连接**                                                    | **不要做**                                   |
|------------------------|--------------------------------------------------------------------------|----------------------------------------------|
| GitHub                 | 新 ChatGPT/新开发者连接同一 repo，确认 Actions/Contents/Pages 所需权限。 | 不要把个人 access token 明文写进 Word/聊天。 |
| Supabase               | 给新账号/开发者加入 kaoyan-phrase-book-sync 所在组织/项目的适当权限。    | 不要暴露 service_role key。                  |
| GitHub Actions secrets | 在 Repository Settings 中核对生产所需 secrets / variables 是否仍可用。   | 不要把 secret 值复制到公开仓库。             |
| ChatGPT Project        | 重新上传源 PDF 与本 DOCX；连接 GitHub/Supabase。                         | 不要假设旧账号的 Project 文件会自动跟随。    |

## **C. 新账号第一天的安全顺序**

1\. 只读核对 repo：确认 \`gh-pages\`、旧 \`kaoyan-reader-v1\`、当前 catalog、Production V2 文件仍与本快照一致。

2\. 立刻给当前生产创建一个可回滚快照（tag 或保护分支）；先保存，再治理。

3\. 不要从旧开发分支起新开发。先决定如何把 \`gh-pages\` 中的后续源代码抽回一个新的正式开发分支，同时保留共享 Pages 资产。

4\. 重新运行 Reader Node tests、DOM tests、production_v2 tests；记录当前真实 baseline。

5\. 核对 Supabase 项目、Edge Function、表与同步实际可用；只读检查，不先改 schema。

6\. 在手机真机打开线上 Reader：随机抽查多个年份、音频、连续播放、难词联动、词群本与同步。

7\. 只有 baseline 可复现后，才继续新增年份/修内容/改播放器。

**RISK REGISTER**

# **11. 现存风险与技术债**

| **优先级** | **风险**                                              | **影响**                                                            | **建议**                                                                 |
|------------|-------------------------------------------------------|---------------------------------------------------------------------|--------------------------------------------------------------------------|
| P0         | 开发分支严重落后生产分支                              | 错误覆盖可能丢 2013–2026、词群本、Production V2 与共享 Pages 资产。 | 先快照 gh-pages；建立新的 source-of-truth 开发分支；再做分支治理。       |
| P0         | gh-pages 同时承载部署与后续源码                       | 职责混合，发布操作容易变成“生产数据库式操作”。                      | 保持 additive guard；逐步把源码开发移回受控分支，gh-pages 只做发布产物。 |
| P1         | 内容 JSON 的 audio_status 与实际 manifest 可能漂移    | 运营/QA 面板可能误判是否生成。                                      | 建立 manifest→content status 的一致性审计和自动 gate。                   |
| P1         | V4 technical QA 通过但 voice / c_direction 仍 pending | “可播放”不等于声音最终合格。                                        | 按批次建立主观/半自动艺术 QA 状态，禁止模糊口径。                        |
| P1         | 2000–2001 有 PDF 但 live catalog 从 2002 开始         | 内容覆盖与源库存不一致。                                            | 明确产品是否要补齐；若补齐，走 canonical V2。                            |
| P1         | 词群浏览器 Translator fallback 与纯静态原则边界不清   | 产品承诺可能不一致。                                                | 明确“仅可选词群学习增强”或关闭；正文翻译仍必须离线固定。                 |
| P2         | 历史 \*-once.yml 较多                                 | 新开发者可能误把恢复脚本当主生产链。                                | README/目录标记 legacy；正常生产只用 V2。                                |
| P2         | 第三方音频/参考音许可与长期可用性                     | 长期维护与公开发布风险。                                            | 保留 THIRD_PARTY_AUDIO_NOTICE，生产演员素材保持可追溯许可。              |

## **已知的历史教训**

- 2002 Text 1 初代技术样片虽然 TTS 生成成功，但用户明确反馈“旁白太平”；因此声音 QA 必须以理解/自然度为标准，而不是“有文件”。

- 曾发生 2003–2006 内容消失/恢复类历史问题；Production V2 的 additive release guard 就是为防止同类回归。

- 浏览器系统 TTS 在目标 Android 设备上不可靠；不要重新走回头路。

- GitHub Actions 适合重型 Chatterbox 生成；本地/容器环境历史上受依赖与网络限制。

**QUALITY**

# **12. 测试、验收与“完成”的定义**

## **内容 QA**

- 原文忠实：source hash / pages 可追溯；正文没有题目/选项/写作污染。

- 完型必须由可信 answer key 还原；缺答案时标记阻塞，不得猜。

- 中译完成第二轮校对。

- level ≥ 6 中英高亮覆盖完整且 offset 无陈旧。

## **音频 QA**

- 无 missing segment，文件哈希与 generation fingerprint 完整。

- technical QA：剪切、首尾空白、速度、对齐、字序等满足 gate。

- C-mode QA：旁白不平、信息焦点合理、换声有说话者依据、强表演有文本证据。

- 新/重生资产 leading silence 通常 ≤ 150 ms；不以压缩语义停顿来换速度。

## **Reader QA**

- catalog 多年份选择、文章切换、返回已访问文章不触发错误网络依赖。

- Web Audio 启动、fallback、pause/resume、速度变化、自动续播、stale async suppression。

- 全库缓存可中断后恢复；不阻塞首屏和显式播放。

- 连续播放跨篇/跨年正确，手动操作能取消 pending handoff。

- 词群本保存、删除、双语 span、音频片段、同步码跨设备合并。

- 最终一定要有 Android 真机 smoke test；jsdom 不能替代真实音频与后台行为。

## **Definition of Done**

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><p><strong>一个生产批次只有在以下全部成立时才算 Done</strong></p>
<p>所有 mandatory gates 通过；batch state = published；frozen manifest 要求的 render units 完成；merged manifest 无 missing segments；Reader tests / content / bilingual / actor / source-scope / C-mode contract 通过；release guard 对当前生产通过；GitHub Pages 部署成功且 head_sha 对应新 production SHA；旧内容全部保留；release report 记录 previous_production_sha 供回滚。</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

**NEXT**

# **13. 下一阶段建议路线图**

## **P0：先把工程治理正确**

1\. 冻结 2026-10-03 production snapshot；给 gh-pages 当前 Reader 状态建立可回滚标记。

2\. 从当前生产事实重建“正式开发分支”，把后续源码从部署分支抽离；绝不丢共享 Pages 资产。

3\. 跑一遍完整测试矩阵，写出 baseline report；修复任何 deterministic regression 并补永久测试。

## **P1：把状态与 QA 口径补齐**

1\. 增加 content JSON ↔ manifest 的状态一致性审计，消除 \`not_rendered\` / 已存在音频这种漂移。

2\. 建立 voice / c_direction 的明确验收状态，不再让 “pending” 长期与 production 混在一起。

3\. 确认 2000–2001 是否纳入产品；若纳入，按 V2 走完整 content → freeze → render → release。

4\. 明确 Translator API 的产品边界并写回仓库规范。

## **P2：体验优化**

1\. 基于真实 Android 设备测量 article-switch / audio-start，而不是只依赖 CI。

2\. 继续优化词群选择、即时音频片段与复习交互，但不能破坏阅读主流程。

3\. 逐步清理历史 once/recovery workflow 的可见性，避免新维护者误触。

**OPERATING MODEL**

# **14. 变更管理：以后任何新功能都按这个方式做**

项目已经从“单篇样片”演变成带生产资产、缓存、Edge Function 和长期数据的真实系统。以后不能再把每次修改当作“改一个网页文件”。每个变更至少回答五个问题：

1\. 它改变了哪条产品原则或用户路径？

2\. 它会改变 catalog / content / audio / cache identity / Supabase 数据吗？

3\. 失败时如何回滚到明确 SHA / freeze / previous production？

4\. 哪一条测试或 gate 能永久防止同类回归？

5\. 它是否会删除、覆盖或使旧年份资产不可访问？

## **决策记录规则**

- 新重大决策写进 repo 文档，不依赖 ChatGPT 记忆。

- 决定与实现分开：spec/standard 写“为什么和规则”，plan/commit 写“怎么做”。

- 确定性 bug 必须落成回归测试/不变量；“下次记得”不算修复。

- 对生产状态的描述必须来自仓库/manifest/report；不能凭聊天印象说“后台已经跑完”。

**APPENDIX**

# **附录 A · 2026-10-03 事实快照**

| **事实项**          | **值**                                                                                             |
|---------------------|----------------------------------------------------------------------------------------------------|
| 仓库                | dannhitruong852-jpg/taptap（public）                                                               |
| 旧开发 HEAD         | 7dbbd4da1296a19d62aface099d55b82ba04b3f0 · fix: restore 2003-2006 alongside 2007-2012              |
| 当前 gh-pages HEAD  | 1992d6bec9deafb325a586f18475d2597ee22dfc · Publish ZJU MAP 2026 A4 printable outline               |
| Reader 最近可见提交 | d848786e1bc1370a91256da6933bf04d8e4b8bc3 · 2026-09-29 · refresh prepared phrase clip cache version |
| 分支关系            | gh-pages ahead 282 / behind 0 relative to old dev branch                                           |
| Live catalog        | 2002–2026 · 25 years · 172 articles                                                                |
| Supabase            | kaoyan-phrase-book-sync · ref beeayqhmdgehnkqzxlwm · ACTIVE_HEALTHY                                |
| Edge Function       | phrase-book-sync · v1 · ACTIVE · verify_jwt=false                                                  |

这些 SHA/状态是交接日快照。新账号接手时必须重新核对，因为仓库可能继续变化。

**BOOTSTRAP PROMPT**

# **附录 B · 新账号首次对话建议指令**

下面这段可以直接复制给新账号。它的目的不是让新账号“相信旧资料”，而是强制它先同步当前 repo，再继续工作。

> 你正在接手“考研英语 C 模式学习网站”。先不要修改代码，也不要重新设计产品。请先只读核对 GitHub 仓库 dannhitruong852-jpg/taptap 的当前状态，重点读取：PRODUCTION_RULES.md、docs/production/C_MODE_PRODUCTION_V2.md、docs/standards/BILINGUAL_HIGHLIGHT_STANDARD.md、docs/standards/PLAYBACK_LATENCY_STANDARD.md、kaoyan-reader-v1/content/catalog.json。  
>   
> 当前交接快照提示：旧 kaoyan-reader-v1 分支曾落后 gh-pages 很多，gh-pages 可能同时承载生产资产与后续源码，所以绝对不要用旧分支覆盖 gh-pages。请重新报告：当前关键 branch SHA、gh-pages 与开发分支的 ahead/behind、catalog 年份/文章数、Reader/Production V2 测试状态、Supabase 词群本同步状态、主要未解决风险。  
>   
> 产品冻结原则：只处理完型恢复正文、阅读 A Text1–4、Part B 正文、Translation 英文原文；Writing 全部忽略。翻译信达雅；词汇 1–9 级且 6+ 双语突出；C 模式为理解导向的自然交流式朗读；Chatterbox 预生成静态音频；不要回到浏览器 speechSynthesis。正常生产只走 Production V2 状态机和 additive release guard。  
>   
> 核对完事实后，再基于当前仓库继续；任何确定性 bug 都必须补回归测试。

**SECURITY CHECKLIST**

# **附录 C · 凭证与权限交接清单（不填写 secret 值）**

| **项目**                             | **状态/负责人** | **新账号动作**                                        |
|--------------------------------------|-----------------|-------------------------------------------------------|
| GitHub repo collaborator / owner     | □ 已确认        | 确保能读取/写入需要的分支与 Actions。                 |
| GitHub Pages                         | □ 已确认        | 确认部署来源、共享 gh-pages 资产与 rollback 方式。    |
| GitHub Actions secrets / variables   | □ 已核对        | 只确认名称/存在性/有效性；secret 值不写入文档。       |
| Supabase organization/project access | □ 已移交        | 新账号可见 kaoyan-phrase-book-sync。                  |
| Supabase Edge Function env           | □ 已核对        | SUPABASE_URL / SUPABASE_SERVICE_ROLE_KEY 留在服务端。 |
| 源 PDF 文件                          | □ 已复制        | 新 ChatGPT Project 重新上传。                         |
| 本交接 DOCX                          | □ 已保存        | 作为账户迁移最小上下文包。                            |

**RUNBOOK**

# **附录 D · 常用命令 / 操作入口**

以下命令来自当前 Production V2 SOP；执行前仍应先查看仓库最新文档与 CLI \`--help\`。

> python -m production_v2.cli_manifest create --batch-id \<ID\> --years \<Y1,Y2,...\> --source-ref \<SOURCE_GIT_SHA\> --output batch-manifests/\<ID\>.json
>
> python -m production_v2.cli_manifest validate --manifest batch-manifests/\<ID\>.json
>
> python -m unittest discover -s production_v2/tests -v
>
> cd kaoyan-reader-v1 && npm test
>
> cd kaoyan-reader-v1 && npm ci && npm run test:dom

GitHub Actions 正式批处理入口：\`C Mode Production Pipeline V2\`。推荐先 \`publish=false\` 生成 release candidate；只有所有 gate 通过，再用相同 manifest \`publish=true\`。

**HANDOFF CLOSE**

# **附录 E · 最终交接口径**

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><p><strong>接手者可以从哪里继续</strong></p>
<p>最优先不是“继续堆内容”，而是先保护当前 production、把开发/部署职责重新分开、重跑 baseline tests、校准 Supabase 与艺术 QA 状态。完成这四件事之后，这个项目就能在新账号下稳定继续迭代，而不再依赖旧聊天窗口。若以后仓库状态与本文冲突，以当前仓库和正式生产规范为准，并更新新的交接快照。</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>