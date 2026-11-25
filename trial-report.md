# 试用报告：开源 skill 包 × 原创烧脑恐怖短剧《十四楼不存在》

## 0. 本批交付与流程
用户要求「找一本烧脑恐怖网文出 100 集」与商用授权红线冲突（无授权现成网文不能改编），故按升级后的工作流执行：**先用 oh-story 写原创小说，再用 shuohao + drama-skills 改编**。顺序为：小说设定/大纲/章纲 → 小说正文前 6 章 → 改编 bible → 100 集分集地图（outline.json 过 13 道门）→ 前 5 集剧本（过 10 道门）。剧本情节全部回溯到小说章纲：EP(2k−1)、EP(2k) 对应小说第 k 章上下半。

## 1. oh-story（story-long-write / story-deslop / story-review）实际用上的方法
- **开书流程（workflow-setup）**：按「定方向→定设定」落盘 `设定/题材定位.md`（含读者契约、终局底牌与升级台阶、透支红线）、`设定/关系.md`、4 张角色卡+配角组简卡、`设定/世界观/雾时与守则.md`（含刻度表与守则真实范围底牌）、`设定/文风.md`。
- **卷纲（workflow-volume）**：全书 10 卷 × 5 章；第一卷卷纲含剧情单元卡（U101–U103）、情绪弧线与章节定位、卷尺度关键点（开场/1/4/中点/3/4/卷末）、契约风险标注。无对标书，按八节点自排（文件里已注明）。
- **细纲（workflow-outline）**：前 6 章按权威模板写全（九个必填字段、四小节、情节点表含「禁＋放」执行边界、复沓锚句锁定守则原文逐字）。
- **正文（workflow-chapter + long-format + opening-design + long-suspense）**：冲突开局、三基点（切入点第 1 章、人设前 3 章、金手指＝职业调查法第 1 章起作用）；正文格式执行段间单换行、半角双引号、无省略号/破折号停顿、无工程词。字数收口按其规则执行：2–6 章初稿低于 2000±15% 带时，只展开已批准情节点的质感（痕迹、声口、在场反应），未新增事件，最终 6 章 1733–2016 字，合计 11064 字。
- **去AI味（story-deslop）**：三层扫描后跑确定性检测（见 §4），表达层问题按检测器提示定点改写。
- **审查（story-review）**：以 solo 模式自审（未 spawn 独立 reviewer agent，如实标注）：契约四问（代理权/利益安全/期待债/终局储备）逐项核过；发现并保留 2 条 advisory——第 3 章顾长生有一句直接给信息的台词，与其「只用问题回答问题」的卡面略有出入，判定为考题环节的合理例外；第 6 章末行是主角记录本上的推论句，属人物习惯而非叙述者点题，保留。
- **未启用**：oh-story 的追踪事务系统（tracking_commit / storyctl chapter check）未部署——本项目交付形态是文档包而非其标准工程状态机；后续若进入日更连载，建议先跑 story-setup 初始化追踪再续写。

## 2. shuohao-skills 实际用上的方法
- **novel-outline**：五件套结构（改编说明/人物表三档分级/场景表/爽点表/分集梗概）落成 `outline.json`（100 集、20 角色、16 场景、60 个爽点），`render --md` 产出 `outline-rendered.md`。
- **novel-script**：EP001–005 以 script.json 结构化写作（场次+动作拍/对白拍交替、台词带说话人与语气、hookBeat 认领冷开场、beatsClaimed 认领爽点），`params.preserveOriginal=false`（原创改戏模式，时长与字数门全开），`render --md` 后按集拆为 `scripts/EP001–005.md`。时长按 4.5 字/秒 + 2.5 秒/拍折算，5 集分别为约 57.5 / 53.2 / 56.5 / 56.5 / 60.4 秒。

## 3. drama-skills 实际用上的方法
- **short-drama-develop**：创作者契约锁定（不可改事实/可探索空白/形式约束先行区分）；故事引擎写法（压力如何出现、对手如何反制、每轮改变什么）用于 bible 的机制章；分集设计采用其单集契约思想——每集有进入状态、当集兑现与出去的压力，episode-map 的钩子列即出去压力的落点。
- **short-drama-write**：以其「全集第一拍独立成钩、结尾按功能收束、自检时撤去输入未要求的机关」为 EP001–005 的人工复核标准，与 shuohao 的机器门互补。
- **未使用**：其剧本 Markdown 方言与 duration_estimate.py（与 shuohao 结构化格式不兼容，本批以 shuohao 折算为准）；其自检脚本仅作仓库健康验证（见 §4）。

## 4. 检查脚本实际运行结果（本机实测，未运行的不写）
| 检查 | 命令 | 结果 |
|---|---|---|
| shuohao novel-outline 自测 | `node scripts/selftest.mjs` | ✓ 220 项断言全过（SKILL.md 标注 219，以实跑为准） |
| shuohao novel-script 自测 | `node scripts/selftest.mjs` | ✓ 154 项断言全过（SKILL.md 标注 151，以实跑为准） |
| drama develop 自测 | `python3 scripts/selftest.py` | ✓ 5 项通过 |
| drama write 自测 | `python3 scripts/selftest.py` | ✓ 8 项通过 |
| oh-story 细纲契约检查 | `check-outline-contract.js --chapter 1..6` | ✓ 6 章全部 ok=true，每章 9 项检查 0 失败 |
| oh-story AI 句式检测 | `check-ai-patterns.js --fail-on=blocking`（正文 6 章） | 首跑 exit 1：8 处 blocking（7 处「不是A，是B」对比腔 + 1 处反序对比），另 advisory：比喻密度 4 章、工整并列 1 处、超长段 1 处（228 字）。逐条改写后复扫 exit 0，blocking 清零；余 2 条比喻密度 advisory（第 1 章 5.8/千字、第 3 章 6.9/千字），所涉意象（被剪走的二十六分钟、同时按停的秒表）承担叙事功能，按 deslop 方法保留并在此登记 |
| oh-story 退化检测 | `check-degeneration.js --fail-on=blocking` | ✓ 两轮均 exit 0（无复读、截断、工程词泄漏） |
| oh-story 标点整理 | `normalize-punctuation.js` | ✓ 运行完成，0 个文件被改动（正文已合规） |
| shuohao 大纲校验 | `novel-outline.mjs validate outline.json`（full） | 首跑 1 处违规：EP007/043/052/073 生成难点预警漏报（人群/肢体接触），按点名补报后复跑 ✓ 13 道质量门全过 |
| shuohao 剧本校验 | `novel-script.mjs validate script.json --outline outline.json` | ✓ 一次通过：5 集 / 13 场 / 45 句台词，预估 284.1s / 目标 300s。注意：未给 `--art`，场景/光照/道具对账被跳过——art.json 尚未制作，道具编号（P01 值班簿、P02 怀表）为剧本内登记，待 novel-art 阶段对账 |

质检门真正拦下过的东西：细纲契约检查保证了 6 份细纲字段与五段式结构完整（首轮即过，无返工）；AI 句式检测拦下 8 处对比腔（这是本批最有价值的拦截，人工通读时确实容易滑过）；大纲校验拦下 4 集生成难点漏报。未拦下的风险：比喻密度只到 advisory 级，靠人工判断保留，口味取舍已在上表登记。

## 5. 原创故事来源说明
《十四楼不存在》为本项目原创：人物、安澜公寓、雾时/雾季、七条守则、借名/无名者/落定条件、1994 年事件与全部情节均在本项目 `novel/设定/` 与 `novel/大纲/` 中从零建立，未改编、未仿写任何已出版网文或小说，不含他人作品的人物、设定与桥段。题材手法（规则怪谈、清点、守则）为类型公约，不构成对具体作品的复制。

## 6. 已知短板
- 小说正文仅完成前 6 章（约 1.1 万字），第 7–50 章目前是章纲级（每章含上下集切分点与章尾钩子），写正文前须按 oh-story 模板逐单元补细纲。
- outline.json 的爽点 setup/payoff 文本由梗概自动截取，措辞较糙，只作节奏骨架，不宜直接当文案用。
- 功能性角色（C11–C20）目前只在个别集露脸，戏份薄；若后续要强化群像，需在对应章细纲里给戏。
- art.json（场景光照状态/道具对账）与 cast.json（角色音色）未做，剧本对账链缺一环；分镜层（novel-storyboard / drama 的 storyboard 技能）未启动。
- 本剧的恐怖强度走认知路线，无强刺激场面；若平台要求更强的视觉钩子，需在分镜层用光影与声音设计补强，而不是改剧本加血浆（合规红线）。

## 7. 后续批次（EP006–100）生产方式建议
1. 小说侧：按卷推进，每卷先补 5 章细纲（过 check-outline-contract），再写正文 5 章（过双检测器），保持「先小说后改编」铁律；建议每卷末做一次伏笔表对账（埋/收点位与 bible 登记表逐项核销）。
2. 剧本侧：沿用 script.json 增量扩集，每批 ≤ 3 集（novel-script 的默认批次），每批跑 validate --outline；EP006 起先补 art.json（至少登记 S01–S16 的光照状态与 P01–P05 道具），把 --art 对账打开。
3. 每 10 集（一篇）收口时跑一次 outline 全量 validate + 一次人工软审（可使用 novel-script 的顾问软审流程），篇末卡点集单独复核钩子强度。
4. 全部 100 集剧本完成后，再进分镜层（shuohao novel-storyboard 或 drama-skills 的 storyboard 技能），不要在剧本未冻结前并行出分镜，避免返工。
