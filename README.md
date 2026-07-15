# AI Builders Digest Skill

从 [zarazhangrui/follow-builders](https://github.com/zarazhangrui/follow-builders) 中央 feed 生成 **叙事化** 的「AI Builders Digest」飞书日报。

> 目标不是爬虫导出，也不是「每人一条原文+说明」词条库，而是可扫的编辑部日报：头条冲突、社区语境、可抄工作流、情绪板、判断。

## 这个 skill 解决什么

| 差的日报 | 好的日报（本 skill） |
|----------|----------------------|
| 按人归档 CRM | 按主题/冲突讲故事 |
| 法务腔免责声明 | 一句「读法」 |
| 只丢外链 | 拆出选型表 / 关键句 |
| 精确到个位的 likes | 量级表述 |
| 全量覆盖无重点 | 头条 2–4 + 精选 |

数据源仍是 follow-builders；本仓库只固化 **怎么编、怎么写、怎么落到飞书**。

## 仓库结构

```text
ai-builders-digest/
├── SKILL.md                              # Agent 执行说明（主文件）
├── README.md
├── LICENSE
└── references/
    ├── gold-sample-2026-07-14.md         # 金样例结构
    └── anti-patterns-0707-0708.md        # 反例速记
```

## 安装

### Grok / Claude Code 等（用户级 skill）

```bash
# 克隆到 user skills 目录（路径按你的 agent 调整）
git clone https://github.com/larryboiNEUQ/ai-builders-digest.git ~/.grok/skills/ai-builders-digest

# 或 agents 共用目录
git clone https://github.com/larryboiNEUQ/ai-builders-digest.git ~/.agents/skills/ai-builders-digest
```

Windows 示例：

```powershell
git clone https://github.com/larryboiNEUQ/ai-builders-digest.git "$env:USERPROFILE\.grok\skills\ai-builders-digest"
git clone https://github.com/larryboiNEUQ/ai-builders-digest.git "$env:USERPROFILE\.agents\skills\ai-builders-digest"
```

### 项目级

```bash
git clone https://github.com/larryboiNEUQ/ai-builders-digest.git .agents/skills/ai-builders-digest
# 或 .grok/skills/ai-builders-digest
```

安装后重启 / 重载 agent，使 skill 被索引。

## 依赖

- 能访问 GitHub raw（拉 feed）
- **X/Twitter 检索能力**（thread + 高赞评论；本 skill 的关键差异）
- 可选：飞书 [`lark-cli`](https://github.com/larksuite/cli) + 用户登录  
  - `docs +create` / `+update` / `+media-insert`  
  - `wiki +move` 迁入「我的文档库」

没有飞书时，仍可只输出 markdown 日报。

## 怎么用

对 agent 说：

```text
按 skill ai-builders-digest 生成今天的 AI Builders Digest
```

或：

```text
/ai-builders-digest
用 follow-builders 做 2026-07-15 日报，风格按 ai-builders-digest skill
```

给 Codex / 其他 agent 时，**点名 skill 名 + 金样例**，比「模仿之前日报」更稳：

```text
按 ai-builders-digest skill 生成 YYYY-MM-DD AI Builders Digest。
风格以 references/gold-sample-2026-07-14.md 为准，
禁止「原文+说明」词条体和法务腔。
```

## 输出约定

| 项 | 规则 |
|----|------|
| 标题 | `YYYY-MM-DD AI Builders Digest` |
| 语言 | 简体中文为主，专名/handle 英文 |
| 落库（可选） | 飞书「我的文档库」`my_library`，不是云空间随便根目录 |

## 工作流摘要

1. 拉 `feed-x` / `feed-blogs` / `feed-podcasts`  
2. 选题聚类 → 2–4 条头条  
3. X 补上下文 + 外链展开  
4. 写 markdown（骨架见 `SKILL.md`）  
5. 可选：配图、写飞书、迁我的文档库  
6. 用验收清单自检  

完整步骤见 [`SKILL.md`](./SKILL.md)。

## 致谢

- Feed 与 builder 列表：[@zarazhangrui/follow-builders](https://github.com/zarazhangrui/follow-builders)  
- 本 skill 为独立的 **digest 编辑规范**，不隶属于 follow-builders 官方

## License

MIT
