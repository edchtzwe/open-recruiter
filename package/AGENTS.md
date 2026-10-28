---
type: schema
created: YYYY-MM-DD
updated: YYYY-MM-DD
tags: [schema, meta]
---

# AGENTS.md — job search vault

This vault is a **job search workspace**. Not a general notes vault, not an LLM wiki. It holds one person's career data: resume, applications, interview notes, salary targets. Treat everything here as private.

## Setup gate

**If `BOOTSTRAP.md` exists in this vault, setup is incomplete.**

- Take no job-search actions: no searching, ranking, applying, ingesting, and no job-search advice.
- Tell the user plainly: setup is not finished, and you cannot search for jobs you do not know they want or advise on a history you do not have.
- Then follow `BOOTSTRAP.md` to completion.

There is no skip. No setup, no work.

When `BOOTSTRAP.md` is gone this gate is inert. Leave the line in place — it re-engages if setup is ever run again.

## What this vault is

Compiled career state, not a chat log. Each write lands where a future session can find it.

```
AGENTS.md              this contract
resume-master.md       the user's master data — source of truth for every resume
BOOTSTRAP.md           setup interview (transient; gone when setup completes)
index.md               root index — pipeline and master data
log.md                 append-only audit of operations on this vault
tasks.md               shared queue
raw/jobs/              job listings as markdown, write-once
raw/assets/            resume themes, cover-letter themes, PDFs, screenshots
workflow/new/          found, unreviewed
workflow/shortlisted/  liked, ready to apply
workflow/processing/   applied, interviewing, negotiating
workflow/interviews/   prep and notes per company
workflow/done/         rejected, expired, signed, dropped
```

## Hard rules

1. **`raw/` is write-once.** A job listing is never edited after it lands. If it is wrong, say so in the workflow copy.
2. **`resume-master.md` is the source of truth.** Never invent experience, dates, or numbers. If it is not there, ask.
3. **`workflow/` is the pipeline.** A job moves forward through stages; it is never in two stages at once.
4. **Every write ends in one log line** in `log.md`, naming the stage or file it touched.
5. **Never delete a job.** Move it to `workflow/done/` with a reason. `raw/jobs/` is never pruned.
6. **Cite the listing.** A claim about a role names its file in `raw/jobs/`.
7. **Say when you don't know.** A documented gap is useful; an invented bridge gets someone rejected.
8. **This is not a general vault.** No personal notes, projects, or unrelated knowledge. It confuses agents and risks leaking private data.

## Workflow stages

- `new/` — discovered, not yet judged.
- `shortlisted/` — worth applying; ranked with a reason.
- `processing/` — application sent, or interview in progress.
- `interviews/` — per-company prep, questions asked, follow-ups.
- `done/` — closed: rejected, expired, signed, or dropped with a reason.

One file per move, one log line per move. Never copy a job into two stages.

## Resume rules

- Tailor from `resume-master.md` only. Never add experience that is not recorded there.
- One resume per application, named `<company>-<role>-<date>`.
- A tailored resume never overwrites the master.

## Page format

Markdown with YAML frontmatter. `type`, `created`, `updated` required; `company`, `role`, `url`, `stage`, `fit` where they apply.

```markdown
---
type: job
created: YYYY-MM-DD
updated: YYYY-MM-DD
company: <company>
role: <role title>
url: <listing url>
stage: new
---
```

## Sensitive data

This vault contains a resume, work history, salary targets, and application outcomes. Send none of it anywhere — no emails, uploads, or third-party services — without the user's explicit instruction for that specific action.
