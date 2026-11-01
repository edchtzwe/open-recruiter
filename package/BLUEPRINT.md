# BLUEPRINT.md — the build manual

Nothing in open-recruiter ships as a skill or a script. This vault ships **this file**. Your agent writes its own skills and scripts, in its own runtime's conventions, from the pseudocode below — and rebuilds them from here if any are ever lost.

**Permanence.** `BLUEPRINT.md` outlives `BOOTSTRAP.md`. During setup, BOOTSTRAP drives the circuit below. After setup, this file is the rebuild reference: a script broken or missing → reopen its section, follow it, re-run its acceptance line.

## Rules that keep this portable

- **Pseudocode is behaviour, not technique.** Each section states what to observe, decide, and emit — never what language, framework, library, or tool to use. Your agent chooses its own means.
- **Capability is discovered, never prescribed.** No browsing or automation product is named or hinted at anywhere in this file. Whether the runtime can drive live pages is a category-level question, asked once in setup and re-checked by each skill at run time.
- **No personal data.** Region, target roles, search terms, comp floor, and source lists come from `resume-master.md` at run time. Nothing here hardcodes any person's market.
- **Sections are appended, never rewritten.** One section per skill and its scripts. New skills add sections; existing ones stay valid.

## The circuit — one skill per pass

For each skill section in this file, in order:

1. Open the section. Read the skill spec, and the script pseudocode if the section ships any.
2. Create the skill folder and a `SKILL.md` stub in your runtime's own skill conventions. If your runtime has no skill system, keep the written spec as a vault page instead — this is decided at setup, not invented later.
3. If the section ships script pseudocode, write the script(s) from it. The script gets live-page capability **only if the runtime actually has it**; otherwise it must degrade the way the pseudocode says, not fail. Skip this step for a skill-only section.
4. If the section has scripts, run each script's acceptance line once. Fix until it passes. A skill-only section ships behavioural assertions instead — they are checked against the finished skill, not executed.
5. Return to the stub and fill out `SKILL.md` per the skill spec. A section with scripts is complete when the skill knows how to run its script and how to behave without the capability. A skill-only section is complete when every behavioural assertion holds.

Scripts exist where the work is mechanical — loops, matching, deterministic transformation. A skill whose work is judgement and conversation needs none, and its section says so. Where a section does ship scripts, the script comes first: a skill whose script is missing is useless.

---

## Section 1 — job-search skill

**Delivers:** one skill (a fresh discovery run) plus two scripts — the site-discovery script and the dedupe script.

### Script 1 — site discovery (pseudocode)

Inputs, all resolved at run time:

- **Search terms** — from `resume-master.md`, the recorded search targets.
- **Source list** — user-supplied custom sources if recorded; otherwise the agent's own current knowledge of job boards relevant to the user's recorded region. Group the sources into three site classes.

**Site-class order** (a run never returns to an earlier class once it has advanced past it):

1. **Public direct boards and employer career pages** — plainly reachable listings.
2. **Aggregator searches** — broad listings across many employers.
3. **Bot-protected boards** — the class that requires live-page capability. If the runtime does not have it, report the class UNAVAILABLE and do not attempt it.

The loop:

1. For each site class in order, for each source in the class: query per search term. **Up to 3 runs per source**, then advance to the next source regardless of yield.
2. For every listing encountered, capture: company, role title, location, stated compensation if any, canonical URL, source name, and a stable listing id when the page carries one. **Every field comes from the page itself.** A field the page does not show is recorded as absent — never guessed, never filled from memory or from secondary snippets.
3. Record one status per source: OK, EMPTY, or BLOCKED. An unreadable or blocked page reports BLOCKED and moves on. Never defeat a login wall, paywall, or access restriction to read a listing.
4. Stop when **N in-scope candidates** (default: 10) have been collected or every source's budget is spent. In-scope means the skill's eligibility filter is applied at capture time; out-of-scope listings do not count toward N.
5. Emit one structured record per candidate with its captured fields and absences marked as such, plus the per-source status table and run totals. Never pad to reach N. Never invent facts.

**Acceptance line:** run once against a live listing — every field present or explicitly absent; a blocked source is reported and skipped, not fatal; output is structured records, not prose.

### Script 2 — dedupe (pseudocode)

Input: candidate identities from Script 1. Identity = source name, listing id or canonical URL, normalised company, normalised title.

1. Check every candidate against every job file under `raw/jobs/` **and** `raw/jobs/done/`. An archived job is still a duplicate.
2. Report each candidate NEW or DUPLICATE, with a pointer to the existing file for duplicates.
3. A repost of the same identity is a duplicate. Existing job files are never rewritten — job content is write-once.
4. The script is read-only: it writes nothing and logs nothing. The skill decides what lands.

**Acceptance line:** run against one candidate copied from an existing `raw/jobs/` file and one fabricated, never-seen candidate — the first reports DUPLICATE, the second NEW, and no file on disk is changed.

### Skill spec — what the written job-search skill must do

- **Discovery only.** No applications, no recruiter contact, without explicit user approval per action.
- **Eligibility filter first, at run time.** Read the target role family, level terms, comp floor, and location preference from `resume-master.md`. Admit only listings the recorded targets admit; exclude titles outside the target families and levels the user did not ask for. When in doubt, exclude and say why.
- **Capability check before each run.** The skill's first act is checking whether this runtime can browse live pages with its own native tooling. If it cannot, say so plainly — the bot-protected site class is unavailable — and continue degraded on the accessible classes instead of failing. The degraded wording lives here because BOOTSTRAP is gone after setup.
- **Run discovery via Script 1**, dedupe via Script 2. The skill orchestrates; it never re-derives their logic conversationally.
- **Write each new qualifying listing once** into `raw/jobs/`, one markdown file per job with the full frontmatter and body `AGENTS.md` requires: url is mandatory, recruiter optional, score evidence-backed from `resume-master.md` or explicitly unknown, `why_survived` one line naming why it passed the filter. Filename: `<company>--<role>--<source>--<source-key>.md`, lowercase, non-alphanumerics to `-`, components capped at 80 characters; source-key is the page's stable listing id, or a short deterministic hash of the canonical URL when none exists.
- **Every write ends in one log line** in `log.md` naming `raw/jobs/` and the file.
- **Report at run end:** classes, sources and per-source statuses; raw candidates cast; new records written; duplicates skipped; blocked or unavailable sources; shortfall against N. Then suggest the obvious next step — rank the new finds, adjust the search targets, or run again.
- **Never pad, never guess, never invent.** A short honest run beats a full fabricated one.
---

## Section 2 — rank-jobs skill

**Delivers:** one skill. **No scripts** — ranking is judgement work: reading each listing against the user's profile is comprehension, not computation, and a script would only multiply numbers the skill itself produced. This section carries behavioural assertions instead of an acceptance line.

### Skill spec — what the written rank-jobs skill must do

- **Reads** every job file under `raw/jobs/` and the user's recorded profile from `resume-master.md`. Nothing about the user is assumed; every target, floor and preference comes from the profile at run time.

**Hard filters — four axes, values read from `resume-master.md`:**

Every axis is a dimension, not a default answer. The skill filters against what the user recorded. A user with nothing recorded on an axis is asked — never assumed.

1. **Role type.** The employment types the user admits — permanent, contract, fixed-term, or any mix. Listings outside the recorded set are cut. "Permanent only" is one possible answer, not the rule.
2. **Location and work mode.** The user's recorded locations, acceptable commutes, and modes (onsite / hybrid / remote). Listings outside are cut.
3. **Level.** The seniority band the user is pursuing. Clearly-below-band roles are cut. Clearly-above-band roles are kept and flagged, not cut — the user decides what is out of reach.
4. **Compensation floor.** The user's recorded floor. A listing that *states* comp below the floor is cut. A listing that *does not state* comp is never cut on comp — see flags below.

**Composite score — default weights, tunable with the user:**

Hard filters decide membership; the composite orders what survived.

- Stack and skill alignment — **40%**
- Scope, autonomy and level signal — **25%**
- Compensation and role type — **20%**
- Location and work mode — **15%**

These four dimensions at these weights are the default rubric — chosen because they track what predicts a good move for most individual-contributor engineers. They are defaults, not doctrine: the user may reweight or replace dimensions, and the skill scores by whatever rubric the user records. Every sub-score is evidence-backed from the listing text or the profile, or explicitly marked unknown — never invented to make the arithmetic work.

**Flags, not cuts:**

A listing that survives the hard filters is never cut for a soft misalignment. Stack mismatch, undisclosed compensation, an employer archetype the user prefers to avoid — each is a flag carried on the listing and shown alongside its score. The cut list holds hard-filter failures only. The user decides what soft risk to accept.

**Output:**

1. **Shortlist.** For each surviving listing, a working document in `workflow/shortlisted/` — created there per the vault contract — linking to its file in `raw/jobs/`, carrying the composite score, the sub-score evidence, and the flags. The listing file itself is never edited by this skill; `raw/jobs/` is write-once.
2. **The brief.** One executive brief for the top-ranked listing: company and role; compensation quoted verbatim from the listing text — never an inferred, rounded, or reconstructed bound; if the listing states no comp, say so plainly. Then why it ranks first today, two or three alignment points matching the user's recorded achievements against the role's stated needs, the risks, and the listing link.
3. **Next step.** Suggest the obvious move — draft the tailored resume for the top pick, adjust the rubric or search targets, or re-run discovery if the shortlist is thin. Suggest only; the user acts.

**Every write ends in one log line** naming the file touched.

**Never invent, never pad.** A ranked three beats a fabricated ten.

### Behavioural assertions — rank-jobs

Checked against the finished skill. Nothing here executes at build time.

1. Two users with different recorded role-type preferences produce different cut decisions on the same contract listing — cut for the permanent-only user, kept for the user whose record admits contract.
2. A listing stating comp below the user's recorded floor is cut. The same listing against a user with no recorded floor is kept (or the user is asked) — never silently cut on an assumed floor.
3. A listing with no stated comp survives to the shortlist, flagged "comp not stated". No bound is inferred anywhere in the pipeline.
4. Identical inputs rank identically across two runs.
5. The brief's compensation line matches the listing text exactly, or states that comp is not disclosed.
6. A stack-mismatched listing that passes the hard filters appears on the shortlist with its flag and a score — it never appears in the cut list.

---

## Section 3 — write-resume skill

**Delivers:** one skill plus one script. Choosing and phrasing bullets against a single listing is judgement work, so the skill writes the document. Producing the PDF is mechanical, so a script does that. They ship together: a resume nobody can open is not a resume.

**Every resume is a pair** — `<stem>-resume.md` and its PDF, in the job's stage folder. The markdown is canonical. Revisions edit the markdown and re-render; the PDF is never edited by hand, never patched, never read.

### Script — document render (pseudocode)

Inputs: source markdown path, theme name, output path, document kind (resume or cover letter).

1. **Read** the source markdown. Empty input is a hard failure.
2. **Convert** to HTML with the runtime's own markdown converter. If it has none, write a minimal one covering headings `h1`–`h4`, paragraphs, bold, italics, links, unordered lists and horizontal rules — those are the only constructs these documents use. Anything else is emitted as plain text rather than silently dropped.
3. **Resolve the theme.** Resumes: `raw/assets/resume-themes/<theme>.css`. Cover letters: `raw/assets/cover-letter-themes/<theme>.css`. A theme that does not resolve is a **hard failure**: exit non-zero, name the theme, name both folders searched. **Never fall back to a default.** A silently restyled document is worse than no document, because nobody notices it happened.
4. **Assemble one self-contained HTML document.** Stylesheet inlined in a `<style>` element. No external requests of any kind — no remote fonts, no remote CSS, no remote images. It must render identically with the network off.
5. **Render to PDF.** A4. Backgrounds printed. No header or footer furniture. Page margins come from the stylesheet's `@page` rule, not from the renderer's defaults.
6. **No PDF path in this runtime?** Write the styled HTML beside the source, say so plainly, and tell the user to print it to PDF. Expected outcome, not an error.
7. **Emit** the output path, the theme used, the page count, and whether an extractable text layer is present.

**Verification is structural, never textual.** Existence, non-zero size, page count, theme resolved, text layer present. The script never opens, parses, quotes or prints the document's contents.

**Acceptance line:** render a two-page sample with the sample theme — output exists, is non-zero, is two pages, the theme resolves, the text layer is extractable. Then render once with a theme name that does not exist — the script exits non-zero and names both folders it searched. No step in either run reads the PDF's contents.

### Theme contract

A theme is a print stylesheet and nothing else. The vault ships one sample; the user builds more with their agent. Themes work only because the renderer's HTML is fixed, so that structure is part of the contract:

- `h1` — the name line: the candidate's name on a resume, the letterhead on a letter.
- `h1 + p` — the contact line directly beneath it.
- `h1 + p + p` — the target line on a cover letter ("Application for <role> — <company>").
- `h2` — a section heading.
- `h3` — employer and role, or a dated entry line.
- `h4` — a secondary line: project name, sub-heading.
- `p > em` — a meta line: dates, location, one row of context under a heading.
- `ul` / `li` — bullets.
- `hr` — a section rule, removable for space.
- `a`, `strong` — links and emphasis.

A theme must: target A4 print; use system font stacks only, never a webfont; contain no script and no external reference; and stay legible in monochrome, because printing and applicant tracking both strip colour.

### Skill spec — what the written write-resume skill must do

- **Reads** `resume-master.md`. In tailored mode it also reads the job document in the stage folder and the listing it links to in `raw/jobs/`. Nothing about the user is assumed and nothing is invented — no employer, title, date, metric or technology that the master file does not record.
- **Two modes.** Tailored: one listing, one application, bullets chosen and ordered for that listing. Public general: `resume-public-general.md` at the vault root, for job boards and a profile, written once and re-rendered when the master changes.
- **Document shape.** Name and contact header; a title and short summary; four or five core strengths; experience in reverse chronological order with three to five evidence bullets each; two or three selected projects; a final line for citizenship, location and work mode. No standalone skills, tools or technology list — capability is shown through the work, never asserted through a keyword row.
- **Bullets.** Outcome first, then the measure, then the mechanism. A bullet that cannot state an outcome or a measure is rewritten or dropped; no task-board phrasing, no invented number.
- **Two pages.** The render reports the page count. Over-length is fixed by compressing repeated claims and redundant rules first, and by cutting evidence last. The skill never edits the PDF to fit.
- **Writes both files into the job's stage folder**, named from the job document's stem: `<stem>-resume.md`, and a recipient-facing `<candidate name>-Resume-<company>.pdf`.
- **Verifies structurally**, exactly as the script does: exists, non-zero, page count, theme, text layer. It reports the markdown path and the page count — it never reads the PDF back.
- **Every write ends in one log line** in `log.md` naming the stage folder and the files.
- **Reports** both paths, the page count, the theme, and anything that looks thin, repeated or unaligned with the listing.
- **Never submits anything.** Preparing a document is the end of this skill's authority.

### Behavioural assertions — write-resume

Checked against the finished skill. Nothing here executes at build time.

1. Every experience bullet in a generated resume traces to a fact recorded in `resume-master.md` — employer, title, dates, metric. A fact that is absent is absent from the output, not softened into a claim.
2. A resume that would run three pages is compressed before evidence is cut, and the reported page count comes from the render, not from an estimate.
3. Revising a resume rewrites its markdown and its PDF together; no second PDF appears and no orphaned PDF survives from the previous revision.
4. Rendering with a theme name that does not exist fails visibly and is reported as a failure — never as success with a fallback style.
5. The skill's report cites the markdown path and the page count. It never quotes, summarises or attaches the PDF.
6. A tailored resume shares its job document's stem; its PDF is named for the recipient, not for the vault.

---

## Section 4 — write-cover-letter skill

**Delivers:** one skill. **No scripts** — it reuses Section 3's render script unchanged, which is why the themes come in pairs.

### Skill spec — what the written write-cover-letter skill must do

- **Reads the tailored resume, not the master file.** Inputs: `<stem>-resume.md`, the job document, and the listing it links to. The letter is a projection of the resume — a shorter, targeted restatement of what the resume already proves. If the letter needs a fact the resume does not carry, stop and surface the gap; the resume is revised first, then the letter.
- **One page.** A letter that needs two is a letter that has not been cut.
- **Shape.** The same header as the resume, name and contact; a target line naming the role and company; a salutation; an opening that gives one specific reason for this application; two or three body paragraphs, each anchored to an achievement already in the resume; a close naming the next step; a signature.
- **Same theme name as the resume.** A package that renders in two different styles looks assembled from two different people. The render script resolves the cover-letter variant of the name.
- **Writes** `<stem>-cover-letter.md` and `<candidate name>-Cover-Letter-<company>.pdf` into the same stage folder.
- **Never invents.** No metric, employer, date or claim that is not in the resume. No flattery that outruns the evidence.
- **Verifies structurally** and logs one line, exactly as in Section 3.
- **Never submits, never contacts.** The package is handed to the user.

### Behavioural assertions — cover-letter

1. Every claim in a generated letter appears in the resume it was generated from. Where the letter wanted a fact the resume lacked, the run stopped and said so.
2. The letter renders in the theme named by the resume — never the default, never a different one.
3. A letter that would exceed one page is cut, not shrunk by editing the PDF.
4. The letter's header matches the resume's header exactly: same name line, same contact line.
5. The skill's report cites the markdown path and the page count. It never reads the PDF.
