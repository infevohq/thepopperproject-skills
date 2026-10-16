<p align="center">
  <a href="https://thepopperproject.com">
    <img src="assets/logo.svg" alt="The Popper Project" width="80" height="80">
  </a>
</p>

<h1 align="center">The Popper Project Skills</h1>

<p align="center">Agent skills for The Popper Project and Karl.</p>

<p align="center">
  <a href="https://thepopperproject.com">thepopperproject.com</a> ·
  <a href="https://agentskills.io/specification">Agent Skills format</a>
</p>

<p align="center"><strong>English</strong> | <a href="README.zh-CN.md">简体中文</a></p>

## About

Skills for [The Popper Project](https://thepopperproject.com). Karl is TPP's research agent. The current skill helps you move ongoing work from another platform to Karl, carrying over the task's context and files.

## Skills

| Skill | Purpose |
| --- | --- |
| [migrate-to-karl](skills/migrate-to-karl/SKILL.md) | Package a research task's context, decisions, code, dependencies, data, and results into `KARL_MIGRATION.zip`. |

## Use

Send this prompt to the agent you've been working with. No installation is needed; the agent must be able to read the link, access your task's context and files, and create a ZIP.

```text
Read and follow this skill:
https://raw.githubusercontent.com/infevohq/thepopperproject-skills/main/skills/migrate-to-karl/SKILL.md

Move our current research task to Karl, The Popper Project's agent.
Gather the task context, decisions, progress, code, dependencies, data, and
results available to you, and package them as KARL_MIGRATION.zip.
List anything you cannot access, with instructions for retrieving it.
Give me the ZIP and the handoff prompt to paste into Karl.
```

Then open a new Karl conversation at [thepopperproject.com](https://thepopperproject.com), upload the ZIP, and paste the handoff prompt your agent provides. Karl will check the bundle and outline the next steps for your confirmation.
