# Invigilant - Claude Code Instructions

> ⚠️ **READ THIS ENTIRE FILE BEFORE DOING ANYTHING**
> Global rules (session logging, feature workflow, sub-agents) are in `~/CLAUDE.md` - read that too.

---

## 🔍 Project Detection

**You are working on: Invigilant** ([Brief description])

Verify by checking current directory contains:
- [Key file or folder that identifies this project]
- [Another identifying marker]

---

## 🚨 MANDATORY FIRST STEPS (Every Session)

1. **Read vault map:** `3 - Infrastructure/Vault Map.md`

2. **Read Obsidian docs:** `1 - Projects/Invigilant/00 - Overview.md`

3. **Read development log:** `1 - Projects/Invigilant/Development Log.md`

4. **Check session log:** `5 - Archive/Claude Sessions/Invigilant/Session-[Machine].md`

5. **Check project plans:** `1 - Projects/Invigilant/*Plan.md`

6. **Search related vault notes** for business context, infrastructure, integrations, and prior work before implementing

---

## 📍 Environment Reference

| Machine | Role | Project Path | URL | Port |
|---------|------|--------------|-----|------|
| **TALLULAH** | Windows Dev | `D:\Development\Invigilant` | http://localhost:[PORT] | [PORT] |
| **MacBook** | Mac Dev | `~/Documents/Development/Invigilant` | http://localhost:[PORT] | [PORT] |
| **MEDIA-PC** | Production | `F:\Invigilant` | https://[DOMAIN] | [PORT] |

---

## 🛠️ Tech Stack

- **Backend:** [Framework] ([Language])
- **Frontend:** [Framework/Templates]
- **Database:** [Database type]
- **Other:** [APIs, tools, etc.]

---

## ✅ Standing Permissions (Project-Specific)

| ✅ Can Do | ❌ Must Ask |
|-----------|-------------|
| Restart dev services | Restart production services |
| Git commit & push (dev branches) | Deploy to production |
| Read databases | Modify production database |
| Install dev dependencies | Push to main branch |

---

## 🔧 Common Commands

### Start Dev Server (TALLULAH)
```powershell
cd D:\Development\Invigilant
.\venv\Scripts\activate
[START_COMMAND]
```

### Start Dev Server (MacBook)
```bash
cd ~/Documents/Development/Invigilant
source .venv/bin/activate
[START_COMMAND]
```

### Production Operations (ASK before restart)
```bash
ssh media-pc "[STATUS_COMMAND]"
ssh media-pc "git -C F:\Invigilant pull"
ssh media-pc "[RESTART_COMMAND]"   # ← ASK FIRST
```

---

## 📁 Project Structure

```
Invigilant/
├── [folder]/           # [Description]
├── [folder]/           # [Description]
├── [file]              # [Description]
└── [file]              # [Description]
```

---

## 📚 Documentation Index

| Topic | Obsidian File |
|-------|---------------|
| Overview | `00 - Overview.md` |
| Architecture | `01 - Architecture.md` |
| [Topic] | `[Filename].md` |
| Dev Log | `Development Log.md` |

---

## ⚠️ Common Mistakes

1. **[Mistake]** - [How to avoid]
2. **[Mistake]** - [How to avoid]

---

*Last updated: 2026-08-01*

<!-- agent-collab:forward BEGIN (managed by wire-agent-collab.sh) -->
## Codex Second Opinion (Claude ⇄ Codex)

A **read-only** Codex MCP server is wired into this project (`.mcp.json`), exposing the `codex` / `codex-reply` tools. Codex runs in a read-only sandbox — it can read and analyse this repo but **cannot modify files**.

**Convention:** for any non-trivial design decision, diff review, or debugging conclusion, get a Codex second opinion and reconcile it against your own *before acting*: (1) form your own view first; (2) call `codex`; (3) when it returns, where you **agree** → higher confidence, where you **differ** → adjudicate (don't blindly defer, don't dismiss); (4) report both views + your reconciled conclusion, then act. Use `codex-reply` to follow up in the same thread. `/codex` runs this explicitly; do it proactively for substantive work too.
<!-- agent-collab:forward END -->
