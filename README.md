# Your Next Job

An end-to-end job search workflow for anyone working toward their next role
with Claude's help. It screens every new lead before any resume or cover
letter work starts, tailors application materials honestly, tracks every
application in one place, and (optionally) checks long AI prompts before you
run them.

**New here? Start with `README_FIRST.md`**, not this file — it's a two-minute
orientation on why this exists and what to read before your first real
request.

## Components

| Component | Purpose |
|---|---|
| Skill: `job-search-workflow` | The full workflow — screen a lead, score it, build tailored materials, track the application, follow up. Triggers automatically when you share a job posting, recruiter email, or ask Claude to score/track/tailor materials for a job lead. |
| `docs/Your_Next_Job_Overview.docx` | The full program overview — why the process works, background and key terms, the five-stage life cycle, and how-to steps. |
| `docs/Your_Next_Job_Cheat_Sheet.docx` | A quick-reference table of every variable the setup interview asks about, for anyone who wants to prep answers on paper first or hand-edit a saved answer later. |

No MCP servers, agents, or hooks are included — this is a single, self-contained skill plus two reference documents.

## Setup — Claude interviews you

The first time you use this skill in a project, Claude interviews you: your
target job titles, locations, pay floor, contract terms, industries,
clearance/authorization notes, where your real resume data lives, document
format preferences, and (optionally) age-bias coaching and prompt-scoring
settings. Your answers are saved to a `job-search-config.md` file at the
root of your project, and every stage of the skill reads from that file from
then on — nothing needs to be edited by hand.

To trigger the interview yourself at any time, just say "set up my job
search toolkit" or "reconfigure my job search settings." Claude will re-run
the interview and overwrite your saved answers.

The plugin also ships with one worked example (a real Release Train
Engineer / Scrum Master search) in
`skills/job-search-workflow/references/job-search-config.template.md` — this
is a demonstration only. Claude is instructed not to screen a real person's
opportunities against it; it always interviews a new user instead. If you'd
rather skip the interview and edit values directly (or hand someone the
questions ahead of time so they can think them through before answering),
see `docs/Your_Next_Job_Cheat_Sheet.docx` — it lists every variable, what it
controls, and where it lives.

## Usage

Once installed, just talk to Claude naturally:

- The first real request ("score this job for me," "help me apply to this")
  triggers the setup interview automatically if you haven't configured the
  toolkit yet.
- Paste a job posting or recruiter email and ask "should I apply to this?" or
  "score this opportunity" — Claude runs the Stage 1 screening against your
  saved configuration.
- Ask "build me a tailored resume and cover letter for this" once a lead
  clears screening.
- Ask "log this in my tracker" or "what's the status on [company]?" to work
  with your application tracker.
- Ask "score this prompt before I run it" for prompt-discipline checks on
  long or important AI requests (if you turned this stage on during setup).
- Ask "reconfigure my job search settings" any time your criteria change.

## Customization

This plugin does not use `~~` tool-category placeholders (no CONNECTORS.md
is included) — it doesn't depend on any specific external tool. Customization
happens through the setup interview described above, not through manual file
edits — though manual edits to `job-search-config.md` work too, if preferred.
