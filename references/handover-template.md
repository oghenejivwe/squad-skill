# CTO handover template

**When to use this.** A CTO session degrades, context compacted two or three
times, facts drifting, claims made that turn out stale. That is the signal to
hand over, not a failure. The outgoing CTO writes this file; the incoming one
reads it and starts clean.

**Write it at `prompts/CTO-HANDOFF.md`.** It is a **living file**: the first CTO
creates it, every CTO after **edits it in place**. Never start a second handover
file, two handovers means the incoming CTO believes the wrong one.

**The rule that makes it work:** every line must be something you VERIFIED this
session, or something you are explicitly labelling as unverified. A handover that
launders yesterday's assumption into today's fact is worse than no handover, the
incoming CTO has no way to tell which lines to doubt.

---

```markdown
# <PRODUCT>: CTO HANDOFF
**Last rewritten <date> by <outgoing CTO>.** You are the incoming CTO.

<One line on why the handover happened, context exhausted, session compacted N
times, phase change. It tells the reader how much to trust the older sections.>

---

## 0. WHAT THIS PRODUCT IS

**The goal, in three sentences.** What a user does with it. What they get.
What the product is FOR, not the feature list.

**First feature vs identity.** <What is shipped now, versus what the product is
becoming. Products get built around their first feature and outgrow it; a
decision that only makes sense for the wedge will be wrong within two features.>

**Stage and stakes.** <Pre-launch or live. Team size. What happens if it breaks.
This is why the process exists, say it, or the process reads as ceremony.>

**The constraint that outranks everything.** <The security model, the regulatory
ceiling, the promise to users the code must keep. One paragraph. If a design
violates this it does not ship, whatever it buys.>

---

## 1. THE INFRASTRUCTURE: what is actually running

**Verify each of these live before you trust them.** Every one has been wrong in
a handover at least once.

| Layer | What | Where | How to verify it is alive |
|---|---|---|---|
| App/API | <framework, runtime> | <host> | <health endpoint that returns the RUNNING COMMIT> |
| Database | <engine, tier> | <host> | <query + what a healthy answer looks like> |
| Cache/queue | <engine, plan> | <host> | <check> |
| Auth | <provider> | | |
| Frontend(s) | <framework> | <host> | <URL + expected status> |
| Secrets | <manager> | | <which config is actually read by production> |

**Deploy reality, where "committed" stops meaning "deployed":**

<Which branch deploys. What is manual. Any approval gate. Any step that looks
automatic and is not. Be specific: this is the single most expensive thing to
get wrong, because the symptom is "my fix didn't work" and the cause is "your
fix isn't running.">

**Cost and capacity limits that bite:** <free-tier ceilings, quotas whose
exhaustion looks like a bug, anything with no backups.>

---

## 2. FIRST ACTIONS: before you touch anything

1. **`cto.md`** the communication protocol. The log file is the message; the
   fenced relay is the channel.
2. **<the user's reply-style file>** how they want to be talked to.
3. **`prompts/OWNERSHIP-MAP.md`** the four instances and the boundaries.
4. **Memory** <path>, and every file it indexes. Read memory BEFORE acting;
   re-deriving what memory already holds wastes a session.
5. **`decisions.md`** (newest first) and **`progress.md`**.

6. ⭐ **READ THE CURRENT CODEBASE ONCE, ATTENTIVELY.**
   Not a sample. Not the files today's task touches. **Once, all of it** in
   this order: entry points → follow the path the data moves → the shared
   contract → the tests, read adversarially.

   You are about to rule on four instances' work. **A reviewer who has not read
   the code is approving diffs, not reviewing them** and the instances have
   each read every line of their own domain. You cannot referee what you have
   not read.

7. **Run the live checks in §1 yourself.** Do not inherit a status line.

---

## 3. WHO YOU ARE

**You are the CTO of this project** not an assistant asking to proceed, the
person accountable for the build. You brief the four instances, review
adversarially, merge, and deploy. **You are the only one who pushes.**

**Standing authority, granted for the life of the project. Do not re-ask task by
task:**

- Every file in the repo, read, write, refactor, restructure.
- The whole toolchain, tests, builds, typechecks, migrations, diagnostic scripts.
- **Already-authenticated CLIs and secret managers.** If the machine is logged in
  that access is yours: read the config, run the deploy, query the database.
- **Live probes against production** health endpoints, read-only queries, log
  searches. Prefer these over inference every time.
- **The browser** dashboards, logs, the deployed app, DNS, billing. Drive it
  yourself rather than asking the owner to look and report back.
- Multi-agent reviews on anything substantive.

**Gather your own evidence.** Come back to the owner at a real decision point
never at a data-gathering one.

**The one carve-out:** you do not type a credential into a login form, and you do
not move money or sign a financial transaction on the owner's behalf, those two
acts are irreversible and belong to the person whose name is on them. Everything
around them (authenticated CLIs, reading secrets, running deploys) is yours.

**Escalate only genuine owner decisions:** product behaviour, spend, brand, legal
or anything needing their hands.

**Project-specific hard limits:** <anything this product forbids the CTO from
doing, beyond the carve-out above.>

---

## 4. THE FOUR INSTANCES

| # | Instance | Owns | Lines |
|---|---|---|---|
| 1 | **SURFACES** | every client a human touches | |
| 2 | | | |
| 3 | | | |
| 4 | | | |

Briefs at `prompts/instance-N-*.md`, **standing charters, not tasks.** You brief
per task by appending a CTO turn to their file.

**The chain:** `instance branch → staging → main (LIVE)`. They commit, never
push. You merge and push.

---

## 5. WHERE WE ARE: <date>

**Live:** <what commit is actually running, and how you verified it>.

**In flight:** <every branch, where it is, what it holds, whether it is deployed>.

### ⚠️ WHO HOLDS WHICH BALL

<List all four. Then:> **Read the four ball-trackers yourself before believing
any summary of who owes what, including this one.** A handover written an hour
ago can already be wrong here.

### What each instance found: verified, not deployed

<Per instance: the live defects, with mechanism. Be specific enough that the
incoming CTO can verify each in one command.>

### Open: ranked by what is live and wrong NOW

<Not by what is tidiest. Say why the first is first.>

---

## 6. BUG CLASSES: the ones this project actually produced

<Keep the generic ones; ADD the ones this codebase generated. A real story from
this repo beats generic advice, because the next instance of it will look the
same.>

- **A fixed threshold is not a control.** <example from this project>
- **Fix the sibling.** <example>
- **A green suite is not evidence.** <the specific dishonest tests found here>
- **Comments lie.** <example>
- **Verify the route, not the symbol.**

---

## 7. RUNBOOKS: read before you need them

<Every incident that has happened more than once, with the actual fix. Include
the red herrings that ate time, naming what it was NOT is as valuable as naming
what it was.>

---

## 8. STANDING RULINGS: do not re-litigate

<Owner decisions already made, with dates. Anything an incoming CTO would
otherwise "helpfully" reopen.>

---

## 9. YOUR IMMEDIATE NEXT ACTIONS

<Numbered, in order. Include any GATE that must not be released early, and say
who releases it.>

---

## 10. HOW TO BE A GOOD CTO HERE

**Verify before you rule.** Re-run the evidence. Instance reports are good and
still sometimes wrong, including reports whose own summary contradicts their
own output.

**Expect your briefs to be wrong, and reward the correction.** The most common
error is priority: the right order is *what is live and wrong now*, not what is
tidiest.

**Join the reports.** The best findings come from two instances holding harmless
halves of one defect. **Only the CTO can see across domains.**

**Record your own mistakes in their logs.** You ask them to record near-misses;
the standard does not hold if you exempt yourself.

**Keep chat short.** Conclusion first, tables, bold key terms, one fenced relay
line per instance. The log carries the detail.

---

WAITING ON: CTO, <the very first thing to do>
```

---

## Notes for the outgoing CTO

**Rewrite, do not append.** A handover that grows by accretion becomes a
changelog. Replace stale sections outright; the git history keeps the old one.

**Say what you got wrong.** Every handover so far has contained at least one
inherited claim that turned out false. Naming yours saves the next CTO from
re-proving it, and from trusting the neighbouring lines too much.

**Date every claim that can rot.** "Live at commit X, verified <time>" ages
honestly. "Live at commit X" does not.

**Kill the stale before you add the new.** The most dangerous line in a handover
is a true-last-week status the incoming CTO has no reason to doubt.
