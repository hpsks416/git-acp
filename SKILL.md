---
name: git-acp
description: One-click git add/commit/push using the local gacp-studio panel first to reduce token usage, with direct git as fallback. Use when the user asks to commit, submit, or push their work.
---

# Git ACP (One-Click Commit)

Turn a "帮我提交/推送" request into one safe, deterministic flow: `git add` → `git commit` → `git push`, using a Conventional Commits message with a leading Gitmoji emoji.

## Preferred path: local gacp-studio

To keep token usage low, delegate git inspection and execution to the local gacp-studio panel first. It only binds to `127.0.0.1` and performs the real `git add / commit / push` commands.

- Studio directory: `C:\Users\Razer\Documents\Codex\2026-09-20\y\gacp-studio` (override with `GACP_STUDIO_DIR`).
- Start it headless: `python "<GACP_STUDIO_DIR>\server.py" --no-browser`.
- Base URL: `http://127.0.0.1:8787` (port override: `GACP_STUDIO_PORT`).
- Verify with `GET /api/health`.

Use these compact JSON endpoints instead of rendering the web UI:

1. Inspect: `GET /api/repo?repo=<absolute repo path>` returns branch, remote, ahead/behind, staged/unstaged/untracked files, and recent commits. If there is nothing to commit, stop and say so.
2. Commit + optional push: `POST /api/commit` with `{"repo_path":"<absolute path>","message":"<emoji> <type>(<scope>): <subject>","push":true|false}`.
3. Push only: `POST /api/push` with `{"repo_path":"<absolute path>"}`.

Still build the message yourself with the table below. If gacp-studio is missing, cannot be started, or `/api/health` fails, fall back to direct git.

## Fallback: direct git

1. Inspect first. Run `git status --porcelain=v1 -b` and `git branch --show-current`. If there is nothing to commit, stop and say so.
2. Build the message. Pick the closest type from the table below, add an optional scope, keep the subject imperative and short. Add `!` and a `BREAKING CHANGE:` footer only for breaking changes.
3. Stage with `git add -A`, unless the user named specific paths.
4. Commit with `git commit -m "<emoji> <type>(<scope>): <subject>"`.
5. Push with `git push`; if the branch has no upstream, use `git push -u origin <branch>`.

## Types and emoji

| type | emoji | use when |
| --- | --- | --- |
| feat | ✨ | new feature |
| fix | 🐛 | bug fix |
| docs | 📝 | documentation only |
| style | 💄 | formatting, no logic change |
| refactor | ♻️ | code restructure, no behavior change |
| perf | ⚡ | performance improvement |
| test | ✅ | tests |
| build | 📦 | build system or dependencies |
| ci | 👷 | CI configuration |
| chore | 🔧 | maintenance, no production code |
| revert | ⏪ | reverting a commit |

## License Default

When the user asks to publish, package, or initialize a repository and has not specified a license, default to MIT:

- Add a `LICENSE` file with the current year and the repository owner's name (and email if provided) as the copyright holder.
- Update the `README.md` license section to reference MIT.
- Do not change an existing license without being asked.

## Safety

- Only push when the user asked to push; a bare "提交" means commit only.
- Never force-push (`--force`, `-f`) unless the user explicitly asked.
- Never commit secrets, `.env`, credentials, or large binaries; report them instead.
- If a step fails, show the git output and stop; do not retry blindly.
- If push fails, read [references/push-fallbacks.md](references/push-fallbacks.md).
