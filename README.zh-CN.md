<p align="center">
  <a href="https://thepopperproject.com">
    <img src="assets/logo.svg" alt="The Popper Project" width="80" height="80">
  </a>
</p>

<h1 align="center">The Popper Project Skills</h1>

<p align="center">The Popper Project 与 Karl 的 Agent Skills。</p>

<p align="center">
  <a href="https://thepopperproject.com">thepopperproject.com</a> ·
  <a href="https://agentskills.io/specification">Agent Skills 格式</a>
</p>

<p align="center"><a href="README.md">English</a> | <strong>简体中文</strong></p>

## 简介

本仓库收录 [The Popper Project](https://thepopperproject.com) 的相关 skill。Karl 是 TPP 的研究 agent。现有 skill 帮助你将其他平台上进行中的工作迁移到 Karl，保留任务上下文与项目文件。

## 现有 Skill

| Skill | 用途 |
| --- | --- |
| [migrate-to-karl](skills/migrate-to-karl/SKILL.md) | 将研究任务的上下文、决策、代码、依赖、数据和结果打包为 `KARL_MIGRATION.zip`。 |

## 使用

把下面这段话发给一直协助你工作的 agent。无需安装；agent 需要能够读取链接、访问任务上下文和文件，并生成 ZIP。

```text
请读取并按这个 skill 执行：
https://raw.githubusercontent.com/infevohq/thepopperproject-skills/main/skills/migrate-to-karl/SKILL.md

我要将当前研究任务迁移到 Karl（The Popper Project 的 agent）。
请收集你能访问的任务上下文、关键决策、当前进展、代码、依赖、数据和结果，
打包为 KARL_MIGRATION.zip。无法访问的内容请列明，并说明获取方法。
完成后给我 ZIP 文件，以及上传到 Karl 后应发送的交接提示词。
```

然后在 [thepopperproject.com](https://thepopperproject.com) 新建 Karl 对话，上传 ZIP，粘贴 agent 提供的交接提示词。Karl 会检查迁移包，说明恢复情况与下一步计划，待你确认后继续。
