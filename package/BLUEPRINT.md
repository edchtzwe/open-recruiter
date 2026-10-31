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

1. Open the section. Read the script pseudocode and the skill spec.
2. Create the skill folder and a `SKILL.md` stub in your runtime's own skill conventions. If your runtime has no skill system, keep the written spec as a vault page instead — this is decided at setup, not invented later.
3. Write the script(s) from the pseudocode. The script gets live-page capability **only if the runtime actually has it**; otherwise it must degrade the way the pseudocode says, not fail.
4. Run each script's acceptance line once. Fix until it passes.
5. Return to the stub and fill out `SKILL.md` per the skill spec. The circuit is complete when the skill knows how to run its script and how to behave without the capability.

Order matters: the script comes first. A skill without its script is useless.

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
