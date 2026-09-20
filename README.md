# Git ACP (One-Click Commit)

One-click git add/commit/push with Conventional Commits + Gitmoji messages, packaged as a Codex skill.

## 参考依赖

- [`gacp-studio`](https://github.com/hpsks416/gacp-studio)：本技能优先调用的本地可视化提交面板（端口 `8787`），用于减少 token 消耗。

This is a [Codex](https://github.com/openai/codex) skill. Copy this repository into `~/.codex/skills/git-acp/` to use it.

## Structure

- `SKILL.md` — skill instructions and workflow (preferred path: gacp-studio; fallback: direct git).
- `agents/openai.yaml` — Codex UI metadata.
- `references/push-fallbacks.md` — git push fallback notes.

## License

[MIT](LICENSE)
