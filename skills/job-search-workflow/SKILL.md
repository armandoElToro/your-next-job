---
name: job-search-workflow
description: This skill should be used whenever the user shares a job posting, a recruiter email, or a new job lead and wants to know whether to pursue it, asks to "score this job," "is this worth my time," "am I a good fit for this," "build me a resume for this," "tailor my cover letter," "log this application," "update my tracker," wants help landing their next job or planning a career move, or says "set up my job search toolkit," "configure this for me," or "reconfigure my job search settings." Interviews a new user to capture their own search criteria on first use, then screens each opportunity — producing a downloadable Word (.docx) Skill & Experience Match gap-analysis report mapping the posting against the current resume — builds tailored application materials, tracks every application in one place, and can score long AI prompts before they run. Every generated file (resume, cover letter, match report) is delivered as .docx.
---

# Your Next Job — Job Search Workflow

Run every new job lead through this workflow, in order: confirm the user's
configuration exists, screen the lead, then (only if it clears screening)
build materials for it, then log it, then follow up. Do not skip straight to
building a resume for a lead that has not been screened, and do not screen
anything against the shipped example values — those exist only to prove the
workflow runs, not to be used for a real person's search.

Every generated file — resume, cover letter, the Skill & Experience Match
report, and any other supporting document — is delivered as **.docx**.
.docx is the one standard format across this workflow: it parses reliably
in ATS systems for the resume and cover letter, and stays easy for the user
to open, edit, or print for everything else.

## Stage 0 — Get or confirm the user's configuration

This skill is driven by a single configuration file, not by hardcoded
values. Every other stage below reads from it.

1. Look for a file named `job-search-config.md` at the root of the current
   project. This is the single source of truth for every stage below.
2. **If it doesn't exist yet**, this is a new user — follow
   `references/setup-interview.md` to interview them and create it, before
   doing anything else. Do not fall back to `references/job-search-config.template.md`'s
   example values for a real screening; that file is a demonstration
   default only, not a substitute for asking the actual user.
3. **If it exists**, read it and use its values for every stage below. If
   the user says something in conversation that contradicts a value already
   on file (a new pay floor, a widened location range, a title they've
   decided to drop), treat their most recent statement as authoritative,
   update `job-search-config.md` to match, and confirm the change back to
   them briefly.
4. **If the user explicitly asks to "set up," "configure," or "reconfigure"**
   this toolkit at any point, run the interview in
   `references/setup-interview.md` again and overwrite `job-search-config.md`
   with the new answers, even if a config file already exists.

Every reference below to "the configuration" means the current contents of
`job-search-config.md` (see the setup interview and template file for the
exact fields it holds: target titles, locations, pay floors, contract
length, industries, clearance notes, resume data source, document formats,
standing corrections, optional age-bias coaching, and the optional
prompt-discipline threshold).

Also confirm two other things exist before the first screening:

1. A real, current baseline resume/career-history document Claude can read
   every session (per the config's "Baseline resume data source" row).
   Never invent or round up a qualification that isn't documented there.
2. An application tracker file (see Stage 3) to log every lead going forward.

## Stage 1 — Screen the opportunity before doing anything else

For every new posting, recruiter email, or lead:

**Check for duplicates first.** Search the tracker for the same company,
requisition number, or an obvious repost before scoring. The same
requisition often reaches a candidate through more than one recruiter or
staffing agency at once — flag this to the user even if the score would
otherwise be a clear pass, since it's a relationship/channel-conflict risk
either way.

### Part A — Skill & Experience Match (the credibility check)

1. Pull every requirement out of the posting and sort into **Required** and
   **Preferred/Desired**.
2. Score each requirement against the user's real, documented background
   (never invent or round up): **Fully met = 1.0**, **Partially met = 0.5**,
   **Not met = 0**.
3. `Required Match % = (sum of required scores / number of required items) * 100`
4. `Preferred Match % = (sum of preferred scores / number of preferred items) * 100`
5. `Skill & Experience Match % = (Required Match % * 0.7) + (Preferred Match % * 0.3)`

| Match % | Label | Meaning |
|---|---|---|
| 90-100% | A-List | Strong, credible submission — proceed |
| 75-89% | B-List | Competitive but not a lock — name the specific gaps, let the user decide |
| Below 75% | Not competitive | Hard disqualifier — don't spend interview time here |

Report this number and label every time, before anything else: **"Skill &
Experience Match: XX% — [A-List / B-List / Not competitive]."**

### Skill & Experience Match report — Word document (build this every time Part A runs)

After computing the percentages above, generate a .docx titled "Skill &
Experience Match — [Company] / [Role]" containing:

- A header block: company, title, requisition/source, date screened, and
  the headline Skill & Experience Match % with its A-List/B-List/Not
  competitive label, set apart in bold/larger type.
- A two-column (or three-column, with a status column) table, one row per
  requirement pulled from the posting: one column is the exact requirement
  text from the JD, the next is how the resume meets it (quote the specific
  resume line or bullet used) or "Not documented" if it's a gap — never
  invent a resume line that doesn't exist. Required items first, then
  Preferred items, each group under its own sub-heading.
  - Mark each row's status visually — bold/green for fully met, italic/
    amber for partial, strikethrough/red or a "GAP" label for not met — so
    it's scannable at a glance, not just a wall of text.
- A summary block at the bottom: Required Match %, Preferred Match %,
  combined Skill & Experience Match %.
- Build it as a real .docx (table + formatted text, not a plain-text file
  renamed), following the same document formatting standards as the resume
  so it looks like it belongs to the same document set. Deliver it alongside
  the chat screening summary, not instead of it — the chat table stays for
  a quick read, the .docx is the shareable/downloadable version.

### Part B — Hard disqualifiers (any single "yes" ends scoring at 0%)

Check in order, using job-search-config.md:

1. Title is not one of the target titles or a direct synonym.
2. Skill & Experience Match is below 75% ("Not competitive").
3. Onsite/hybrid location falls outside the accepted list, with no fully
   remote option.
4. Pay does not clear the applicable floor.
5. Contract length is under the minimum, with no stated extension or
   conversion path (contract roles only).
6. Requires a clearance/authorization the user does not currently hold, per
   job-search-config.md's notes.

If none of these hit, run the full weighted score.

### Part C — Weighted score (100 points)

Score location and onsite/remote percentage as **two separate categories** —
where a job is based, and how much time is actually required in person, are
different risks.

| Category | Points | Full credit when... | Partial credit when... |
|---|---|---|---|
| Skill & Experience Match | 30 | Feed in the Part A percentage: `(Match % / 100) * 30` | — |
| Physical location | 15 | In an accepted location, or fully remote | Location not yet specified/TBD = 8, flag to confirm |
| Onsite/remote % | 15 | 0% onsite = 15; light hybrid (1-2 days/week) in an accepted metro = 15; 100% onsite in the home-base city = 15; 100% onsite elsewhere clearing the higher pay floor = 15 | 3 days/week onsite = 12; 4 days/week onsite = 8, flag to confirm |
| Compensation vs. floor | 20 | Clearly clears the applicable floor | At or barely above floor = 12; wide range with the floor sitting mid-band = 15 |
| Contract duration/stability | 5 | FTE, open-ended consulting, or 6+ months stated | 3-6 months with a real extension track record = 3; duration not stated = 2, flag to confirm |
| Industry fit | 5 | Matches the preferred list | Adjacent regulated-like environment = 3; unrelated = 1 |
| Bias/red-flag language | 5 | No red-flag language; posting emphasizes outcomes/experience | One red-flag phrase = 3; multiple, or a "high-energy" culture framing = 0-1 |
| Sponsorship/clearance fit | 5 | Nothing new required | Stance unclear = 3; requires something new = 0 |

**Red-flag language to watch for (age-bias signals):** "digital native," "high
energy," "fresh perspective," "recent graduate preferred," "reverse
mentoring," "startup hustle," heavy emphasis on culture perks over delivery
outcomes. These don't disqualify a posting by themselves — they lower this
one category and should be named out loud in the summary.

### The call

- **90-100% overall (and A-List on Part A):** Green light — proceed to Stage 2.
- **80-89%:** Borderline. Summarize the score, the Skill & Experience Match,
  and the specific gaps, then present the user a numbered choice (pursue
  anyway / pass / get one more piece of information) before building
  anything.
- **Below 80%, or any hard disqualifier tripped:** Pass. Give a one-line
  reason, log it in the tracker, and stop — don't build materials unless the
  user explicitly overrides.

### Output format for a screening result

Keep it short — executive-summary style with tables/bullets, not prose:

1. **Skill & Experience Match: XX% — [A-List / B-List / Not competitive]**
2. **Overall Score: XX% — [Pursue / Ask first / Pass]**
3. One-line reason if it's a hard disqualifier or a clear pass.
4. A small table showing the category scores that drove the number.
5. Any red-flag phrases spotted, named directly.
6. The Skill & Experience Match report, delivered as a .docx file.
7. Numbered next-step options (e.g., "1. Build tailored resume + cover
   letter now. 2. Log as pass and move on. 3. Ask [specific missing detail]
   before scoring further.").

## Stage 2 — Build the tailored resume and cover letter

Only start once a lead clears Stage 1 (or the user consciously overrides a
borderline score).

- Pull only from the baseline resume data source in job-search-config.md
  — reword and reorganize to match the posting's language; never invent a
  qualification, employer, or number.
- Apply every standing correction from job-search-config.md consistently
  (exact certification names, "coached" vs. "led" framing, LinkedIn in the
  contact line) without being asked each time.
- Name real gaps honestly in the cover letter rather than staying silent or
  overclaiming — interviewers test what doesn't hold up.
- Lead the cover letter with the strongest quantified outcome available, hit
  the posting's top requirements directly, and close with a specific,
  direct ask (a call, an interview, a next step).
- Keep the resume to a length that respects the reader's time (2-3 pages is
  a reasonable default) and format it per the resume format row in
  job-search-config.md.
- **File format: every generated document — resume, cover letter, the
  Skill & Experience Match report, any prep packet or worksheet — is built
  and delivered as .docx by default.** Only switch to a different format if
  a specific posting's application portal explicitly requires it.
- If age-related bias is a live concern for this user, apply the age-bias
  coaching row when drafting interview prep material — do not apply it to
  the resume/cover letter text itself unless the user asks for that framing
  there too.

### ATS optimization checklist (apply to every resume before delivery)

Most applications are screened by an Applicant Tracking System before a
person ever sees the resume. A resume that reads well to a human but breaks
the parser never gets that far. Apply all of the following when building or
reviewing a resume, regardless of what visual template the user's resume
format uses:

**Formatting that breaks parsers — avoid these:**
- Multi-column layouts, text boxes, or tables used to lay out the page
  (a single-column layout is the safest default).
- Contact information placed only in a header or footer — many parsers skip
  those entirely; put name and contact details in the body of the first
  page.
- Icons, logos, photos, charts, or other graphics standing in for text
  (a phone icon next to a number, a skill-level graphic instead of a word).
- Non-standard or decorative fonts; stick to a common system font.
- Text embedded inside an image (never lets the parser read it at all).

**Section and keyword conventions — apply these:**
- Use standard, literal section headings a parser expects: "Professional
  Experience" (or "Experience"), "Education," "Skills" or "Core
  Competencies," "Certifications." Creative or rebranded headings
  ("My Journey," "What I Bring") often go unrecognized.
- Pull the exact phrases the posting uses for its required and preferred
  qualifications, and mirror that exact wording in the summary, a
  competencies section, and relevant bullets — wherever it's true. A
  parser matches literal strings; "SAFe" and "Scaled Agile Framework" are
  not automatically treated as the same term, so use whichever term (or
  both) the posting itself uses.
- Spell out acronyms at least once if the posting does, and vice versa —
  match both forms when it's not clear which the parser is keyed to.
- Use standard date formats (Month Year – Month Year) rather than
  stylized or ambiguous ones.

**File format:**
- Default to .docx for every document generated by this workflow —
  resume, cover letter, and the Skill & Experience Match report alike.
  .docx parses reliably across ATS platforms for the resume/cover letter,
  and stays easy for the user to open, edit, or print for everything else.
  Only switch to a different format if a specific posting's application
  portal explicitly requires it.
- A resume that already avoids columns, text boxes, and graphics parses
  safely; the file-type choice only becomes protective against parsing
  problems the layout itself would otherwise cause.

**Self-check before delivery:**
- Copy the resume's text into a plain-text editor (or select all and
  paste as unformatted text). If any content is missing, scrambled out of
  order, or run together without spaces, an ATS will likely have the same
  problem — fix the layout issue causing it before sending the file.
- Confirm every keyword mirrored from the posting actually reflects a
  real, documented qualification — matching the posting's language is
  about parsing, not about claiming something that isn't true.

## Stage 3 — Track every application in one place

Maintain a single tracker (one entry per opportunity) recording, at minimum:

- Company, role, recruiter/source, and where the lead came from.
- The Stage 1 score and the specific reasoning behind it.
- Files produced (all .docx) and the date.
- Every confirmed fact a recruiter or hiring manager provides (rate,
  schedule, interview dates, panel names) — write it down the moment it's
  learned so it is never re-asked in a future session.
- Current status and the next concrete step.

Before answering a question about a company or requisition the user has
already discussed, check the tracker first rather than asking the user to
repeat themselves.

## Stage 4 — Score long prompts before running them (optional)

If the user is running AI-assisted research or document-building prompts as
part of the search, and a prompt is longer than the threshold in the
job-search-config.md:

1. Score it on four dimensions — **Clarity** (is the ask unambiguous),
   **Scope** (sized right, not too broad or narrow), **Context** (does it
   have what it needs to succeed), **Format** (does it say what output is
   wanted).
2. If it scores low on any dimension, rewrite before running rather than
   after.
3. Log the prompt, its score, the model that scored it, and the model that
   ran it, to the master prompt log named in job-search-config.md.

## Stage 5 — Follow up and close out

- When an offer arrives, use a short accept/decline template rather than
  drafting one from scratch under time pressure.
- The moment one offer is accepted, send a brief thank-you-and-withdraw note
  to every other recruiter with an open submission — this protects the
  user's reputation for future roles.
- Update the tracker's status field the same day something changes.

## Anti-patterns to avoid

- Building a resume before scoring the opportunity.
- Letting AI invent or round up a qualification not in the baseline data.
- Re-explaining the same fact to Claude every session instead of writing it
  in the tracker once.
- Treating a borderline (80-89%) score as an automatic green light.
- Leaving the tracker's status stale after something changes.
- Submitting through multiple recruiters for the same requisition without
  flagging the possible channel conflict.
- Skipping the honest-gap paragraph in a cover letter — silence reads as
  evasion once an interviewer finds the gap themselves.
- Delivering any file in a format other than .docx without a specific
  posting requirement forcing the exception.

## Verification (always the last step before sending anything)

- [ ] Every claim in the resume, cover letter, and Skill & Experience Match
      report is real and traceable to the baseline data source — nothing
      invented or rounded up.
- [ ] The Stage 1 score and reasoning are logged in the tracker.
- [ ] Any known gap is named in the materials, not hidden.
- [ ] Standing corrections (certification names, framing, contact details)
      are applied exactly as configured.
- [ ] The resume passes the ATS optimization checklist (no columns/text
      boxes/graphics, contact info in the body not just a header, standard
      section headings, posting's exact keywords mirrored where true, plain-
      text self-check done).
- [ ] Every generated file (resume, cover letter, match report) is a real,
      valid .docx, unless a specific posting required another format.
- [ ] The tracker entry is current: files listed, status accurate, next
      step written down.
- [ ] A duplicate-requisition risk, if any, has been flagged to the user.
