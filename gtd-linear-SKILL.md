---
name: gtd-linear
description: Run a personal Getting Things Done (GTD) workflow entirely in Linear, with a Decision Matrix process for choices between multiple viable options. Use this skill whenever the user wants to capture a task or commitment, clarify what something means and what the next action is, organize Linear issues into the right project/state, run a weekly review, decide what to work on next, or choose between several alternatives. Trigger on phrases like "add this to Linear," "what should I work on," "I need to follow up on," "help me plan my week," "weekly review," "process my inbox," "should I do X or Y," "I'm trying to decide between," "next action," or any task-capture or decision intent — even when the user doesn't name GTD or Linear explicitly. Do NOT use for coding tasks, factual lookups, or generic advice unrelated to running a personal task system.
---

# GTD in Linear, with Decision Matrix

A personal workflow that combines David Allen's *Getting Things Done* with a Decision Matrix process for non-obvious choices. All task state lives in Linear; this skill defines how to put it there and how to act on it.

---

## Part 1: The GTD workflow

GTD has five sequential stages. Run them in order; don't skip.

### 1. Capture

Anything on the user's mind that might require action goes into an inbox first. Don't try to organize at capture time — the only goal is to get it out of their head.

- The user's Linear **Inbox** (or "Triage" view) is the single capture target.
- When the user mentions a task, commitment, idea, or open loop in conversation, create a Linear issue in the inbox with `save_issue`. No project, no labels, no due date yet — just title and (if useful) a one-line description.
- Capture verbatim where possible. Don't pre-interpret. If the user says "the thing with the landlord," the title is "The thing with the landlord" — clarification happens in stage 2.
- Multiple items in one message → multiple issues, not one issue with a list.

### 2. Clarify

For each captured item, ask two questions in order:

1. **Is it actionable?**
   - **No** → it becomes one of: *trash* (delete the issue), *reference* (description-only issue, label `reference`, no state changes expected), or *someday/maybe* (move to the Someday project — see stage 3).
   - **Yes** → continue.
2. **What's the next physical, visible action?**
   - Rewrite the issue title as that action, starting with a verb. "Mom's birthday" → "Text Mom asking what she wants for her birthday." "Taxes" → "Gather W-2 and 1099s in folder."
   - If completing the next action won't finish the whole outcome, it's a **project** (GTD sense: any outcome requiring more than one action). Create a Linear project for it and make the issue the first next-action under that project.

**Two-minute rule.** If the next action takes less than two minutes, the user should do it now instead of filing it. Say so explicitly: "This is under two minutes — do it now, don't file it." Only file it if they decline.

### 3. Organize

Every clarified issue lands in one of these buckets. In Linear:

| GTD bucket | Linear representation |
|---|---|
| Next Actions | Issue in state `Todo` or `In Progress`, in the relevant project, with a **context label** (see below) |
| Projects | Linear **project** containing the issues that make up the outcome |
| Waiting For | Issue in state `Todo` with label `waiting-for`, assignee left as the user, description noting who is blocking and since when |
| Someday/Maybe | Issue in a dedicated `Someday` project, state `Backlog` |
| Reference | Issue with label `reference`, state `Backlog` or closed — kept for lookup, not for doing |
| Calendar | **Not in Linear.** Date+time-specific commitments belong on the calendar, not in the task system. If the user adds one to Linear, flag it. |

**Context labels.** GTD organizes next actions by the *context* needed to do them — the tool, place, or energy required. Default label set:

- `@computer` — needs a laptop
- `@phone` — phone calls
- `@errands` — out of the house
- `@home` — at home, off-screen
- `@anywhere` — pure thinking, can be done on a phone in line
- `@high-energy` — needs focus
- `@low-energy` — possible when tired

Apply one or more per next-action issue. The user may have their own context set — if so, use theirs; don't impose the default.

**Project hygiene.** Every Linear project should have at least one issue in `Todo` that represents the next physical action. If a project has no next action, it's stalled — flag it during the weekly review.

### 4. Reflect (Weekly Review)

When the user asks for a weekly review, or it's been a week since the last one, run this sequence:

1. **Process the inbox to zero.** List every issue in `Triage`/Inbox. For each, run stage 2 (Clarify) with the user.
2. **Review every active project.** `list_projects` filtered to active. For each, confirm it still has a defined next action. If not, ask: "What's the next concrete step on this?" or "Should this move to Someday, or close?"
3. **Review Waiting For.** `list_issues` with label `waiting-for`. For each, ask whether to nudge the blocker, escalate, or drop it.
4. **Review Someday/Maybe.** Scan the Someday project. Surface anything that's become time-sensitive or that the user clearly isn't going to do — offer to close it.
5. **Scan upcoming.** Issues with due dates in the next 7 days — confirm they're still real and have a next action.
6. **Surface stalled items.** Issues that haven't been updated in over 14 days and aren't in `Someday`/`Backlog` — ask whether they're still alive.

Output a short summary at the end: inbox processed, projects active, projects stalled, items moved to Someday, items closed. Don't make this a wall of text — the user has just done the work.

### 5. Engage (What to do now)

When the user asks "what should I work on" / "what's next":

1. Pull current state: `list_issues` filtered to assignee=user, state in (`Todo`, `In Progress`), not in `Someday`.
2. Filter by what's actually possible right now. GTD's four criteria, in order:
   - **Context** — what tools / location / energy does the user have? Ask if unclear. Filter by matching `@` labels.
   - **Time available** — ask if unclear. A 15-minute window and a 3-hour window suggest different work.
   - **Energy** — high-energy work needs focus; low-energy windows are for routine items.
   - **Priority** — among items that pass the first three filters, pick by Linear priority and due date.
3. Recommend one specific next action, not a list. Say why. The user can override.

Don't dump the full task list. The point of the system is that they don't have to scan it.

---

## Part 2: The Decision Matrix

When the user is choosing between multiple viable options — a project approach, a tool, a vendor, a path forward — and the choice isn't obvious, run a Decision Matrix instead of a pros/cons list. Capture the result as a Linear document or as the description of the parent project issue.

### When to use it

- Two or more genuinely viable options.
- The choice has meaningful consequences (time, money, lock-in, opportunity cost).
- The user is going back and forth, or different considerations point different directions.

Don't run this for trivial choices or when there's clearly a dominant option.

### The five parts of a Decision Document

Produce all five, in order:

1. **Problem Statement and Constraints.** State the decision being made, the objective, and the hard non-negotiable constraints (budget, timeline, must-haves). Without this, every later step optimizes for the wrong thing.
2. **Solution Space.** A short neutral description of each option. Use technical descriptions, not personal labels — "in-memory cache option," not "Alice's plan."
3. **Decision Matrix.** A table with options as columns and metrics as rows. Each cell holds a **quantitative value** wherever possible (dollars, hours, GB, count). For genuinely subjective metrics, use a low-resolution scale (1–5, where 1 is most desirable) — but use this sparingly.
4. **Recommendation.** State which option, and why, in two or three sentences. A reader should be able to stop here.
5. **Metric Descriptions.** One or two sentences per metric defining what it means, so "Deployment Complexity" or "Ease of Use" can't be interpreted three different ways.

### Rules for building the matrix

- **No weighted summation.** Do not assign numeric weights to metrics and sum them to pick a "winner." This is an anti-pattern — it hides nuance behind a fake number and encourages gaming the metric list. The matrix is a springboard for thinking, not a calculator.
- **Provide actual values, not pass/fail.** If the constraint is "≥ 250 miles range," write "275 miles" and "212 miles" — not ✓ and ✗. The margin matters.
- **Describe the "why" in metric names.** "Real-time performance" or "library ecosystem breadth" — not "Java" or "Rust." Metrics name needs, not implementations.
- **Use quantifiable metrics where possible.** "Driving time in peak traffic: 45 min" beats "commute is bad." Quantification often reveals creative options (stagger the schedule, work-from-home days).
- **Prioritize ruthlessly.** Too many metrics paralyze the decision. Sort the matrix rows by priority and discuss top-down.
- **Consistent directionality.** If "high is bad" for one metric, make "high is bad" the convention across the matrix so the reader doesn't have to mentally switch directions row by row.
- **Round to readable precision.** Nearest thousand for prices, nearest hour for durations. Optimize for fast comprehension.
- **Consider a hybrid.** When two options are both strong, explicitly check whether a third option combining their strengths is available. The matrix often reveals these.

### Output

Produce the Decision Document as the description of a Linear issue (or as a comment on the parent project). Once the user accepts the recommendation, the **next action** implied by it goes into the inbox via the standard Capture → Clarify → Organize flow.

---

## Tool reference

All task state lives in Linear via the Linear MCP. Key tools:

- `list_issues` — filter by assignee, state, label, project, due date.
- `save_issue` — create or update an issue (capture, clarify renames, state changes).
- `list_projects` / `save_project` — manage projects (GTD projects = Linear projects).
- `list_issue_labels` / `create_issue_label` — manage context labels and `waiting-for` / `reference`.
- `save_comment` — for Decision Documents on a parent issue, or for notes during weekly review.
- `save_document` — for stand-alone Decision Documents that span multiple projects.

Before creating new labels or projects, check with `list_*` first to avoid duplicates.

## Standing conventions

- **Don't make the user remember what's in Linear.** Query it. If they ask "what's next," pull the data.
- **Confirm destructive actions** before executing — closing issues, deleting, bulk state changes.
- **One issue per outcome.** Don't fold multiple unrelated tasks into one issue's description.
- **Titles are actions, not topics.** "Email Sam re: Q3 budget" — not "Q3 budget."
- **Surface, don't withhold.** Overdue items, stalled projects, long-untouched Waiting-For items — mention them when they're relevant, don't wait to be asked.

## Self-update

The canonical version of this skill lives at <https://github.com/JRTaylord/Project-Management-Skill>. When the user asks to update this skill, fetch the latest `gtd-linear-SKILL.md` from that repo's `master` branch and replace this file with its contents.
