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

### Step D — 配图

1. 从原帖 `media` / pbs.twimg.com 下高清图到本地临时目录。
2. 飞书 `docs +media-insert` 要求 **相对路径** → 先 `cd` 到图片目录。
3. 用 `--selection-with-ellipsis` 插到**对应段落旁**，不要堆在文末。
4. 每张图加 `--caption`（谁 + 什么）。
5. 优先：产品截图、信息图、选型图、官方宣发图；跳过纯头像/无意义 meme 可酌情留 1 张情绪板。

### Step E — 写 markdown → 飞书

1. 本地写完整 markdown（见第 2 节骨架）。
2. `lark-cli docs +create --title "YYYY-MM-DD AI Builders Digest" --markdown @./file.md --as user`
3. 再插图（create 后再 media-insert，定位更稳）。
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
- 原帖 URL
```

---

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
- [ ] 关键帖配图已插入对应章节（非仅文末）
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
