# Agent Instructions for Loon Auto Select Config

Target file in this directory:

`Loon_Auto_Select_Config_By_iKeLee.lcf`

This is xuanwoa's customized Loon config. Keep changes small and intentional.

## Sections to check before editing

- `[Remote Filter]`: country/all-node filters.
- `[Proxy Group]`: policy group order and strategy definitions.
- `[Rule]`: local exact rules and `FINAL`.
- `[Remote Rule]`: remote rule-set references.
- `[Plugin]`: plugin list and `policy=` bindings.

## Invariants

- The first non-comment proxy group should remain `全节点手动策略`.
- `FINAL` should remain `FINAL, 全节点手动策略` unless the user explicitly changes the default.
- `LAN_SPLITTER` and `REGION_SPLITTER` should remain enabled and direct.
- Do not restore upstream's Telegram/TikTok/AI/Speedtest remote-rule bundle unless the user names those services.
- Do not create a `全球手动策略` group for this config; the customized default group is `全节点手动策略`.
- If the user says "全球手动策略" casually, confirm whether they mean the existing `全节点手动策略`; do not invent a new group.

## Adding country manual strategies

If a country filter already exists but the manual group is commented, prefer uncommenting the existing group line instead of creating a duplicate.

Example:

```ini
英国手动策略=select, 英国全部节点, img-url=https://raw.githubusercontent.com/Orz-3/mini/master/Color/UK.png
```

## Adding service rules

Use this order of preference:

1. Popular maintained Loon rule source: `blackmatrix7/ios_rule_script`.
2. kelee/luestr `.lsr` source when already used by this config style.
3. Project-owned grouped rule files such as `Tool/Loon/Rules/UKFinance.lsr` for several small related services sharing one policy.
4. Minimal local `DOMAIN-SUFFIX` / `DOMAIN` rule when no mature service-specific rule exists and grouping is not worthwhile.

Preferred remote-rule format:

```ini
https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Loon/<Service>/<Service>.list, policy=<策略组>, tag=<Service>, enabled=true
```

For niche UK finance apps, prefer the grouped project rule file instead of one remote rule per app:

```ini
https://raw.githubusercontent.com/xuanwoa/ProxyResource/main/Tool/Loon/Rules/UKFinance.lsr, policy=英国手动策略, tag=UK Finance, enabled=true
```

Inside `UKFinance.lsr`, keep entries small and exact. Example:

```ini
DOMAIN-SUFFIX,zopa.com
```

## Placement

- Local `[Rule]` service overrides must be before `FINAL`.
- `[Remote Rule]` service entries should stay near existing remote rules and avoid duplicates.
- Keep direct LAN/CN region rules enabled.

## Verification checklist

After editing, verify with file search or equivalent:

- Expected strategy group exists exactly once and is not commented.
- Expected local or remote service rule exists exactly once.
- `FINAL, 全节点手动策略` still exists.
- No unintended TG/TikTok/AI/Speedtest bundle was added.
- `git diff` contains only intended changes.
