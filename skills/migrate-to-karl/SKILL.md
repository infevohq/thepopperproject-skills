---
name: migrate-to-karl
description: Export one research task to KARL_MIGRATION.zip with its full working context, project files, dependencies, and data for a single chat upload to Karl. Use when migrating an existing research task or handing it off to a fresh Karl session.
---

# migrate-to-karl

Produce `KARL_MIGRATION.zip`: one upload containing `KARL_CONTEXT.md`, a file manifest, and the task's project files and data. Karl should unpack it, read the context, and recover the working project without requiring the user to attach each file separately.

Comprehensive over concise: no length limit, no importance triage. Include everything relevant to the task; when unsure whether something is relevant, include it. Never drop details to keep the document short.

## Collect

Identify the single research task from the user's request; ask only if its scope is unclear (one task per bundle). Then gather:

- **Environment**: OS and architecture; language runtime versions; dependency manifest files (requirements.txt, environment.yml, renv.lock, DESCRIPTION, package.json, …) in full text; GPU or special hardware needs; external compute servers (address, purpose, where login is configured — never credentials). Record what you find verbatim; probe the current machine for OS and runtime versions only when the user confirms this machine is the research environment; mark anything not found as "not found — confirm with user"; never invent values.
- **Memory and decisions**: key decisions with reasoning; analysis workflow (steps, order, parameter choices and why); pitfalls (symptom → cause → fix); open questions not yet settled. Do not compress or omit details. Where sources conflict, record every version and flag the conflict as an open question — do not silently pick one.
- **Assets**: every task-related file — source code, packages, notebooks, dependency manifests, configs, data, intermediates, results, notes — with its original path, size, and a one-line description. Gather the actual readable local files for the ZIP; keep binary data out of the Markdown. Include whole relevant Python/R package directories, preserving their structure and metadata such as `pyproject.toml`, `requirements.txt`, `renv.lock`, `DESCRIPTION`, `NAMESPACE`, `R/`, `man/`, `src/`, and `data/`. Record remote or unavailable assets and how to retrieve them.

## Output

Write `KARL_CONTEXT.md` following the template below. Write the document in the user's working language, including the notes in Sections 0 and 7 of the template.

- All sections 0–8 plus appendices A/B are present; empty sections stay with "None" and a note — no section may be dropped.
- Lightweight scripts and configs (≤200 lines each) are embedded in full in the appendices for readability; longer scripts are referenced by their ZIP paths. The ZIP contains the full files regardless of length. Dependency manifests live in Appendix A only — never duplicate them into Appendix B; note missing manifests in §3.3. Redact secret values and mark those redactions in both the document and bundled copies.
- The document must be fully understandable without the original workspace or conversation.

## Package

Create a temporary staging directory and copy an explicit inventory of task-related files into it. Preserve the original files; review configs, notebooks, logs, and data for credentials before copying. Exclude credential files, `.git/`, virtual environments, installed dependency caches, and unrelated workspace files. Capture relevant memory/decision content in `KARL_CONTEXT.md` rather than including unrelated chat histories.

Use this layout, with the context and manifest at the ZIP root:

```text
KARL_MIGRATION.zip
├── KARL_CONTEXT.md
├── manifest.json
└── project/
    ├── <original project-relative directories and files>
    └── external/<unique source label>/<files from other task locations>
```

- Keep project-relative paths intact so imports, R package structure, and relative data references still work. Use unique source labels for files outside the project root; record any necessary path adjustments. Preserve text encoding and binary bytes except for explicitly recorded credential redactions. Do not flatten directories or overwrite same-named files from different locations.
- Bundle task-related data and results whenever the final ZIP fits the account's current single-file upload limit. The default is **100 MiB**; accounts with expanded drive limits allow **500 MiB**. If the account limit is unknown, use 100 MiB. Check the actual ZIP size, since binary data may compress poorly. If it exceeds the limit, agree with the user which large assets to leave external; record their exact locations, exclusion reasons, and retrieval steps. Keep all asset inventory entries even when the bytes cannot be bundled.
- Every ZIP member uses a relative path with no `..` components or absolute paths. Include regular files and directories only. Resolve a task-related symlink to readable regular-file contents and record the source mapping, or list it as excluded with a reason; never package links or special files.
- Create a standard, unencrypted ZIP using `ZIP_DEFLATED` (for example, Python's `zipfile`), compatible with Karl's Python runtime. Store it in the workspace root or the user-specified output directory. Keep the output ZIP outside the staging tree so it cannot include itself.

Write `manifest.json` with this structure:

```json
{
  "schema_version": 1,
  "task": "<task name>",
  "generated_at": "<ISO-8601 timestamp with timezone>",
  "included_files": [
    {
      "source_path": "<original local path, or generated context>",
      "archive_path": "project/R/analysis.R",
      "size_bytes": 1234,
      "sha256": "<SHA-256 of the bundled bytes>",
      "description": "<purpose; redactions or path adjustments if any>"
    }
  ],
  "excluded_assets": [
    {
      "source_path": "<original path or remote location>",
      "reason": "<remote, unreadable, credential file, too large, etc.>",
      "retrieval_instructions": "<how to restore access; no credentials>"
    }
  ]
}
```

`included_files` inventories **every regular file in the ZIP except `manifest.json` itself**, including `KARL_CONTEXT.md`. Hash and count the staged bytes after any redaction. Match every task asset in Section 7 to either an included path or an explicit exclusion.

Before handing off, reopen the ZIP, verify its CRCs, unique member names, safe relative paths, root context/manifest, and every manifest size/hash. Extract into a fresh temporary directory and verify the same layout and hashes there. Report the ZIP path, compressed size, included file count, and any excluded assets; distinguish a self-contained project from a bundle that still needs external data.

Then hand the user:

1. The path to `KARL_MIGRATION.zip` and its verified inventory summary.
2. These instructions — upload the **single ZIP** through Karl's chat attachment button or drag it into the conversation, then paste the prompt below:
   > 附件是我的研究任务迁移包 KARL_MIGRATION.zip。请先查看 ZIP 文件清单，检查路径安全及解压后的总大小，在工作目录中新建一个独立目录并按原目录结构解压。完整阅读根目录的 KARL_CONTEXT.md 和 manifest.json，核对清单中的文件大小与 SHA-256，并根据上下文检查 project/ 中的代码、依赖配置和数据。请区分包内已有资产与仍需从外部获取的资产，向我复述研究方向与当前进展、计算环境与依赖、恢复后资产的位置、缺失项和下一步计划。等我确认无误后，再安装依赖、运行项目代码或开展下一步研究。
   >
   > (English) The attachment is my research migration bundle, KARL_MIGRATION.zip. Inspect its members, check path safety and the total uncompressed size, then extract it into a new working directory while preserving its layout. Read the root KARL_CONTEXT.md and manifest.json in full, verify the listed sizes and SHA-256 hashes, and inspect the code, dependency configs, and data under project/ as directed by the context. Distinguish bundled assets from assets that still need external retrieval. Restate my research direction and progress, compute environment and dependencies, restored asset locations, missing items, and next steps. Wait for my confirmation before installing dependencies, running project code, or continuing the research.

## Hard rules

1. Never write secrets into the document, manifest, or ZIP — API keys, tokens, passwords, private keys, connection strings. Where credentials matter, write "needs reconfiguration in the new environment" and where to configure it. Review staged copies as well as prose; document redactions and excluded credential files.
2. Binary data and compute outputs belong in the ZIP, never embedded or dumped into Markdown. Excluded assets remain in the inventory with reasons and retrieval instructions; never claim their bytes were migrated.
3. State compute limits explicitly: the default target sandbox is an 8-core 16 GiB CPU environment. Identify GPU/hardware-dependent computations that must stay on external servers; record actual runtime/dependency availability as unverified until Karl checks it. Do not assume the ZIP transfers an installed environment.
4. When information is missing or uncertain, ask the user; do not guess.
5. Cross-references ("see Section 6") are allowed as navigation, as long as the referenced content is complete at the target.

## Self-check

- [ ] Sections 0–8 + appendices A/B present; empty ones marked "None"
- [ ] One uploadable `KARL_MIGRATION.zip` contains root `KARL_CONTEXT.md`, `manifest.json`, and full task files with project structure preserved
- [ ] Every task asset is accounted for by a bundled path or an exclusion reason and retrieval instructions; original paths are clearly distinguished from restored paths
- [ ] Dependency manifests embedded in full (or marked missing with reason)
- [ ] Lightweight scripts embedded; full reusable scripts/configs and Python/R package metadata included in the ZIP regardless of line count
- [ ] ZIP size checked against the account limit; CRCs, paths, member uniqueness, and manifest hashes/sizes verified, including after a trial extraction
- [ ] No secrets in prose, manifest, or bundled files
- [ ] Compute/GPU limitations stated
- [ ] No elisions ("see above", "omitted", …); cross-referenced content complete at target
- [ ] Nothing task-related dropped for brevity
- [ ] Readable standalone, without the original workspace

## Template

````markdown
# KARL_CONTEXT: <task name>

> Generated by migrate-to-karl on <YYYY-MM-DD>. Read the full document, including appendices.

## 0. About this migration
- Source platform: <e.g. Claude Code / Cursor / local scripts; if unknown: "confirm with user">
- Task: <name>
- Generated by: <agent>
- Reading notes: extract this ZIP into a new working directory, then read this document and manifest.json in full. The project/ directory contains bundled files; lightweight scripts/configs are also embedded in the appendices. Section 7 maps original paths to paths relative to the extracted ZIP root and identifies assets that still require external retrieval. Original local/server paths do not automatically exist in Karl.

## 1. Research direction and background
<field, scientific question, motivation, disease/target/methods as applicable>

## 2. Current progress and next steps
### 2.1 Completed
### 2.2 In progress
### 2.3 Next steps

## 3. Compute environment
### 3.1 Hardware and OS
<CPU, memory, OS; GPU if any>
### 3.2 Language runtimes and versions
### 3.3 Dependencies
<manifest names, original paths, and bundled paths; full non-secret text in Appendix A; missing manifests and redactions explicitly noted>
### 3.4 Limitations
<GPU dependencies, external compute dependencies; which computations cannot run in the target sandbox>

## 4. Analysis workflow
<complete steps from raw data to conclusions: order, methods, key parameters and why>

## 5. Key decisions and discussion record
<chronological or by topic: decision → conclusion → reasoning; complete, no omissions>

## 6. Pitfalls
<symptom → cause → fix; keep details needed for reproduction>

## 7. Asset inventory and migration mapping
> Bundled paths are relative to the extracted ZIP root. Original paths are provenance from the previous environment. Excluded assets are not in this ZIP and must be retrieved using the instructions below.

| Asset | Original path | Bundled path | Status | Description / retrieval instructions |
| --- | --- | --- | --- | --- |
| <name> | <local/server/cloud path> | <project/relative/path or None> | <included / excluded: reason> | <content, format, size; retrieval steps if excluded> |

## 8. Credentials to reconfigure
<which credentials must be reconfigured in the new environment and where; this document contains no credentials themselves>

## Appendix A: Dependency manifests

<file path="<relative path>">
```
<full verbatim content>
```
</file>

## Appendix B: Reusable scripts and configs

<file path="<relative path>">
```python
<full verbatim content>
```
</file>
````
