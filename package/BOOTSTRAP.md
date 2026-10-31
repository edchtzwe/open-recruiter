# BOOTSTRAP.md — setup interview

**This file is the setup gate.** While it exists the vault is in setup mode: no job-search work happens. It shrinks as you go and deletes itself when setup is complete.

## How this works

Each section below is one piece of setup. Start with the resume question, then work through the remaining sections in order, one at a time, whether or not a resume was provided.

For every section:

1. Check `resume-master.md` for information already present. A partially populated section is still incomplete; its presence does not mean it is done.
2. Confirm resume-derived information with the user rather than assuming it is current or correct. Ask about missing or unclear information and run the section's questions, analysis and suggestions as applicable. Do not ask again for facts the user has already confirmed.
3. The user makes the decisions required by the section. Do not invent facts; record an explicit unknown or user decision where appropriate.
4. Write the complete, decided output to `resume-master.md`.
5. Only when the section is fully satisfied and its output is written, **delete that whole section from this file**. Otherwise leave it in place and continue filling its gaps.
6. Move to the next section.

On resuming interrupted setup, start at the first remaining section and inspect `resume-master.md` before asking anything. Record confirmations and decisions there as they happen so they survive interruptions. A remaining section is not complete merely because some or all of its output exists. A deleted section is complete; do not repeat it.

When the last section is complete, delete this file.

---

## 0. Resume seed

Ask whether the user has a resume they can share. If they do, read it and seed `resume-master.md` with the information it contains, organised under the relevant setup headings. Preserve useful career details for later tailoring. Mark extracted information as unconfirmed until the user verifies it; a resume may be incomplete, outdated or wrong. Do not invent missing facts.

If they do not have a resume, start `resume-master.md` from scratch and continue with the same sections below. If a provided resume cannot be read, explain the issue and ask for a readable version or to continue without it.

Record the user's choice in `resume-master.md` so an interruption does not cause the question to be repeated. Once the user has chosen a path and any resume seed has been written, delete this section. Then continue to Identity. Seeding does not complete or delete any later section.

---

## 1. Identity

Ask, one question at a time, conversationally:

- Full name (for resumes).
- City and timezone.
- Work rights (citizen, permanent resident, visa type, or "need sponsorship").
- Contact: email, phone, LinkedIn, portfolio/GitHub.

Write to `resume-master.md` under `## Identity`. Then delete this section.

---

## 2. Target role and career highlights

Ask:

- What role are you targeting? In your own words.
- What are your 3–5 strongest career highlights? Push for scope, numbers, outcomes. "Led the migration" is not a highlight; "led a 6-person migration of 40 services with zero downtime" is.

Do not accept vagueness. Do not invent. If they cannot name outcomes, ask what changed because of their work.

Write to `resume-master.md` under `## Target` and `## Career highlights`. Then delete this section.

---

## 3. Role synthesis

Crunch the target role and highlights. Propose:

- Adjacent roles they are plausibly qualified for but did not name.
- Concrete search terms to run.
- Where the money is, if you can tell.

One line of reasoning per suggestion.

The user decides: accept, adjust, or reject. Record **only the decision** in `resume-master.md` under `## Search targets` — the roles and terms actually being pursued.

Delete this section either way. If they chose badly, they were warned. Do not argue, do not revisit.

---

## 4. Market reality check

Be honest. Compare their target, seniority, highlights, and location against the market as you actually understand it.

If the target is unrealistic — underskilled, too senior, wrong market, comp far above evidence — say so plainly and name the options:

- **Lower the comp target.**
- **Drop the seniority.**
- **Change the target.**
- **Exit the industry.**

Say it once, clearly, with the reasoning. Do not soften it into meaninglessness. Do not moralise.

The user decides. Record the decision — target band, seniority, comp range — in `resume-master.md` under `## Position`. If they insist on a course you believe is futile, record it and move on. It is their career.

Delete this section. Do not revisit.

---

## 5. Job preferences and ranking

Ask, one question at a time, and record the user's decisions in `resume-master.md` under `## Job preferences`:

- Accepted employment types (for example, permanent, contract or fixed-term, in any combination).
- Acceptable locations, commute constraints and work modes (onsite, hybrid and/or remote).
- Compensation floor, including currency and pay period, or an explicit decision that there is no floor. Confirm how it relates to any range already recorded under `## Position`.

Confirm the seniority band already recorded under `## Position`; resolve any uncertainty with the user. Do not assume preferences from the resume, location or market.

Explain the default ranking rubric in `BLUEPRINT.md` Section 2: stack and skill alignment 40%, scope and level 25%, compensation and role type 20%, and location and work mode 15%. Ask whether to use it or change its dimensions or weights. Record the chosen rubric and weights under `## Job preferences`; if changed, confirm the weights total 100%. The user may revise it later.

Only delete this section when the decisions are recorded. If a preference remains unknown, record that explicitly and leave this section in place until the user resolves it; do not invent an answer.

---

## 6. Skill authoring circuit

The vault ships no skills and no scripts. Every skill this system needs is authored here, by you, in your own runtime's conventions. `BLUEPRINT.md` holds the specs and script pseudocode — one section per skill. Nothing in it is copied; everything in it is built from.

First, one question, category-level only: **can this agent browse live pages / drive a browser?** Never name a tool, never suggest one, never point at where to find one — that research is the user's. Record the answer in `resume-master.md` under `## Capabilities`.

Then run the circuit once for every skill section in `BLUEPRINT.md`:

1. Open the section. Read the script pseudocode and the skill spec.
2. Create the skill folder wherever this runtime actually loads skills from — find it, do not invent one — and stub the `SKILL.md` in this runtime's own convention.
3. Write the script(s) from the pseudocode, in whatever language and tooling are native here. If a capability a section needs is absent, build the degraded path the pseudocode describes and say so plainly in the skill.
4. Run each script's acceptance line once. In degraded mode, run it against whatever listing is reachable and record what could not be exercised.
5. Return to the stubbed `SKILL.md` and fill it out from the section's spec. That skill's circuit is complete: the script exists and the skill knows how to run it.

Record where each finished skill lives in the `## Capabilities` section as you go, so interrupted authoring resumes without rebuilding what already exists. Author every skill before finalising; `BLUEPRINT.md` survives this file's deletion and is the rebuild reference later.

Write the `Capabilities` record, complete every `BLUEPRINT.md` section, then delete this section.

---

## 7. Finalise

- Read `resume-master.md` back. It must have: Identity, Target, Career highlights, Search targets, Position, Job preferences, Capabilities. Check that the decisions are recorded and resume-derived details used in setup have been confirmed. Verify accepted employment types, locations and commute constraints, work modes, seniority band, compensation floor or explicit no-floor decision, and the chosen ranking rubric and weights.
- If a required section is still present in this file, complete it before finalising. If required output is missing or unconfirmed but its section has already been deleted, stop and resolve the gap with the user before deleting this file; do not silently treat it as complete.
- Confirm every `BLUEPRINT.md` skill section has a completed skill: stub filled, scripts written, acceptance lines run. `BLUEPRINT.md` is permanent and stays in the vault.
- Confirm the vault has nothing else in it beyond the known structure.

Only when every section is complete and its output is recorded, delete this file.

---

## Rules

- One section at a time. Do not batch.
- Delete a section only after it is fully satisfied and its output is written. A partly written section is incomplete.
- Never offer to skip setup. Do not ask again for an answer the user has already confirmed.
- Resume-derived information is unconfirmed until the user verifies it. Do not infer missing details.
- Never write a tailored resume here. Setup builds the master only.
- Do not add sections at runtime. If a section is missing, it is done, subject to the finalisation check.
