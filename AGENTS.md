# Agent Instructions for ProxyResource

This repository contains upstream-derived resources plus xuanwoa's local customizations. Be conservative: do not make broad repository-wide edits unless the user explicitly asks for them.

## Scope

When the user says "Loon 项目", "Loon 配置", "lcf", "分流规则", "策略组", "插件", or references this repository without a narrower path, maintain only this file by default:

`Tool/Loon/Lcf/zh-CN/Loon_Auto_Select_Config_By_iKeLee.lcf`

Do not modify other Loon config files, generated assets, or unrelated repository files unless explicitly requested.

## Workflow

1. Read the current target file before editing. Do not rely only on memory.
2. Inspect the relevant sections before changing them: `[Remote Filter]`, `[Proxy Group]`, `[Rule]`, `[Remote Rule]`, `[Plugin]`.
3. Apply small targeted patches. Do not rewrite the whole `.lcf`.
4. Compare upstream only for reference. Do not whole-file merge `luestr/ProxyResource` into this customized file.
5. After edits, verify the exact lines changed and run `git diff -- Tool/Loon/Lcf/zh-CN/Loon_Auto_Select_Config_By_iKeLee.lcf AGENTS.md Tool/Loon/Lcf/zh-CN/AGENTS.md`.
6. If the user says "提交", make a scoped commit containing only the relevant files. If the user says "推送", push after commit.

## Current Loon steady state

- Default / `FINAL`: `全节点手动策略`.
- First non-comment `[Proxy Group]` entry: `全节点手动策略`.
- Remote rules should stay minimal unless the user names a service.
- Keep `LAN_SPLITTER` and `REGION_SPLITTER` direct rules.
- Do not restore Telegram / TikTok / AI / Speedtest remote rules as a bundle.
- AI strategy groups may exist and should be preserved unless the user explicitly asks to remove them.
- Plugin policies for DNS leak prevention, BoxJs, Sub-Store, and Script-Hub should use `全节点手动策略` unless the user changes the policy.

## Rule additions

Only add services the user explicitly names.

Accuracy/source preference:

1. Official service/project documentation, when usable.
2. Popular maintained GitHub rule repositories, especially `blackmatrix7/ios_rule_script` under `rule/Loon/<Service>/<Service>.list`.
3. Existing kelee/luestr `.lsr` rules when they fit this config style.
4. Small local `[Rule]` patches such as `DOMAIN-SUFFIX` only for niche services without mature rule sets.

Rules:

- Prefer `[Remote Rule]` references over copying hundreds of domains into `[Rule]`.
- For small UK finance apps without mature public rules, group them in `Tool/Loon/Rules/UKFinance.lsr` and reference that single rule set from `[Remote Rule]` with `policy=英国手动策略`.
- Do not mix multiple rule sources for the same service unless debugging a concrete miss.
- Be careful with broad categories such as Apple, Google, and Microsoft; prefer specific services where possible.
- Keep `FINAL, 全节点手动策略` unless explicitly told otherwise.
- For finance/banking services that need a stable country IP, prefer a dedicated country manual strategy group rather than automatic global fallback.

Recent example:

- PayPal/Zopa/Freetrade/Plum/Tide/Yonder are grouped in `Tool/Loon/Rules/UKFinance.lsr`; PayPal's many-domain rule block is vendored from blackmatrix7 with attribution.

## Git hygiene

The repository may contain unrelated untracked or modified files. Do not include them in commits unless they are part of the user's request.
