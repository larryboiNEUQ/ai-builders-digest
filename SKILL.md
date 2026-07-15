---
name: ai-builders-digest
description: >
  从 zarazhangrui/follow-builders 中央 feed 生成「AI Builders Digest」飞书日报：
  叙事化头条、X 评论区补充、配图、迁入我的文档库。
  Use when: 用户说「AI builders 日报」「follow-builders 日报」「生成 builders digest」、
  「今天的 feed 日报」、/ai-builders-digest，或要模仿 2026-07-14 风格补历史日期日报。
---

# AI Builders Digest（固化模板）

把 follow-builders 的原始 feed 编成**可读的编辑部日报**，不是逐人流水账，也不是法务免责声明合集。

**金样例（风格与结构的唯一真相源）**

- 标题格式：`YYYY-MM-DD AI Builders Digest`
- 结构与文风参考：[`references/gold-sample-2026-07-14.md`](references/gold-sample-2026-07-14.md)
- 反例速记：[`references/anti-patterns-0707-0708.md`](references/anti-patterns-0707-0708.md)

**反例（不要学）**

- 「结构标题齐了，但正文是每人一条 + 原文/说明 + 编辑判断（非事实）」的目录体——读者像在翻通讯录，而不是读「今天行业在吵什么」。

---

## 0. 输出物约定（必须对齐）

| 项 | 规则 |
|----|------|
| 文档标题 | `YYYY-MM-DD AI Builders Digest`（日期在前，英文 Digest，无「日报·」） |
| 正文语言 | 简体中文为主；专有名词/模型名/handle 保留英文 |
| 落库位置 | 飞书 **我的文档库**（Wiki `my_library`），不是随便丢云空间根目录 |
| 日期语义 | 以 feed 的 `generatedAt` 本地日期为准，或用户显式指定的 `--date`；不是每条 tweet 的 publishedAt |

---

## 1. 工作流（按顺序执行）

### Step A — 拉 feed

```text
https://raw.githubusercontent.com/zarazhangrui/follow-builders/main/feed-x.json
https://raw.githubusercontent.com/zarazhangrui/follow-builders/main/feed-blogs.json
https://raw.githubusercontent.com/zarazhangrui/follow-builders/main/feed-podcasts.json
https://raw.githubusercontent.com/zarazhangrui/follow-builders/main/state-feed.json
```

- X 窗口约 24h；blog 约 72h；podcast 约 14d lookback（以 JSON 字段为准）。
- 记录 `generatedAt` 写进文首来源行。

### Step B — 选题（编辑，不是全量搬运）

1. 按互动（likes/replies/views）+ **跨 builder 重复出现的主题** 聚类。
2. 选出 **2–4 条头条叙事**（行业/产品级），其余进「X Builder 精选」或表格。
3. **砍掉**：纯生活/球赛/与 AI 无关的闲聊；重复转发只留最强一条。
4. 优先保留：**可复用的工作流、产品发布、产业结构判断、限流/额度实感、开源权重/路由**。

### Step C — X 上下文增强（必须做，这是 7-14 与 7-07/08 的关键差距）

对每条头条和每个高价值 builder 帖：

1. 用 X 工具拉 **thread + 高赞回复**（至少扫前几条有信息量的回复）。
2. 若原帖链到文章/latent.space/changelog：**打开原文**，把「别人推荐用什么 / 选型表 / 关键数据」拆进正文（表格或 bullet），不要只写「后续贴了某汇总」。
3. 记录 1–3 条**有观点的评论**（原话可短引），并给一句「社区语境 / 读法」。

### Step D — 配图（**硬性要求，不可跳过**）

> **纯文字日报不合格。** 交稿前若关键帖没有「解释性配图」，视为未完成 Step D。

#### 必须放什么（有内容、能解释，不是装饰）

对**每条头条**和**每个高价值精选**，用 X thread fetch / 原帖 media 检查是否有图，并按下表取舍：

| 优先级 | 类型 | 例子 | 是否必插 |
|--------|------|------|----------|
| P0 | **操作/产品 UI 截图** | `/checkup` 结果、Artifacts 功能图、Work 界面 | 有则**必插** |
| P0 | **信息图 / 选型表 / 架构图** | 模型档位表、三层 adoption、栈对照 | 有则**必插** |
| P0 | **官方宣发图**（带产品信息，非纯 logo） | 发布 KV、功能三要点海报 | 有则**必插** |
| P1 | **演示结果图** | e-reader 固件、训练 progress、dashboard | 头条相关则插 |
| P2 | **有信息量的 meme** | 额度/reset 梗图、发布周情绪 | 全篇最多 1–2 张 |
| 跳过 | 纯头像、无字空白、纯风景、足球进球（非 AI） | — | **不插** |

#### 数量底线

- 正常有图日：**≥ 3 张**解释性配图；头条尽量 **每条至少 1 张**（原帖无图可跳过并在脑中记下，不硬凑）。
- 若当日 feed 几乎无 media：至少对 Top 互动帖做 thread fetch；仍无图则在文首注明「当日关键帖无可用截图」，**不得假装已配图**。

#### 操作步骤

1. thread fetch 头条/高赞帖 → 收集 `pbs.twimg.com/media/...`（优先 `?name=large`）。
2. 下载到本地临时目录；飞书 `docs +media-insert` 要求 **相对路径** → 先 `cd` 到图片目录。
3. **`--selection-with-ellipsis` 插到对应段落旁**（标题下或「读法」前），**禁止**只堆在文末图库。
4. 每张图加 `--caption`：`@handle：一句话说明图在解释什么`。
5. create/overwrite 正文**之后**再插图（定位更稳）；保留已有 image token 时勿盲目 overwrite 冲掉图。

### Step E — 写 markdown → 飞书

1. 本地写完整 markdown（见第 2 节骨架）。
2. `lark-cli docs +create --title "YYYY-MM-DD AI Builders Digest" --markdown @./file.md --as user`
3. **立即执行 Step D 插图**（create 后 media-insert；无图不算交付）。
4. 迁入我的文档库：

```bash
# 解析个人知识库
lark-cli wiki spaces get --params '{"space_id":"my_library"}' --as user
# docs-to-wiki
lark-cli wiki +move --obj-type docx --obj-token <DOC_ID> --target-space-id <SPACE_ID> --as user
```

5. 确认标题仍是 `YYYY-MM-DD AI Builders Digest`；若被改名则 `drive files patch` 改回。
6. 把 wiki 链接回给用户。

### Step F — 依赖 skill

- 飞书文档：`lark-doc` + `lark-shared`（认证）
- 迁库：`lark-wiki` / `lark-drive` 的 move 说明（**我的文档库 = wiki my_library，不是 drive 根目录**）
- X：内置 X search / thread fetch

---

## 2. 文档骨架（顺序固定）

```markdown
> 数据来源：[follow-builders](https://github.com/zarazhangrui/follow-builders) 中央 feed
> （生成时间 UTC `...`）
> 补充：X 原帖、评论区与社区讨论
> 窗口：X ~24h · 博客 72h · 播客 lookback

## 今日一句话
**一句话抓住当天主冲突/主趋势（加粗）。**

## 头条叙事
### 1. …
### 2. …
### 3. …
（每条：发生了什么 → 社区语境 → **读法**；配图夹在中间）

## X Builder 精选
### 某人（@handle）— 一句话主题
（表格/bullet；深挖链接内容；次要 builder 用总表）

## 官方博客
## 播客（Lookback）
## 评论区与情绪板（综合）
| 情绪 | 代表声音 |
## 今日可带走的判断
1. …
## 延伸阅读 / 原帖
- 链接列表
---
*本日报由 follow-builders feed + X 上下文综合整理，非官方 digest 原文。*
```

---

## 3. 文风规则（像 7-14，不像 7-07/08）

### 要做

- **叙事优先**：先讲「今天行业在吵什么」，再落到具体人。
- **读法 / 社区语境**：每条头条结尾用 1–2 句编辑判断（可以鲜明，但别写成免责声明）。
- **可抄作业**：工作流用表格（plan/critique/code/review；Luna/Terra/Sol 选型等）。
- **对照**：Swyx 栈 vs 社区推荐、OpenAI vs Anthropic、frontier vs open weights。
- **情绪板**：用表格归纳 🎉😤🧩⚔️🛠，短。
- **判断 4–6 条**：可带走、可复述，不要复述正文。

### 不要做（7-07/08 踩坑清单）

| 反模式 | 为什么差 | 改成 |
|--------|----------|------|
| 每人固定「原文 + 说明」两段 | 变成词条库 | 主题叙事；次要人压进表格 |
| 反复写「编辑判断（非事实）」「样本中可见」「不代表社区共识」 | 法务腔，打断阅读 | 需要时一句「公开回复样本」即可 |
| 堆精确 likes/views 到个位 | 像爬虫日志 | 「点赞破 7k / 阅读约 38 万」量级即可 |
| 只写「后续贴了汇总」不展开 | 读者仍要点外链 | 拆出选型表/关键句 |
| 全量覆盖所有 builder | 无重点 | 头条 2–4 + 精选 6–12 人 |
| 英文日报或中英混排章节标题 | 与历史中文 digest 不一致 | 章节中文，专名英文 |
| 标题写成「AI Builders 日报 · 日期」 | 与侧边栏历史不一致 | `YYYY-MM-DD AI Builders Digest` |

### 头条段落模板

```markdown
### N. 标题（产品/冲突一句话）

**谁** 做了什么（可短引原话）。  
🔗 [原帖 @handle](https://x.com/...) · [相关官方帖](https://x.com/...)

**社区语境**
- 高赞/典型回复 1–2 条（可引原文）
- 争议点或情绪（额度/羡慕/质疑统计口径…）

**读法**：一句话行业含义。

（配图 + caption）
```

### Builder 精选模板

```markdown
### Name（@handle）— 主题

- 要点 bullet 或表格
- 若有外链深内容：单独小标题展开（选型表）
- 🔗 [原帖](https://x.com/...)
```

### 原帖链接规则（硬性，解决「文末才有链接、跳转麻烦」）

> **禁止只在文末堆链接、正文无法一点到达。**  
> **「其他硬信号 / 其他值得扫一眼」类表格必须带链接列，禁止只有 Builder+要点。**

1. **正文内联（主路径）**：每条头条、每个精选小节，在该段**首段或标题下第一行**放可点链接：
   - 写法：`>> [原帖 @handle](url)` 或 `>> [Making-of](url) · [Artifacts 博文](url)`
   - 多条相关帖用 ` · ` 分隔，**最多 3 个**，其余放文末
2. **「其他 / 次要 Builder」表格（强制 3 列）**：

```markdown
| Builder | 要点 | 原帖 |
|---------|------|------|
| **Josh Woodward** | … | [原帖](https://x.com/...) |
| **Madhu Guru** | … | [原帖](https://x.com/...) |
```

   - 列名固定：`Builder` · `要点` · **`原帖`**
   - 原帖列只放短链文字如 `[原帖](url)` / `[@handle](url)`，不要把长 URL 裸露在单元格里
   - **禁止**表下再挂一排游离链接代替表格列（如图片里 Madhu / Josh 掉在表外那种）

3. **官方博客（每篇必链）**：标题本身做成链接，或标题下第一行 `>> [全文](url)`：

```markdown
### [Claude Code quality postmortem](https://www.anthropic.com/engineering/...)
>> [全文](https://www.anthropic.com/engineering/...)
```

   - 若当日 0 篇：写「当日 feed-blogs：0 篇」即可，无需空链

4. **播客 Lookback（每集必链）**：标题链到 YouTube/官方页；有 feed `url` 则用 feed 的：

```markdown
### [Unsupervised Learning — Jürgen Schmidhuber…](https://www.youtube.com/watch?v=...)
>> [收听/收看](https://www.youtube.com/watch?v=...)
- 要点…
```

5. **文末「延伸阅读 / 原帖」**：仍保留完整列表（便于收藏/复制），与正文/表格链接**同一批 URL**
6. 飞书 Markdown 用标准 `[文字](https://...)`；交稿后抽查：表内、博客、播客均可一点打开---

## 4. 选题权重（启发式）

**高权重**

- Lab 官方/PM 发布（Codex/Claude Code/Artifacts/Work）
- 多模型路由与成本（Levie 产业结构、Fable 当经理）
- 可复制 workflow（Swyx 栈、skill 开源、固件 hacking）
- 限流/额度/reset 实感（用户真正的日常）

**中权重**

- 开源权重占比、gateway 数据
- 播客 lookback（只精炼 5–8 要点，不贴长转录）

**低权重 / 通常删**

- 纯足球/美食/无 AI 信息量的 quote
- 无上下文的单字 meme（除非是情绪板代表）

---

## 5. 飞书操作备忘（易错点）

1. `+media-insert --file`：**相对路径**，先 `cd` 到图片目录。  
2. 定位失败：caption 不在可搜索正文里；改用相邻正文 `--selection-with-ellipsis`。  
3. `我的文档库`：先 `wiki spaces get space_id=my_library`，再 `wiki +move` docs-to-wiki。  
4. 不要用 `drive +move` 冒充「我的文档库」。  
5. lark-cli 版本过旧可能缺 flag；报错时读 skill / `--help`。

---

## 6. 验收清单（交稿前自检）

- [ ] 标题 = `YYYY-MM-DD AI Builders Digest`
- [ ] 有「今日一句话」且能单独转发
- [ ] 头条 2–4 条，每条有社区语境 + 读法
- [ ] 至少 1 处「外链内容展开」（表或 bullet），不是只丢 URL
- [ ] 有评论区情绪板 + 4–6 条可带走判断
- [ ] **配图（硬性）**：≥3 张解释性图（或已注明当日无 media）；插在段落旁 + caption；非文末堆图
- [ ] **未跳过** P0：产品 UI / 信息图 / 官方功能宣发图（原帖有 media 却未下未插 = 不合格）
- [ ] **原帖链接（硬性）**：每条头条/精选在**该段正文内**有可点链接，不只堆在文末
- [ ] **其他信号表**为 3 列（Builder / 要点 / **原帖**），无表外游离链接代替
- [ ] **每篇博客、每集播客**标题或段首有全文/收看链接（0 篇可写无内容）
- [ ] 已在我的文档库，wiki 链接已发用户
- [ ] **通读**：不像「按人归档的 CRM」，像「今天发生了什么」

---

## 7. 触发示例

```text
生成今天的 AI Builders Digest
用 follow-builders 做 2026-07-15 日报，风格按 ai-builders-digest skill
补 2026-07-10 的 builders 日报并放进我的文档库
```

用户若只说「和 7-14 一样」，**直接加载本 skill 全流程**，不要临场重新发明目录体模板。
