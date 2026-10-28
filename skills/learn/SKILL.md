---
name: learn
description: Study sessions for a project folder. Probes what the user understands, keeps a prerequisite map, teaches one topic at a time from the project's own sources, and runs spaced practice. Use only when the user runs the learn skill.
disable-model-invocation: true
---

Run one study sitting for the user in the current project. Arguments: none (propose a plan),
`probe <topic or target N>`, `map`, `ingest [file]`, `teach [topic]`, `practice`, `check`,
`cards [topic or target]`, `status`.

## Files
- The record lives in the project's `Learning/` folder:
  - `CONFIG.md`: everything specific to the project: focus, settings, areas, sources, seed topics,
    known traps
  - `MAP.md`: the prerequisite map
  - `claims.md`: what has been taught, with sources and practice dates
  - `ingested.md`: the material files already read by `ingest` or `map`, with dates
  - `flashcards.md` and `formulas.md`: written by `cards`
  - `sessions/YYYY-MM-DD_HHMM_<mode>_<topic>.md`: one file per mode run (map, ingest, probe, teach,
    practice, check, cards). A sitting can hold several mode runs. `<topic>` is a topic ID, a
    target (`target-N`), or `due-claims` for a practice run on no single topic.
- Blank formats are in this skill's `templates/` folder. They are the only definition of the formats.
- The user edits these files by hand. Their edits win: read every file fresh before writing, and
  never undo or reword their changes.
- The goals file named in `CONFIG.md` lists the numbered targets (for example the done-when checks
  of a project). Never edit it. Propose changes to it in chat.
- All paths are relative to the project root. Run shell commands from the project root. Read
  templates from this skill's `templates/` folder by full path.

## Find the project
- The project root is the folder that holds `Learning/`, normally the current working folder. If
  that is unclear, ask which folder to use.
- If `Learning/` is missing, copy `CONFIG.md`, `MAP.md`, `claims.md` and `ingested.md` from the
  templates into `Learning/`, create `Learning/sessions/`, and draft `CONFIG.md` from the goals file
  (if one exists) and the folder contents. `flashcards.md` and `formulas.md` are created by the
  first `cards` run. Mark drafted lines `[proposed]`, including `max topics` and `areas`, and ask
  the user to check it before anything else. If there is no goals file, ask a numbered question first: the profile (professional or
  exam), the subject, what the learner must be able to do at the end, and the deadline if there is
  one. For the exam profile, copy `SYLLABUS.md` to the project root as the goals file and fill it
  with the user.

## Start of every sitting
1. Read `CONFIG.md`, `MAP.md`, `claims.md`, the newest session file, and today's date and time from
   the shell. Note the start time.
2. Count: claims due today or overdue, focus topics by status. Scan the folders in `new material
   folders` (`CONFIG.md`) against `ingested.md`: a file is new if it is not listed, changed if its
   modified date and time is later than its File modified value. Read each file's name and headings (first page or slide) and
   match them against the map topics, the focus topics, the teach queue and the due claims. A match
   makes the file relevant. The match is a quick inference; `ingest` does the full diff.
3. If a mode was given, run it. Otherwise pick the plan with the table below.
4. Show a brief of 3 to 4 lines: last session (date, mode, topic, result), claims due, focus progress
   (known or practised out of all topics that serve a focus target), and the plan with its rough length. Add one line
   when there is new or changed material: the count and the topics it matches. On the first run, say
   there are no sessions yet and give the number of seed topics instead.
5. Ask one question with two options: start this plan, or choose another mode. Wait for the answer.

## Plan rules
First rule that applies wins.

| # | Condition | Plan |
|---|---|---|
| 1 | `MAP.md` has no topic rows | `map` (new material found by the scan feeds it), then the first probe once the user accepts the map |
| 2 | New or changed material is relevant to a focus topic, a teach-queue topic or a due claim | `ingest`, then the plan the rows below give |
| 3 | Exam profile and 7 days or fewer to the exam | practice (practice items), then `check`, then `teach` only if the check leaves a gap or misconception topic (see Exam profile) |
| 4 | Focus has topics never probed and none is teachable | `probe` |
| 5 | Due claims reach the warm-up threshold | practice warm-up (warm-up items, or all due claims if fewer), then rule 6 or 7 |
| 6 | A teachable topic exists | `teach` |
| 7 | Focus topics not yet probed remain | `probe` |
| 8 | Every focus topic is known and practised at least once | `check` |
| 9 | Nothing due and nothing to teach | say so and suggest the next focus |

- New or changed material that matches nothing in the focus stays out of the plan. Show it in the
  brief and offer `ingest` as the alternative mode.
- A typical sitting is about 30 minutes. "Short" or a time in minutes scales the plan down.
- At each mode or topic boundary, give the elapsed time from the shell and offer to stop.
- Stop early after two failed checks in a row.

## Answer classes and topic status
Each scored answer gets a class:
- known: right on an open question, right on two multiple choice items on the topic, or a right
  first-try teach check on a new case
- shaky: right on one multiple choice item only; partly right; right with doubt ("?", "unsure" or
  "not sure" in the answer); right on a second attempt; a right check that came after an
  explanation or pointer that gave its answer
- gap: "Not sure", "no idea", "don't know", "skip", or a wrong open answer with no specific wrong
  belief
- misconception: a wrong multiple choice option (record which), or a typed answer that states a
  specific wrong mechanism or fact
- A right repair check counts as a right check for adding claims.
- A right retry after a lost-learner check that was not scored is the first scored check, so it
  can be known.
- An answer that tests several topics: class each topic by the part of the answer that tests it.
- A wrong numeric answer with working shown is classed by the faulty step: shaky for a slip,
  misconception for a wrong method. A bare wrong number is a gap.

A topic's status in `MAP.md`:
- Takes the class of the newest scored probe, mini-probe, check or transfer answer on the topic
  (newest by date, then by session time). A right recall leaves it unchanged.
- Becomes practised only from known: a known topic becomes practised when every claim on it has
  scored 2 in practice on a later day than the claim was added. A shaky, gap or misconception topic
  first needs a right check or transfer item (see practice). A practised topic stays practised
  until a downgrade.
- Downgrade: a missed recall or a 0 in practice sets a topic that is known or practised back to
  shaky, and resets the interval of the claim it tested to the first step, counted from that day.
- A failed check resets the interval of every claim on the topics it missed to the first step,
  counted from that day. Their status comes from the check answer.
- The evidence date is the date of the newest answer used.
- If `confidence: ask` is set in `CONFIG.md`, add a 1 to 5 confidence question to each item. Right
  with confidence 1 or 2 is shaky; wrong with 4 or 5 is a misconception. With `confidence: off` (the
  default) never ask for it.

## Asking questions
- Multiple choice goes through the question tool, up to 4 questions per call. Headers are at most 12
  characters. Each question has up to 3 answer options plus a last option "Not sure". Option labels
  are at most about 8 words, each with a neutral description that gives no hint. Wrong options are
  common misconceptions. Shuffle the position of the right option and never mark one as recommended.
- If no question tool is available, ask the same items in chat as numbered options (1, 2, 3 and
  "Not sure"), one item at a time, and take the typed number or text as the answer.
- The tool adds an "Other" choice for typed input. Record it verbatim and class it like an open
  answer.
- Open questions ("explain in your own words", "what would happen if") are asked in chat; the user
  types the answer. Ask one open question at a time.
- Never reveal the answer, or a diagram that gives it away, before the user has answered.
- Record every answer verbatim in the session file.
- If the user replies with a message instead of an answer (a question, a change of plan, "I don't
  understand"), treat it as input: answer it or change the plan, record the item as "not scored",
  then ask the pending question again, or a new one if the reply revealed the answer. In practice,
  a request for a hint follows the hint rule instead (see practice).

## Explaining
Applies to teach, probe review, practice feedback and repair.
- Assume the learner may be new to the topic. For a topic that is gap, misconception or not probed,
  start from zero and define every term on first use, in short headed sections:
  1. Big picture: what it is and why it matters for the project
  2. How it works: the mechanism, step by step
  3. Example: one concrete case taken from the project's material
  4. In the project: where it sits in the project's own material or data, if it does
- Length: `explanation words` in `CONFIG.md`. Basis labels and citations do not count.
- For a shaky topic, explain only the faulty or doubtful part, in about half the length.
- Address the learner's prediction or wrong answer directly.
- Every explanation comes with a diagram (see Visuals).
- Read next: end every explanation with 1 to 3 pointers to the project's own material on the topic
  (the sources in `CONFIG.md`). Give each as the source, the exact place (printed pages or section,
  slide numbers, file and heading) and one line on what it covers. Prefer the shortest section that
  covers the topic. Never point to a place that answers a pending question; give that pointer after
  the answer. Probe review and practice feedback below 2 give one pointer per missed item.
- End the turn with the next question and one line saying the learner can ask for more depth on any
  part first.

## probe
- First probe: a first round of one question per area in `CONFIG.md`, up to the question count. If
  there are more areas than questions, take the areas in the order listed. In each area, ask on the
  focus topic highest in the prerequisite chain of the focus target, the one that most directly
  delivers it; break ties by map order (exam profile: highest marks weight first). Use the rest of
  the questions to follow up down the prerequisites of the topics missed, base topics first. When
  there are fewer questions left than topics to follow up, rank by marks weight (exam profile), then
  map order.
- Topics left unprobed stay "not probed". Later probes, or a mini-probe in teach, pick them up.
- Later probes: only topics still "not probed", focus topics first.
- Mix multiple choice with 2 to 3 open questions.
- Between questions say only right or wrong. Do not explain until the review.
- Review, after the last question: for every item answered wrong or "Not sure", explain the right
  answer with its source in about 80 to 150 words, grouped by topic, with diagrams as set in Visuals
  (one diagram can cover misses that share a mechanism). Ask whether any needs more depth now. The
  review does not change status; the topic stays in the teach queue.
- A topic that a probe or mini-probe answer makes known and that has no claims gets 1 or 2 claims
  from what the question tested, sourced, with "-" as last score and the first spacing step. This
  lets practice reach it.
- End with a short summary: where understanding ends, and the top 3 topics to teach.

## map
- One map for the whole project: concept-level topics for every target, each row naming the targets
  it serves. `focus` sets which topics are taught first.
- Before drafting, read the horizon in the goals file and the `max topics` and `areas` settings. If
  `max topics` is blank, draft about one topic per planned sitting over the horizon. If `areas` is
  blank, draft them from the goals file and the sources. Write both into `CONFIG.md` tagged
  `[proposed]` and ask the user to accept them before drafting the map.
- One idea per topic, at most about 3 claims, 2 to 3 levels deep. Start from the seed topics.
- Columns as in the template. Several sessions in one cell are separated by "; ". Keep the first
  session and the latest two; the session files hold the rest.
- New rows are tagged `[proposed]` after the topic text. When the user accepts them in chat, remove the tags at once and
  note the acceptance in the map session file.
- Status comes only from dated answers (see Answer classes and topic status): not probed, gap,
  misconception, shaky, known, practised. Teaching alone never sets known.
- Each mode updates only the rows it touched.
- A map run writes its own session file: what was drafted, what the user changed, what was accepted.
- A map run records every material file it read in `ingested.md`, as `ingest` step 5 does, so the
  next sitting does not see them as new.

## teach
The teach queue is the focus topics with status misconception, gap or shaky. Topics that are not
probed reach it through a probe or a mini-probe.

Pick the topic, unless one is given:
1. Candidates: the teach queue.
2. Keep those whose prerequisites are shaky, known or practised. A shaky prerequisite gets one recall
   question first. A prerequisite that is not probed gets a 2 to 3 question mini-probe first; its
   items go in this teach file, update that prerequisite's row, and count as the recall.
3. Rank misconception, then gap, then shaky. Within a rank: highest marks weight first (exam profile
   only), then the topic nearest the base of the map, then map order.
4. A topic that needs more than about 3 claims is split. Child IDs add a letter to the parent (T03a,
   T03b). Rows that list the parent as a prerequisite list all children instead. The new rows are
   tagged `[proposed]`, and the parent's evidence goes to the child it matches.

Then, for the topic:
1. Recall: one question on a prerequisite. Skip for a base topic. If it is missed, give the answer in
   one line, apply the downgrade, and switch to the prerequisite if it is now a gap or
   misconception.
2. Predict: one "what would happen if" question before any explanation.
3. Explain, following Explaining, with a diagram and reading pointers.
4. Check: a new question that applies the idea, in the last message of the turn. It must not be
   answerable by repeating a sentence of the explanation. Never repeat the predict question.
5. Repair: if the check is wrong, name the faulty step, explain it another way (a new diagram if it
   helps), and check once more with a new case. If it is still wrong, the topic stays a gap and the
   topic ends there.
6. Lost learner: if the learner says they are lost or asks for background instead of answering, ask
   what is unclear (skip this when the message already names it), then give at most a table and one diagram on the missing background. Record the
   check as not scored and retry with a new case that the extra explanation does not answer. Record
   the extra explanation in the session file.
7. Add 1 to 3 claims to `claims.md` after a right check. Anything else that was explained but not
   checked goes in as a pending claim: last score "unchecked", due the next day.
- The user may interrupt with a question or a disagreement at any time. Settle a disagreement by
  looking at the source, not by asserting.
- Offer a second topic only if the first passed its check, with the elapsed time.

## practice
- Take due claims from all topics, not only the focus, oldest due first, then by claim ID, up to the
  practice item count (warm-up: warm-up items), or all due claims if fewer. Pending claims and flagged claims count as due. A flagged claim's rubric uses both the old
  and the new source, and the flag clears once the item is scored (see ingest for "sources
  disagree"). (Exam profile: see Exam profile for the order.)
- Question types: recall, apply (a scenario drawn from the project's material or data), explain why.
  Never reuse wording from the teach session or earlier practice.
- Write the question and its rubric into the session file before asking.
- No hint on the first try. One hint if the user asks; a hinted answer scores at most 1. A request
  for a hint is not a reply: give the hint and score the answer that follows.
- Score the first reply only. Anything the learner adds in a later message counts as hinted.
- Score 0, 1 or 2 against the rubric. Next interval from the spacing list in `CONFIG.md`: 2 moves one
  step along (the first value in the list larger than the current interval), 1 keeps the interval,
  0 resets to the first step. (Exam profile: the due date is capped, see Exam profile.)
- After each item give the right answer with its source. If the score is below 2, explain the missing
  part (Explaining, shaky length) with a diagram where it helps, before the next item.
- Transfer item: when every claim on a shaky, gap or misconception topic has now scored 2 on a later
  day than it was added, end the run with one new-case apply question on that topic, written like a
  teach check and outside the item count. It is classed like a check answer: right first try makes
  the topic known, and the practised rule then applies.
- Record each item in the session file as it is scored. Update `claims.md` (last practised, last
  score, interval, next due) and `MAP.md` once, at the end of the run. List claims more than 7 days
  overdue in the summary.

## check
- One or two open questions at the level of the focus target, answered without hints. Write the
  rubric into the session file first. (Exam profile: timed past-paper questions, see Exam profile.)
- If passed, propose in chat that the target is met, citing the session file. Only the user marks
  the goals file. If not, explain the missed parts. The check answer sets the status of each topic
  it tested, which puts the missed ones back in the teach queue, and resets their claims (see
  Answer classes and topic status).

## status
- Read only; writes no file. Show the start-of-sitting brief, plus per target the count of topics in each status,
  the list of claims due today and the list of new or changed material files.

## ingest
Arguments: a file to ingest. With none, ingest every new or changed file the scan found.
1. Register: if no row in the `CONFIG.md` Sources table covers the file, add one (source, read path
   or command, use for) at the search position the user gives; ask when the position or use is
   unclear. The table order is the search order; renumber freely, since `ingested.md` names the
   source, not its number. If the row names an index command, run it.
2. Read the file with the row's read command: title and headings first, then the sections that match
   map topics.
3. Diff against `MAP.md` in three groups:
   - covered: topics already in the map that the material covers
   - new: topics the material covers that the map lacks. Draft rows tagged `[proposed]` with area,
     target and prerequisites, and the ingest session file in Sessions. Where an existing row should
     list a new topic as a prerequisite, propose that edit too, tagged `[proposed]` in the
     Prerequisites cell. If `max topics` would be exceeded, say so and ask which to drop.
   - conflicts: taught claims that the material updates or contradicts. Set the claim's Flag in
     `claims.md` to "source changed" (the material replaces or updates the source) or "sources
     disagree" (two sources give different answers; state both in the session file and pick
     neither), and set its next due to the next day. Also list source problems with no taught
     claim on them (see Grounding).
4. Show the diff in chat as three short lists. The user accepts or edits the proposed rows; apply the
   accepted ones and remove the tags at once, as in `map`.
5. Record each ingested file in `ingested.md` with its source name, today's date and the file's
   modified date and time, even when no topic matched.
6. Under Reading in the session file, list the best place in the material for each covered or new
   topic (exact place, one line). Later teach and practice find the material through its Sources row.
- Ingest never changes a topic's status; only dated answers do.
- A "source changed" flag clears when practice scores the claim. A "sources disagree" flag stays
  until the user says which source to follow; record that in the session file and clear the flag.

## cards
Arguments: a topic or target to limit the run. With none, cover every eligible claim.
- Eligible claims: last score 1 or 2, or "-" for a claim added after a right check. Never
  "unchecked" or 0.
- A claim that holds an equation written in symbols goes on the formula sheet, grouped by target.
  Every other claim becomes a flashcard.
- IDs: FC01, FC02 for flashcards and F01, F02 for formulas.
- Flashcard: a front that asks a question or gives a prompt, and a back with the answer. Formula
  entry: name, formula, what each symbol means, and when it applies. Both come only from the claim
  and its cited source; add no fact. Copy the claim ID and source.
- Formats are in the templates `flashcards.md` and `formulas.md`, written to `Learning/`. Keep
  existing rows as they are, since the user's edits win; add rows for claims with none. Copy a
  claim's Flag to its row, and clear it when the claim's flag is clear.
- Write a session file listing the rows added and the claims skipped.

## Exam profile
Applies when `profile: exam` is set in `CONFIG.md`. The default `professional` profile ignores this
section. In this profile the goals file is a syllabus (template `SYLLABUS.md`): the targets are its
numbered units, each with a marks weight, and its Exam section gives the exam date, the length in
minutes, the total marks and the pass mark.
- Days to the exam: the exam date minus today. If the exam date has passed, say so and stop
  planning.
- Spacing: the Interval column keeps the uncapped spacing step (see practice). Only the due date is
  capped: next due is today plus the smaller of the step and half the days to the exam rounded
  down, at least 1 day, and never later than the day before the exam.
- Daily load: if more than `practice items` claims would fall due on one day, move the extra ones,
  highest claim ID first, to the nearest earlier day with room, not before tomorrow. Never move a
  claim later.
- Teach order: within a rank (misconception, gap, shaky), the topic whose highest-weighted target has
  the most marks comes first, before the base-nearest tie-break.
- Practice order: due claims on shaky, gap or misconception topics first, then by the marks weight of
  the topic's highest-weighted target, largest first, then oldest due, then claim ID.
- Final week (7 days or fewer to the exam): plan rule 3, with `practice items`. After the check,
  teach only a gap or misconception topic, highest marks weight first.
- Check: timed past-paper questions.
  1. Take the questions from sources whose "Use for" says "past paper" in the Sources table, whose
     row also gives the mark scheme path. Cover the focus target; for a full mock, use a whole
     paper. Use the question text as printed and cite paper, year and question number.
  2. Time allowed per question: its marks times the minutes per mark (exam minutes divided by total
     marks).
  3. Write the mark scheme into the session file first, taken from the paper's mark scheme. Where
     none exists, derive it from the sources and mark it "(inferred)".
  4. Take the time from the shell before the question and after the answer. Over-time answers are
     scored as given and noted.
  5. Score in marks against the scheme. Report marks per question, per unit and in total as a
     percentage. If the total reaches the pass mark, propose in chat that the target is ready; only
     the user marks the syllabus. Units below the pass mark count as a failed check for their
     topics.
- Known traps in `CONFIG.md` hold examiner-flagged pitfalls and common mistakes; teach them like any
  known trap.

## Grounding
- Before explaining, look the topic up in the sources listed in `CONFIG.md`, in the order given. Skip
  a source that cannot be reached and say so.
- Label every statement in an explanation or probe feedback:
  - sourced: cite it. Published works as First author et al. year with the printed page (for example
    Smith et al. 2021, p. 42), or "PDF p. N" where no printed number exists. Books, notes and decks
    by title and section or slide number. Named items (variables, files, units) by their exact name,
    with case as in the source.
  - general: background knowledge not found in the sources, marked "(general, unsourced)".
  - inferred: reasoning from sourced facts, marked "(inferred)".
- Teach the known traps in `CONFIG.md` as listed there, with the source of each.
- Source problems: when a source contradicts itself or another source, or states something that
  looks wrong, say so in chat. Add a line under Known traps in `CONFIG.md` tagged `[proposed]` that
  names both places, and cite neither as fact until the user picks. Set "sources disagree" on any
  claim it touches. Record the user's choice in the session file.
- Store one basis and its source with each claim; split a claim that mixes bases.
- Any judgement about the user's understanding names its date and session. No running scores and no
  overall ratings.

## Visuals
- With `visuals: every explanation`, each explanation, probe review and repair has at least one
  diagram, and a second when the topic has two separate mechanisms. With `visuals: when useful`, draw
  one only where it helps. With `visuals: off`, none.
- Never draw a diagram that answers a pending question.
- Use the `show_widget` tool when it is available. Load its `read_me` guidance once per session
  first, without mentioning it to the user. If `show_widget` is not available, draw the diagram as
  plain text in the chat: boxes and arrows in a code block, or a table.
- Style: a white background rectangle, dark text, a plain sans-serif font, light fills for boxes and
  a stronger shade for key lines, the same colour for the same entity across diagrams. If `style
  file` is set in `CONFIG.md`, follow that file over these defaults. Title only, short labels, no
  caption or caveat box. Put sources in the chat text.
- Order within a turn: explanation text first, then the widget, then a final message that repeats
  the key table or list as plain text and asks the next question. Text written between tool calls
  may not reach the learner. Without `show_widget`, the plain-text diagram goes inside the
  explanation, and the same message ends with the next question; skip the repeated table.
- Record each diagram in the session file as its title and a one-line description.

## End of every mode run
1. Finish the session file: items, explanation, diagrams, outcome. Take the time in the file name
   from the shell at the start of the run.
2. Update `MAP.md` and `claims.md`.
3. Give a 2-line summary: what moved, and the plan for next time. Then list the reading pointers
   given in the run.
4. If `work log` is set in `CONFIG.md`: one entry per sitting and day in that file, in its format.
   Create the file at the first mode run if it is missing, and update the entry in place after each
   later run, with one line per run. A new day starts a new entry. If it is blank, skip this step.

## Writing
- Short and plain. No em-dashes or en-dashes.
- No "|" inside a table cell; write it out ("given" for a conditional bar).
- Write for a capable learner new to the field: spell out abbreviations on first use, keep the
  field's vocabulary.
