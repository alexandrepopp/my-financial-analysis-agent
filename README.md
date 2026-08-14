# My Financial Analysis Agent

A set of five for equity analysis. Four skills each handle one stage of analysis, and a fifth **orchestrator** skill chains them into a single end-to-end pipeline that produces a Word document research report.

## The skills

| Skill | What it does |
|---|---|
| `stock-research` | Full equity research report: thesis, fundamentals, valuation, catalysts/risks, position sizing |
| `transcript-stock-analysis` | Analyzes the most recent earnings call: numbers vs consensus, management narrative, dodged questions, bear case |
| `red-flag-stock-analysis` | Forensic-accountant review of the latest 10-K across six warning-sign areas |
| `dcf-analysis` | Three-scenario DCF (bear/base/bull) with explicit assumptions and sensitivity tables |
| `full-stock-analysis` | **Orchestrator.** Runs the four skills above in sequence on a ticker and compiles one Word document |

## How the pipeline works

Given a ticker, the orchestrator runs:

1. **stock-research** — broad fundamental and valuation context
2. **transcript-stock-analysis** — latest earnings call (fetched via web search)
3. **red-flag-stock-analysis** — latest 10-K, red flags numbered and severity-rated
4. **dcf-analysis** — valuation whose assumptions must explicitly address each red flag and any guidance changes from the call

Each stage writes its output to a working file and passes key findings forward via a shared handoff-notes file. The final deliverable is a single `.docx` report with an executive summary, all four sections, and a reconciled verdict.

## Installation

Install **all five skills** in Claude (Settings → Capabilities → Skills, or by uploading each `SKILL.md`). The orchestrator depends on the other four.

⚠️ **Keep the skill names unchanged.** The orchestrator references the other skills by their exact names (`stock-research`, `transcript-stock-analysis`, `red-flag-stock-analysis`, `dcf-analysis`). Renaming any of them breaks the pipeline.

## Usage

To run the full pipeline:

> "Run a full analysis on NVDA"

The orchestrator triggers on phrases like "full analysis", "complete analysis", "deep dive", or "run the pipeline" plus a ticker. Each of the four component skills can also be used on its own — just ask for that specific analysis.

Notes:
- The full pipeline is a long task (extensive web research plus a compiled report), so expect it to take a while.
- The DCF skill normally asks for the company's sector; when run through the orchestrator, the sector is determined automatically in stage 1.
- Works best in environments with web search and file creation enabled (Claude.ai with those features on, Claude Code, or Cowork).

## Disclaimer

These skills produce AI-generated analysis for research and educational purposes. Outputs can contain errors and are not investment advice. Verify all figures (the skills mark uncertain data points) and do your own due diligence.
