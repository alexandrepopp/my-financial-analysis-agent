---
name: full-stock-analysis
description: "Run the complete four-stage stock analysis pipeline on a ticker and deliver a single Word document report. Use this whenever the user asks for a 'full analysis', 'complete analysis', 'deep dive', 'full workup', or 'run the pipeline / run all my skills' on a stock or ticker, or asks to analyze a company end-to-end. This orchestrates four skills in sequence: stock-research, transcript-stock-analysis, red-flag-stock-analysis, and dcf-analysis, then compiles everything into one report. Do NOT use this if the user asks for only one of those analyses by itself."
---

# Full Stock Analysis Pipeline

You are orchestrating a four-stage equity analysis on a single ticker. Each stage is defined by an existing skill. Run them in the exact order below, save each stage's output to a working file, and carry findings forward so later stages are informed by earlier ones. The final deliverable is ONE Word document.

## Inputs

Required: a stock ticker. If the user has not provided one, ask for it before starting.

Do NOT ask the user for the sector (the dcf-analysis skill normally requires it) — determine the sector yourself during Stage 1 and pass it to Stage 4 automatically.

## Working files

Create a working directory `/home/claude/analysis-<TICKER>/` and save each stage's output as it completes:

- `01-research.md`
- `02-earnings-call.md`
- `03-red-flags.md`
- `04-dcf.md`
- `handoff-notes.md` — a running file of key facts each stage passes to the next (see below)

Saving intermediate files is mandatory: it keeps the pipeline coherent over a long session and lets you compile the final report without re-deriving anything.

## Pipeline

### Stage 1 — Equity research (skill: stock-research)

Read `/mnt/skills/user/stock-research/SKILL.md` and execute it for the ticker. Use web search to gather current data; cite sources and flag uncertain data points as that skill instructs.

After completing it, append to `handoff-notes.md`:
- Company sector and business model in one line (needed for Stage 4)
- Revenue growth history and consensus forward growth
- Margin trends (gross, operating, net)
- FCF trajectory and conversion
- Net debt, share count, current price
- The preliminary thesis and recommendation

### Stage 2 — Latest earnings call (skill: transcript-stock-analysis)

Read `/mnt/skills/user/transcript-stock-analysis/SKILL.md`. Identify the company's most recent reported quarter, then use web search / web fetch to obtain the earnings call transcript (or, failing a full transcript, detailed coverage of the call plus the press release). Note in the output which source you worked from. Execute the skill for that quarter.

Append to `handoff-notes.md`:
- Guidance changes (prior vs new)
- The 3 dodged/thin-answer topics
- Tone shift vs prior quarter
- Whether the call changes the Stage 1 thesis, and how
- Any numbers that revise Stage 1 figures (use the more recent ones from here on)

### Stage 3 — 10-K red flags (skill: red-flag-stock-analysis)

Read `/mnt/skills/user/red-flag-stock-analysis/SKILL.md`. Locate the company's most recent 10-K (or annual report for non-US filers) via web search — SEC EDGAR is the preferred source — and review it per the skill's six areas. If the full filing is too large to ingest, prioritize: revenue recognition and segment notes, MD&A, liquidity/debt notes, accounting policy changes, auditor's opinion, and risk factors.

Append to `handoff-notes.md` a numbered list of red flags (RF1, RF2, ...), each with:
- One-line description
- Severity: High / Medium / Low
- The DCF assumption it should affect (e.g., "RF2: rising DSO → haircut revenue growth or add working-capital drag in bear case")

If genuinely no red flags are found, record that explicitly — Stage 4 must still state it.

### Stage 4 — DCF valuation (skill: dcf-analysis)

Read `/mnt/skills/user/dcf-analysis/SKILL.md`. Use the sector identified in Stage 1 as the [sector] input — do not prompt the user for it. Execute the skill with all three scenarios and the sensitivity table.

**Red-flag linkage is mandatory.** The DCF must explicitly reference the red flags from Stage 3:
- Include a subsection titled "Red-flag adjustments" that goes through each RF# and states how it is reflected in the assumptions (typically in the bear case: lower growth, compressed margins, higher WACC, working-capital drag) — or states why it was judged not to warrant an adjustment.
- Never silently ignore a High-severity red flag.
- Also reconcile with Stage 2: if guidance changed on the call, year-1 assumptions must reflect the new guidance, and say so.

## Final report (Word document)

After all four stages, read `/mnt/skills/public/docx/SKILL.md` and produce a single professional Word document saved to `/mnt/user-data/outputs/<TICKER>-full-analysis.docx`. Present it to the user with the present_files tool.

Structure:

1. **Cover / title** — Company name, ticker, date, current price, recommendation, 12-month price target
2. **Executive summary** (max 1 page) — thesis in 3-4 sentences; recommendation; price target with upside; the 2-3 most important red flags; DCF verdict (cheap/fair/expensive); position action
3. **Equity research** — Stage 1 content (update any numbers superseded by Stage 2)
4. **Latest earnings call** — Stage 2 content
5. **Red flags** — Stage 3 content, ordered by severity
6. **Valuation (DCF)** — Stage 4 content, including the "Red-flag adjustments" subsection and sensitivity table
7. **Verdict and monitoring** — final reconciled recommendation; if it differs from the Stage 1 preliminary recommendation, explain why; the specific things to track over the next 90 days; entry/stop/target levels from Stage 1 (adjusted if later stages changed the picture)

Formatting: use heading styles, tables for numeric data (multiples, DCF scenarios, sensitivity), and keep prose tight. Cite sources for numerical claims. Mark any unverified figures as [VERIFY], consistent with the underlying skills.

## Rules

- Run stages strictly in order; do not skip a stage. If a stage cannot be completed (e.g., no transcript findable), say so in the report's corresponding section and continue — do not abort the pipeline.
- Later data wins: if Stage 2 or 3 reveals numbers that contradict Stage 1, use the newer/primary-source figure everywhere, including the final report.
- The final recommendation must be internally consistent: the executive summary, DCF verdict, and position action cannot contradict each other. Reconcile before writing the document.
- This is a long task. Do not pause to ask the user questions mid-pipeline unless the ticker itself is ambiguous.
