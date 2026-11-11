# Task prompt template

What goes in the `prompt` field of `mcp__ccd_session__spawn_task`.

**The spawned session cannot see your conversation.** It has no memory of the
plan, the research, or why the task exists. Everything it needs is in this
prompt. A prompt that triggers a question has failed, even if every line is true.

Before you spawn, read it as the session would and ask: *what would I have to
come back and ask?* Then answer that.

---

## The card fields

```
title   Under 60 chars. Imperative, starts with a verb.
        "Fix the double-pay refund"   not   "Refund bug"

tldr    One or two plain sentences, no paths or code. LEAD WITH THE SIZE TAG.
        "HEAVY. The refund path can pay out twice under a retry; this fixes it
         and proves it with a failing test first."

cwd     Only when the work belongs in a different repo on this machine.
```

---

## The prompt

```markdown
## GOAL

<What is true when this is done. One or two sentences. Not a list of steps 
the outcome, so the session can tell whether it got there.>

## CONTEXT

<What the product is, in two sentences. Then what is already true in the code
this task touches: the shape of the thing, what works, what does not, what was
tried. Enough that the session never has to guess or ask.>

<If the task exists because of a bug: how it actually fails, and how to
reproduce it. Not the theory, the reproduction.>

## YOUR FILE SCOPE

You may create and edit ONLY these paths:

- `path/to/file.ts`
- `path/to/dir/**`
- `tests/path/**`

**Do not edit anything outside this list.** Another session may be working in
the same repo, and files outside your scope either belong to it or to the CTO.

**If you need a shared file changed** the composition root, a shared type, a
config, a migration, the lockfile, **do not change it. Build what you can
around it, and report the exact change you need.** The CTO makes it.

## CONSTRAINTS

- Versions, pinned: <library@version, framework@version, checked <date>>
- <Patterns to follow, with the file that shows the pattern.>
- <What not to do, and why.>

## DECISIONS ALREADY MADE: do not re-open

<Every choice already settled, WITH ITS REASON. A ruling without a reason gets
re-litigated; a ruling with one gets followed. Include the ones that seem
obvious, they are not obvious to a session that has not read what you read.>

- **<decision>** because <reason>.

## DEFAULTS: proceed without asking

<For every judgement call you can anticipate: "If X, do Y. Do not ask." Give a
default even where you are unsure, and mark it provisional. A wrong default
costs one revision; a question costs a round trip AND the same revision.>

- **If <situation>** → <do this>. Do not ask.
- **If <situation>** → <do this>. Provisional; flag it in your report.

## RESEARCH FIRST: do not start building

- **Reproduce the problem.** Verify every claim above against the actual code.
  **This prompt may be wrong.** If you disagree after reading the code, say so
  in your report and say why. That is the job, not insubordination.
- **Check the versions.** For anything not pinned above, find the current
  release from the registry or official docs, not from memory, which is stale.
  Record the version and the date. Follow an existing pin and report the gap;
  upgrading is a decision, not a side effect.
- **Research the whole task before building any of it.** Finding the second
  unknown after you have built around the first is what forces a restart.
- **Research options before you would ask about them.** Then decide, or report a
  recommendation with what you did by default, never an abstract question.

## ACCEPTANCE CHECK

Done means all of these pass. Run them yourself before reporting:

```
<exact command>          # expect: <what pass looks like>
<exact command>          # expect: <what pass looks like>
```

<Plus any behaviour check that is not a test: a probe, a page that must render
an endpoint that must return a shape.>

## PROVE THE GUARD

<For any fix or guard: re-introduce the defect, watch the test go RED, restore
watch it go green. Say so in the commit body. A test that has never failed
proves nothing.>

<Then grep for the sibling call site, a fix applied to one call site while an
identical one goes untouched is the most common repeat defect here.>

## GIT RULES

- Branch: **`task/<name>`**. Create it from the current HEAD and stay on it.
- **Commit as you go.** The commit message carries the mechanism you closed and
  the mutation you watched go red, it is the evidence the reviewer reads.
- **Never `git push`.** Nothing reaches the remote by your hand.
- **Never touch `main` or `staging`.** No merge, rebase or reset.
- **`git add` explicit paths only** never `git add -A` or `git add .`.
- Check the branch before every commit.
- **Never `git checkout --` on an uncommitted tree.**

## REPORT BACK

End with a report containing:

- **Every file you changed and what changed in each.**
- **The mutation you watched go red** for each guard you added.
- **What this prompt got wrong** say it plainly.
- **Any shared file you need changed** with the exact change.
- `git status --porcelain` output.
- Test counts vs the baseline, and whether the typecheck is clean.
- Anything you could not verify, labelled as such.

Do not push. Do not deploy. The CTO reviews, merges and ships.
```

---

## Notes

**Size the prompt to the task.** A LIGHT copy fix does not need the full
scaffold, goal, scope, acceptance check and git rules are enough. A HEAVY money
task needs all of it, and the decisions section is where it earns its keep.

**The file scope is the load-bearing part.** Everything else degrades
gracefully; an overlapping scope produces two sessions writing contradictory
versions of the same intent, which no review catches cheaply.

**Pin versions in the prompt, not in the session's judgement.** Parallel tasks
that each pick their own version produce branches that cannot merge.
