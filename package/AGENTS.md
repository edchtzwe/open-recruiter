---
type: schema
created: 2026-10-27
updated: 2026-10-30
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
BLUEPRINT.md           the build manual — skill specs and script pseudocode (permanent; skills and scripts are authored from it, never shipped)
index.md               root index — pipeline and master data
log.md                 append-only audit of operations on this vault
tasks.md               shared queue
raw/jobs/              job listings as markdown, passed screening
raw/jobs/done/         archived jobs: stale (>20 days), rejected, expired
raw/assets/            resume themes, cover-letter themes, PDFs, screenshots
workflow/shortlisted/  interested, ready to apply — working doc created here
workflow/processing/   application sent, awaiting response
workflow/interviews/   interview scheduled — prep, research, follow-ups
```

## Hard rules

1. **`raw/jobs/` content is write-once.** A job listing is never edited after it lands. Location may move to `raw/jobs/done/` for archival; content stays unchanged.
2. **`resume-master.md` is the source of truth.** Never invent experience, dates, or numbers. If it is not there, ask.
3. **`workflow/` is the pipeline.** A job exists in exactly one stage at a time. The working document moves forward; it does not spawn copies.
4. **Every write ends in one log line** in `log.md`, naming the stage or file it touched.
5. **Archive, never delete.** Stale, rejected, or expired jobs move from `raw/jobs/` to `raw/jobs/done/`. `raw/jobs/` is never pruned.
6. **Cite the listing.** A claim about a role names its file in `raw/jobs/` (or `raw/jobs/done/` if archived).
7. **Say when you don't know.** A documented gap is useful; an invented bridge gets someone rejected.
8. **This is not a general vault.** No personal notes, projects, or unrelated knowledge. It confuses agents and risks leaking private data.

## Workflow stages

A job enters the pipeline when the user creates a working document in `workflow/shortlisted/`. That document moves through stages, accumulating history. It never spawns children; it moves.

- `shortlisted/` — interested, ready to apply. Working doc created here, links to `raw/jobs/<job>.md`, initial notes.
- `processing/` — application sent. Same doc moves here, adds application date, resume link, recruiter contact.
- `interviews/` — interview scheduled. Same doc moves here, adds prep, company research, questions, follow-ups, outcomes.

When the journey ends (signed, rejected, expired), archive the `raw/jobs/` entry to `raw/jobs/done/` and update the working doc with the outcome.

**One doc, one journey.** The document is the pipeline state.

## `raw/jobs/` rules

Jobs land here after passing screening. Each file is a markdown document with frontmatter and the full job description.

**Frontmatter fields:**

```markdown
---
type: job
created: YYYY-MM-DD
updated: YYYY-MM-DD
company: <company>
role: <role title>
url: <listing url>
source: <where scraped from>
recruiter: <name, agency, email if present> (optional)
score: <0-100 fit score>
why_survived: <one-line reason this passed screening>
---
```

**Body:** full job description as scraped — responsibilities, requirements, etc.

**Dedupe:** before writing, check both `raw/jobs/` and `raw/jobs/done/` for duplicates. A job that was archived is still a duplicate.

**Archive:** jobs older than 20 days, or with a final outcome (rejected, expired, signed), move to `raw/jobs/done/`. Content unchanged; location changes.

## Resume rules

- Tailor from `resume-master.md` only. Never add experience that is not recorded there.
- One resume per application, named `<company>-<role>-<date>`.
- A tailored resume never overwrites the master.

## Localization

Job sources are region-specific. During setup, the user provides their region and preferences. Skills read this from `resume-master.md` and adapt source lists accordingly. Default suggestions exist for US, EU, APAC; users may supply custom sources.

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
