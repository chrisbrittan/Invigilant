# Invigilant - Claude Code Instructions

> ⚠️ **READ THIS ENTIRE FILE BEFORE DOING ANYTHING**
> Global rules (session logging, feature workflow, sub-agents) are in `~/.claude/CLAUDE.md` — read that too.

---

## 🔍 What this is

**Invigilant is the portable oversight core**: ONE Rust implementation of the deterministic danger
gate, the heartbeat/light state machine and the reviewer bus — replacing the two hand-maintained
implementations that exist today (bash on macOS, PowerShell on Windows). Cross-platform,
installable, agent-agnostic. **Apache-2.0, public from commit 1.**

An *invigilator* is the independent official who watches a candidate to ensure they do not cheat.
That is exactly this tool's relationship to a coding agent.

---

## 🚨 THE DESIGN IS NOT IN THIS REPO — READ IT FIRST

The full plan is **`PORTABLE-CORE-PLAN.md`**, which lives in the private platform-management repo
alongside the two legacy implementations (path in the vault). 1,036 lines, 14 sections, all 11
owner decisions closed 2026-08-01. It stays there until M0 moves it.

**Read it before writing any code.** In particular:

| § | Why it matters |
|---|---|
| **§8.1 — ONE LEXER** | The single most load-bearing decision in the project. Non-negotiable. |
| **§3a — threat model** | What "green" actually means. Must appear verbatim in README/SECURITY.md. |
| **§5** | The paid-for lessons — behaviours whose absence was a real incident. |
| **§9** | The conformance bar and the three corpus shapes. |
| **§10** | Two-repo split, cutover doctrine, the freeze. |

**Do not re-litigate closed decisions without new evidence.** The plan records adjudications
inline precisely so the next session doesn't reopen them.

---

## 🚨 MANDATORY FIRST STEPS (Every Session)

1. Read `PORTABLE-CORE-PLAN.md` (above) — or at minimum the sections your task touches
2. **Read vault map:** `3 - Infrastructure/Vault Map.md`
3. **Read Obsidian docs:** `1 - Projects/Invigilant/00 - Overview.md`
4. **Read development log:** `1 - Projects/Invigilant/Development Log.md`
5. **Check session log:** `5 - Archive/Claude Sessions/Invigilant/Session-MacBook.md`
6. Also check the **platform repo's** Development Log — much of the live work is there until M1

---

## 🧱 The three load-bearing rules

**1. ONE LEXER (§8.1).** Tokenise once, in the analyzer; every downstream predicate consumes those
tokens and **never re-scans raw text**. The bash gate has two views of a command — bash's real lexer
and the line-oriented preprocessing inside `danger.awk` — and every place they disagree has been a
security hole. This was paid for with 25 consecutive bypass fixes across two implementations. The
same divergence produces bypasses *and* false positives, which is why "just tighten the regex" has
failed repeatedly. Porting the split reproduces the entire bug family in a new language.

**2. The worker is COOPERATIVE (§3a).** Worker, watcher, hooks, bus and git hook all run as the same
OS user with the same privileges. An agent that *wants* to defeat this can. Invigilant is a control
against **accidental** harm — a capable second reader — **not** enforcement against an adversarial
agent. Say this plainly in README.md and SECURITY.md; never imply otherwise in a matrix or a docstring.

**3. Never add a predicate to the analyzer; only remove an over-block** — with ground truth and both
suites green. A BYPASS is always fixable in the driver; an OVER-BLOCK originating in the analyzer is
not. A false positive blocks real work and gets the gate disabled, which is worse than the hole.

---

## 🛠️ Tech Stack (planned — nothing built yet)

- **Language:** Rust, one static multi-call binary `invigilant`. No daemon, no sockets, no service.
- **Crates:** `ovs-bus` (tree hash, atomic writes, heartbeat/verdict/stop/lifecycle/goal/markers),
  `ovs-danger` (pure `analyze(cmd, cwd, home) → allow|ask|deny + reason`), `ovs-adapters`
  (per-dialect hook encoders + reviewer runners), `ovs-watch` (loop, evidence, publish pipeline,
  process-tree control), `ovs-light`, `invigilant` (bin).
- **IPC:** the file bus IS the IPC. Enforcement must survive any long-lived process dying.
- **Targets:** macOS + Windows first; Linux post-v0.1. CI is greenfield (no workflows exist yet).

---

## 📍 Two repos, by design (§10)

| repo | holds | visibility |
|---|---|---|
| **Invigilant** (this) | Rust core, conformance corpus, docs, CI, release pipeline | **public from commit 1** |
| **platform repo** | platform sugar, the **golden-file corpus**, the two legacy trees, the legacy-validation runner | private, always |

**The public boundary is enforced, not remembered.** No machine names, absolute home paths, tunnel
hostnames or private repo names in code, comments, test data or commit messages. The golden-file
corpus **never enters this repo**. Corpus extraction *scrubs as it transcribes*.

`.claude/settings.json` is gitignored here for exactly this reason (it hardcodes one developer's
home directory) even though the private platform repo tracks its own copy deliberately.

---

## ✅ Standing Permissions (Project-Specific)

| ✅ Can Do | ❌ Must Ask |
|-----------|-------------|
| Commit on dev branches | **Push to `main`** (public repo — every push is publication) |
| Read/run the conformance corpus | Add an `allow` case to `danger/**` or `conformance/danger/**` |
| Run the legacy suites in the platform repo | Rebuild the council `.app` fleet (re-signing may re-trigger TCC consent) |
| Install dev dependencies | Modify the legacy trees during the FREEZE |

`danger/**` and `conformance/danger/**` are CODEOWNERS-protected and closed to external PRs — a
plausible-looking corpus change that adds an `allow` case IS an attack surface (§13.11).

---

## 📁 Current state

**Pre-M0.** This repo is scaffold only — no Rust yet. Everything live is still in the platform repo.

M0 prerequisites (all in the platform repo):
- [x] Relocate platform sugar out of `scripts/oversight/` → `scripts/launchers/` (2026-08-03)
- [x] Convert `migrate.sh` off `lib/bus.sh` (2026-08-03)
- [x] Fix generators emitting core-tree paths (2026-08-03; Windows one deferred to M4 for cause)
- [x] Rebuild the council `.app` fleet + remove the transitional shim (2026-08-03, verified 15/15)
- [ ] FREEZE both legacy trees
- [ ] Conformance corpus extraction; legacy-validation runner

---

## ⚠️ Common Mistakes

1. **Answering from the plan's *stated intent* instead of executing the code.** Two live security
   holes were found by running the guards against adversarial input, minutes after the docs said
   they were solved. The corpus must include adversarial probes, not transcriptions of what the
   current suites already assert.
2. **Reading empty output as an empty result set.** A zsh glob that doesn't match aborts the whole
   command before your `grep` runs. Assert that a check *can* fail before trusting that it passed.
3. **Quoting 640 as a union.** It is a SUM of two suites that mirror each other; the deduplicated
   union is ~130–155 for the danger gate. "Both suites green" is the ship gate; the deduplicated
   corpus size is the coverage measure.
4. **Treating `ask` as a safety tier.** All launchers run `--dangerously-skip-permissions`; measured
   2026-08-01, an `ask` executes with no prompt. Accepted as-is by owner decision — do not re-raise
   it as a finding.

---

*Last updated: 2026-08-03*

<!-- agent-collab:forward BEGIN (managed by wire-agent-collab.sh) -->
## Codex Second Opinion (Claude ⇄ Codex)

A **read-only** Codex MCP server is wired into this project (`.mcp.json`), exposing the `codex` / `codex-reply` tools. Codex runs in a read-only sandbox — it can read and analyse this repo but **cannot modify files**.

**Convention:** for any non-trivial design decision, diff review, or debugging conclusion, get a Codex second opinion and reconcile it against your own *before acting*: (1) form your own view first; (2) call `codex`; (3) when it returns, where you **agree** → higher confidence, where you **differ** → adjudicate (don't blindly defer, don't dismiss); (4) report both views + your reconciled conclusion, then act. Use `codex-reply` to follow up in the same thread. `/codex` runs this explicitly; do it proactively for substantive work too.
<!-- agent-collab:forward END -->
