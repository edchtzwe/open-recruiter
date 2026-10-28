---
name: open-recruiter
description: Set up a dedicated empty Obsidian vault for AI-powered job searching — writes the vault contract, the setup interview, and installs the job-search skills into the user's agent.
---

# open-recruiter — installer

You are installing open-recruiter. This file is the whole installer. Read it, run it, then the repo is disposable.

open-recruiter is **not** a vault. It is a set of steps that scaffolds a vault the user owns. After setup this repo is irrelevant — the user's agent works from the vault's `AGENTS.md` and the installed skills.

**This works on a dedicated, empty Obsidian vault only.** If it has content, stop. Do not migrate, merge, archive, or "just this once" it. A refusal costs one message; a bad migration costs their work.

---

## 1. Get the vault path

Ask for the absolute path to a **new, empty Obsidian vault dedicated to job searching**. Do not guess, do not search their disk, do not use the current directory.

```bash
cd "<vault>" && pwd -P
```

Confirm it is an Obsidian vault:

```bash
test -d "<vault>/.obsidian" && echo vault || echo not-a-vault
```

Not a vault → stop and say: *"That folder isn't an Obsidian vault. Open Obsidian → Create new vault, point it where you want it, then give me the path again. It needs to be empty and used only for job searching."* Setting up Obsidian is their job.

## 2. Gate 1 — already seeded?

```bash
ls "<vault>/AGENTS.md" "<vault>/index.md" "<vault>/log.md" 2>/dev/null
```

Anything found → stop. It is already an open-recruiter vault; running setup twice overwrites a contract someone may have edited.

## 3. Gate 2 — empty?

```bash
find "<vault>" -type f \
  -not -path '*/.obsidian/*' -not -path '*/.trash/*' -not -path '*/.git/*' \
  -not -name '.DS_Store' -not -name 'Thumbs.db' -not -name 'desktop.ini'
```

- No output → empty. Continue.
- Any output → stop.

The refusal, in full:

> This vault already has notes in it. open-recruiter only sets up empty vaults — migrating an existing one means reformatting your notes and risking permanent damage. I won't touch it. Create a new empty vault for job searching, or don't use this system.

Do not offer to migrate. Do not offer "just this once". Empty folders are fine; a vault with only `.obsidian/` is fresh.

## 4. Create the structure

```bash
mkdir -p "<vault>/workflow/new" "<vault>/workflow/shortlisted" "<vault>/workflow/processing" \
         "<vault>/workflow/interviews" "<vault>/workflow/done" \
         "<vault>/raw/jobs" "<vault>/raw/assets"
```

## 5. Install the package

Copy the **contents** of `package/` from this repo into the vault root — byte-for-byte, no edits:

```bash
cp -R "<repo>/package/." "<vault>/"
```

Windows:

```powershell
Copy-Item -Path "<repo>\package\*" -Destination "<vault>" -Recurse -Force
```

What arrives:

```
AGENTS.md      the vault contract, including the setup gate
BOOTSTRAP.md   the setup interview — deletes itself when setup completes
index.md       root index
log.md         audit log, header only
tasks.md       shared queue, empty
```

Do not copy `SKILL.md`, `README.md`, `LICENSE`, or `.git` into the vault. Those belong to the repo.

## 6. Write the first log entry

Append to `<vault>/log.md`, dated today:

```bash
printf '%s\n' '' "## [$(date +%F)] setup | vault initialised by open-recruiter" \
  '- Structure created: workflow/, raw/, AGENTS.md, BOOTSTRAP.md, index.md, log.md, tasks.md' \
  '- Setup interview pending: follow BOOTSTRAP.md before any job-search work' \
  '' '---' '' >> "<vault>/log.md"
```

That is the only content the installer authors. Everything else is copied.

## 7. Install the skills

Copy the skill directories in `<repo>/skills/` into the agent's own skill directory — the folder the runtime actually loads skills from. Find it; do not invent one. If `skills/` is empty or missing, say so and skip this step; skills ship in a later release.

Replace any `<VAULT_PATH>` placeholder inside an installed skill with the absolute vault path.

Do not create a skill directory the runtime will not load.

## 8. Bake the vault into the agent's long-term memory

Find the file the runtime loads into every session as long-term memory (`MEMORY.md`, `CLAUDE.md`, `.cursorrules`, standing instructions). Do not invent a path. If there is nowhere it will actually load, say so instead of writing a file that does nothing.

**Append** — do not rewrite, reorder, or delete anything already there:

```
## Job search vault (open-recruiter)

Path: <absolute path>
Type: job search vault. This is NOT a personal or general vault.
Contract: read <absolute path>/AGENTS.md before reading or writing in it.

"Find jobs", "search for jobs", "my applications", "my resume", "tailor my resume",
"interview prep", "update my job search": this vault.

Other vaults (personal, notes, projects) are separate entries. Never mix them into this one.
```

The type line is deliberate: it lets the user add other vaults later with zero conflicts.

## 9. Report and stop

Five lines:

- Vault path.
- Files created — the list from step 5, once.
- Skills installed — names, or why not.
- Memory: the file appended to and the vault path in it — or why not.
- Next: the vault has `BOOTSTRAP.md`. Setup is not finished. Say "continue setup" and the agent runs the interview.

Then stop. **Do not run the interview here.** The interview lives in the vault and survives this session; the repo does not.

## What setup must never do

- Never edit a note. Copy files, append one log line, write memory. Nothing else.
- Never run on a vault with content. No exceptions, no partial migration.
- Never `git init` the vault.
- Never copy the repo's `SKILL.md`, `README.md`, or `.git` into the vault.
- Never skip the memory bake silently — if the runtime has nowhere to load it, say so.
- Never offer to skip setup. It is the setup or it is nothing.

## After setup

The vault's `AGENTS.md` is the contract; `BOOTSTRAP.md` is the interview. Until `BOOTSTRAP.md` is gone the vault is in setup mode and no job-search work happens. Once it deletes itself, job hunting is between the user and their agent.
