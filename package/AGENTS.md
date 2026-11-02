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
resume-public-general.md  the untailored resume for job boards and profiles
BOOTSTRAP.md           setup interview (transient; gone when setup completes)
BLUEPRINT.md           the build manual — skill specs and script pseudocode (permanent; skills and scripts are authored from it, never shipped)
index.md               root index — pipeline and master data
log.md                 append-only audit of operations on this vault
tasks.md               shared queue
raw/jobs/              job listings as markdown, passed screening
raw/jobs/done/         archived jobs: stale (>20 days), rejected, expired
raw/assets/            resume and cover-letter themes, screenshots, source documents
raw/assets/resume-themes/        one stylesheet per resume theme
raw/assets/cover-letter-themes/  the same theme names, for letters
workflow/shortlisted/  interested, ready to apply — working bundle created here
workflow/processing/   application sent, awaiting response
workflow/interviews/   interview scheduled — prep, research, follow-ups
```

## Hard rules

1. **`raw/jobs/` content is write-once.** A job listing is never edited after it lands. Location may move to `raw/jobs/done/` for archival; content stays unchanged.
2. **`resume-master.md` is the source of truth.** Never invent experience, dates, or numbers. If it is not there, ask.
3. **`workflow/` is the pipeline.** A job exists in exactly one stage at a time. The working **bundle** moves forward as a folder — job document, resume, letter, PDFs and all — never split, never copied.
4. **Every write ends in one log line** in `log.md`, naming the stage or file it touched.
5. **Archive, never delete.** Stale, rejected, or expired jobs move from `raw/jobs/` to `raw/jobs/done/`. `raw/jobs/` is never pruned.
6. **Cite the listing.** A claim about a role names its file in `raw/jobs/` (or `raw/jobs/done/` if archived).
7. **Say when you don't know.** A documented gap is useful; an invented bridge gets someone rejected.
8. **This is not a general vault.** No personal notes, projects, or unrelated knowledge. It confuses agents and risks leaking private data.
9. **Never apply, never contact.** No submission, no form-filling, no email and no message to a recruiter or employer on the user's behalf. The agent prepares the package and hands it over; applying is the user's job. `processing/` means the user applied and said so — nothing moves there on an assumption.
10. **Vault-rendered PDFs are outputs, never inputs.** Provenance decides, not format: the markdown is the document; the PDF is a rendering of it. An agent may verify a PDF structurally — exists, non-zero, page count, theme resolved, text layer present — and never reads, quotes, summarises or attaches its contents. If the markdown and the PDF disagree, the PDF is stale and is regenerated. A PDF the user supplies — a resume shared at `BOOTSTRAP.md` §0, a listing pasted as PDF — is source material, not a rendering: read it once, extract it into markdown, and from then on this rule applies to any PDF rendered from that markdown. The source PDF is never an input again.

## Workflow stages

A job enters the pipeline when the user creates a working document in `workflow/shortlisted/`. That document moves through stages, accumulating history. It never spawns children; it moves.

- `shortlisted/` — interested, ready to apply. Working doc created here, links to `raw/jobs/<job>.md`, initial notes.
- `processing/` — application sent. Same doc moves here, adds application date, resume link, recruiter contact.
- `interviews/` — interview scheduled. Same doc moves here, adds prep, company research, questions, follow-ups, outcomes.

When the journey ends (signed, rejected, expired), archive the `raw/jobs/` entry to `raw/jobs/done/` and update the working doc with the outcome.

**A bundle, not a document.** The stage folder holds the job document and everything generated for it, flat — no subfolders. Every artifact is named from the job document's stem, so the group is obvious without a filing system:

```
acme-senior-platform-engineer.md
acme-senior-platform-engineer-resume.md
acme-senior-platform-engineer-resume.pdf
acme-senior-platform-engineer-cover-letter.md
acme-senior-platform-engineer-cover-letter.pdf
```

The PDFs are the one exception: they carry the candidate's name and the employer, because a recruiter sees the filename in an inbox — `Name-Resume-Company.pdf`. Everything vault-facing uses the stem.

**The bundle moves when the user says the application was sent.** Not when the resume is drafted, not when the letter is written. A job sits in `shortlisted/` with its whole package beside it until the user confirms. `processing/` is a claim about the world, and only the user can make it.

**One job, one journey.** The bundle is the pipeline state.

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
- One tailored resume per job, generated into that job's stage folder and named from the job document's stem.
- A resume is always a pair: the markdown is canonical, the PDF is rendered from it. Revisions edit the markdown and re-render; a PDF is never edited by hand.
- `resume-public-general.md` at the vault root is the untailored version, for job boards and profiles.
- A tailored resume never overwrites the master.
- A cover letter is generated from the tailored resume, not from the master file, and renders in the resume's theme.

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
