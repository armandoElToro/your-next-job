# Setup Interview

Run this the first time the skill is used in a project (no `job-search-config.md`
exists yet at the project root), or any time the user asks to "set up," "configure,"
or "reconfigure" this toolkit. Do not run the screening or resume-building stages
against the template's example values — always interview a new user first.

## How to run it

1. Tell the user in one or two sentences what's about to happen: a few quick
   questions to configure their own search criteria, so every future lead gets
   screened against their real answers instead of an example.
2. Ask the questions below in small batches (use `AskUserQuestion` for the
   ones with clear, limited choices; ask the free-text ones — titles, pay
   numbers, industries — as plain conversational questions, a few at a time,
   not all sixteen at once).
3. If the user skips a question or says "use the default," carry over the
   matching value from `references/job-search-config.template.md` rather than
   leaving the field blank, and say plainly that you did so.
4. Once all questions are answered, write the results to a new file named
   `job-search-config.md` **at the root of the current project** (not inside
   the plugin's own folders), using the same table structure as the template
   file, so every stage of the skill can read it consistently.
5. Confirm back to the user in a short table what got saved, and mention they
   can say "reconfigure my job search toolkit" any time to run this again.

## Questions to ask

**Batch 1 — role and location**
- What job title or titles are you actually looking for? Include any direct
  synonyms you'd also accept (e.g., "Scrum Master" and "Agile Program
  Manager" as an either/or).
- Are there any titles you want to automatically rule out, even if the pay
  and location look fine?
- What city or cities (or metro areas) will you work onsite or hybrid in?
- Would you take a fully-remote role? If yes, anywhere in the country, or
  only certain states/time zones?

**Batch 2 — pay and contract terms**
- What's the lowest hourly rate you'd accept for W2 or C2C contract work?
- What's the lowest full-time salary you'd accept?
- Is there a specific location where you'd need a higher rate to make it
  worth it (e.g., a costly city requiring relocation or a second household)?
  If so, what's that floor?
- For contract roles, what's the shortest length you'd take, and does it
  need a stated extension or conversion path?

**Batch 3 — fit and constraints**
- What industries do you want to work in, or specifically want to avoid?
- Do you hold — or have you ever held — any clearance, license, or work
  authorization status that matters for the roles you're targeting? Is it
  currently active?
- Is there any kind of bias (age, employment-gap, career-change, or
  otherwise) you want Claude to actively coach around in interview
  materials? If so, describe it in your own words — do not guess this on
  your own.

**Batch 4 — documents and logistics**
- Where does your real, current resume/work-history information live (a
  file, a document, a project you'll point Claude to)? Claude should never
  invent details — only use what's documented there.
- Does your resume need to follow a specific template, font, or format? If
  none is specified, a clean 2-3 page format is used by default.
- For other documents (cover letters, prep packets), do you have a preferred
  file format, font size, margins, or color scheme? If none is given, a
  plain, readable default is used.
- Any standing corrections Claude should always apply without being asked
  (an exact certification name, a role you want described a specific way,
  contact details to always include)?

**Batch 5 — optional prompt discipline**
- Do you want Claude to score your own AI prompts before running long or
  important ones? If yes, at what length should that kick in, and where
  should scored prompts be logged? If no, this stage is skipped entirely.

## Output format — `job-search-config.md`

Write the collected answers into a new file at the project root, using this
exact structure so the rest of the skill can read it:

```markdown
# Job Search Configuration

status: configured

| Variable | Your value |
|---|---|
| Target titles | ... |
| Titles that are automatically a no | ... |
| Onsite/hybrid locations accepted | ... |
| Remote accepted? | ... |
| Pay floor — W2 or C2C contract | ... |
| Pay floor — FTE | ... |
| Pay floor — premium onsite market (optional) | ... or "not applicable" |
| Minimum contract length | ... |
| Industries preferred | ... |
| Clearance/authorization notes | ... or "not applicable" |
| Baseline resume data source | ... |
| Resume document format | ... |
| Other document format (cover letters, prep packets) | ... |
| Standing resume/cover-letter corrections | ... |
| Age-related bias coaching (optional) | ... or "not applicable" |
| Prompt-discipline threshold (optional) | ... or "not applicable — skip Stage 4" |
```

Leave any "(optional)" row as "not applicable" rather than guessing when the
user has no answer for it — Stage 2 and Stage 4 of the main workflow already
know to skip the related behavior when a row says "not applicable."
