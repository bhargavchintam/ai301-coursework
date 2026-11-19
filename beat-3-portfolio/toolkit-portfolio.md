# Toolkit Portfolio

Path: `beat-3-portfolio/toolkit-portfolio.md`

Student: Bhargav Chintam

Note on this page: Assignment 6 was graded on `linkedin-post.md` and `technical-writeup.md` in this folder, and this page is not part of that grade. I keep it as the record of the six tools. The dates, excerpts and judgment calls below are mine; the sentences were drafted from my own logs and contribution story with Claude on 2026-11-18 and read through by me.

## Featured Tools

I chose the three tools that changed the most through use, and that all ran on the same real pull request: spack/spack-packages #7083, a one-line dependency split for the charliecloud package, opened on 2026-10-15 and merged on 2026-11-10. The voice-guard stopped my own words four times on that thread. The ship-gate's notes changed the pull request text three times and the commit once before it opened. The pr-monitor caught me breaking my own playbook. The scout and the three Beat 1 skills did their jobs with less friction, so they sit in the Inventory.

### voice-guard

What it is, and the judgment hole I wrote: A hook that fires before `gh issue comment`, `gh pr comment` and `gh pr create`, plus a manual doorway (`check_voice.py --draft FILE`) for anything I post from a browser. The judgment is `voice-guide.md`, my rules for claims, replies and pull request text, each rule with a Wrong and a Right example. Every stop and pass is written to `guard-log.md`.

How it evolved, dated: I installed it on 2026-10-14 (Unit 6), and the latch test stopped `gh issue comment --help` at the door. The same evening it stopped my first claim for #5400 at 18:02 with the rule "show the output, do not describe it"; I pasted the real `spack spec` run and it passed at 18:07. On 2026-10-16 plan-check flagged "this week" in the posted claim, a timeframe the guard had let through because the guide's own Right example contained one, so I added a timeframe rule with a Wrong and Right pair taken from my own comment; the guard then stopped the old claim text at 18:23 and I edited the posted claim in place on 2026-10-20. On 2026-10-27 it stopped my reply to another contributor until the exact API output sat beside the claim. On 2026-10-28, after an nf-core maintainer answered a 340-word claim of mine with "please don't just copy-paste llm output into comments", I added the rule "short and human, under about 120 words" and cut three posted claims in place to between 111 and 130 words the same day.

Evidence:

```
guard-log.md
- 2026-10-14 18:02 [manual] stop: rule broken: "show the output, do not describe it" | target: claim-5400.md
- 2026-10-14 18:07 [manual] pass | target: claim-5400.md
- 2026-10-16 18:23 [manual] stop: rule broken: "a claim names the fix I will attempt and my first step, not a promise of a PR" | target: claim-5400.md
- 2026-10-27 00:33 [manual] stop: rule broken: "When I say something happened, the exact command and its output are in the same comment." | target: reply-dcchoudhury15.md
- 2026-10-28 04:03 [manual] stop: rule broken: "short and human, under about 120 words (added 2026-10-28 after a maintainer's note)" | target: lqai/claim-587-short.md
```

The timeframe rule as it reads in `voice-guide.md` since 2026-10-16:

```
- Wrong (timeframe, added 2026-10-16 after my own claim on spack-packages #5400 carried one): "I expect to have a draft PR up this week."
- Right: "My first step is the one-line split and a `spack spec` check on a branch of my fork; I will post the PR link here when it is open."
```

### ship-gate

What it is, and the judgment hole I wrote: A gate on `gh pr create` that I also run by hand (`check_gate.py --branch --base develop --title ... --body-file pr_draft.md --issue URL`). It runs my `ship-manifest.md`, the must-pass checks I mined from the repo's own CI plus dated exclusions, and then my pr-precheck rubric in live mode on the real branch. Every verdict is written to `gate-log.md`.

How it evolved, dated: Installed on 2026-10-14 (Unit 7); the canary `gh pr create --help` was blocked at 18:15. The manifest had four rows: CI's two ruff commands with CI's pinned ruff 0.15.11 through `uvx`, vermin at the 3.6 floor, and a one-file diff check, with `spack audit` and `spack spec` as dated exclusions because charliecloud refuses macOS and those ran in a Linux container. Four runs that evening (18:18, 18:21, 18:23, 18:25) all passed the manifest and the rubric, but runs 1 to 3 carried voice notes that changed the package each time: I pasted real output under every control run instead of a one-line summary, ran two more `spack spec` commands to prove the `@main` case instead of asserting it, and amended the commit because the code comment stated as fact what the maintainer had only reported. After the grader's feedback on 2026-10-16 (stop copying state between machines) the checks became one script, `check-5400.sh`, on my Mac's Linux VM, with the working files at the clone root. A retroactive run on 2026-10-27 found an Oct 7 comment on the issue from another contributor that GitHub had hidden on claim day, and I answered it the same day. The gate then ran on two more branches, nf-core/modules and LegalQuants/lq-ai, where the manifest grew a `check-dco.sh` row for the DCO sign-off and for keeping assistant trailers out of commits.

Evidence: the voice report from gate run 1 (2026-10-14 18:18) against run 4 (18:25):

```
run 1: - Show the output, do not describe it:
  - The quoted line is "`$ spack spec charliecloud@0.44            # unchanged: still picks squashfuse@0.6.1, exit=0`". The other four control lines are written the same way: an inline comment and an exit code, but no pasted output.
  - Say what I do not know: "`@main` is a branch version and falls under the new rule." This is stated as fact, but the `@main` run shows a different failure.
run 4: **Voice guide: no rule broken.** ... pastes output next to every claim; marks "I have not tested that myself" and "I expect" as unverified; lists exactly what the diff contains; carries no promises, dates or checkbox ticks.
```

The commit comment before and after the amend (1ffd8f52 to c56a1e88, 2026-10-14):

```
-        # 0.44 enabled multi-threaded FUSE, which older SquashFUSE does not handle well;
+        # 0.44 enabled multi-threaded FUSE, which the Charliecloud maintainers report
+        # older SquashFUSE seems not to support well; see
```

### pr-monitor

What it is, and the judgment hole I wrote: A runner (`monitor.py`) that reads a pull request's live state through read-only `gh` calls, with `--state` as a free canary, folds in my `playbook.md` (response rules, staleness thresholds, nudge etiquette, walk-away criteria) and drafts the matching move to `drafted-move.md`. It posts nothing; anything that reaches a maintainer goes through the guard by my hand.

How it evolved, dated: Installed on 2026-10-27 (Unit 8). I measured the repo before writing thresholds: among the last 25 merged package PRs from repeat contributors the median first human response was 0.4 days and the median merge 1.9, but 9 of 17 open first-timer PRs had no reply yet, the oldest at 14.5 days. So the playbook says 21 days of quiet before a first nudge, 14 after an answered round, one nudge per PR ever, and a 90-day walk-away with a dated note. The first run on #7083 (11 days, no comments) drafted "No nudge. Wait." The same day it ran on the emfcamp PR the maintainer had just closed as already fixed, classified it as my walk-away case and drafted the close-out, which I posted as one sentence. On 2026-10-28 a comment under my name asked the maintainers to approve the CI run on day 13, before my own line. The second run on 2026-11-04, after j-ogas's approval, drafted "No reply ... this is a wait" and named that day-13 ask as the one nudge my playbook allows. Nothing changed in the tool; what changed was the Learnings line in my contribution story and the rule I now hold to.

Evidence: `drafted-move.md` from the 2026-11-04 run on #7083:

```
MOVE: No reply. j-ogas's approval has nothing to answer, so this is a wait.
- A nudge needs something new to bring, and quiet days alone never earn one. You have nothing new, and your one nudge may be spent already. Your 7-day-old comment asking a maintainer to approve the `ci` run reads like it, so check before any future nudge.
```

## Inventory

- issue-scout: the Unit 1 issue-select skill grown in Unit 5 into a GitHub scout with a search strategy written from my fit profile and a rubric patched to `scope-feasible` for real repos; Beat 1 eval 18 of 20; it accepted spack #5400 and emfcamp #1779, and after emfcamp closed #1779 as already fixed it gained one lesson, read the commit log for the function an issue names before claiming.
- repro-check: the Unit 2 grading skill with my claim voice guide; final eval 19 of 20; run in live mode on the #5400 claim before it was posted.
- plan-check: the Unit 3 skill; final eval 19 of 20 with pkg-14 held as an explained disagreement; in live mode it accepted the plans for #5400 and lq-ai #587 and caught the timeframe the guard had passed.
- pr-precheck: the Unit 4 skill with a 13-check rubric; final eval 19 of 20 with pkg-11 held; it now runs inside every ship-gate run as the second half of the verdict.

## The Arc

Beat 1
Date: 2026-09 (Units 1 and 2)
Friction: The grader's feedback on Assignments 1 and 2 named two habits in my rubrics: joining two concerns with an AND inside one check, so a disagreement could not show which part fired, and pass conditions a second person could not apply the same way.
Change: I split every check to one concern with its own name and threshold, and rewrote pass conditions the way a unit-test assertion reads, then carried both habits into plan-check, pr-precheck and the scout rubric.
Result: Assignments 1 and 2 scored 25 of 25 each, and the Unit 3 and Unit 4 evals both reached 19 of 20.

Beat 2
Date: 2026-10-14
Friction: My first claim for #5400 described the control runs in one-line comments instead of showing them; the guard stopped it at 18:02.
Change: I pasted the real output under each command, and applied the same rule to the pull request text after gate run 1 said the same thing about the five controls.
Result: The claim went up at 18:07, gate run 4 passed with no voice note, and the pull request opened on 2026-10-15.

Beat 3
Date: 2026-10-16
Friction: plan-check flagged "this week" in my posted claim; the guard had passed it because its own Right example contained a timeframe.
Change: I added a timeframe rule to the voice guide with a Wrong and Right pair taken from my own comment, and edited the posted claim in place on 2026-10-20.
Result: The guard stopped the old claim text the same day, and no later claim of mine carried a timeframe.

Beat 4
Date: 2026-10-27
Friction: A retroactive gate run found an Oct 7 comment on #5400 from another would-be contributor that GitHub had hidden on claim day; the same day emfcamp closed #1779 and my PR #2391 as already fixed in March.
Change: I replied on the issue with the Oct 14 API output showing zero comments at claim time and offered to close my PR if the maintainers preferred; the scout's patch list gained "read the commit log for the function the issue names before claiming".
Result: No maintainer objected, and j-ogas approved #7083 on 2026-10-28.

Beat 5
Date: 2026-10-28
Friction: An nf-core maintainer answered my 340-word claim with "please don't just copy-paste llm output into comments", then said the same of the pull request description.
Change: I added the rule "short and human, under about 120 words", cut three posted claims in place to between 111 and 130 words, and cut the description from 669 words to about 120 visible words with the commands in a collapsed block.
Result: The same maintainer rebased my branch and left one small suggestion on 2026-11-02, which I applied on 2026-11-04; CI went green.

Beat 6
Date: 2026-11-04
Friction: The monitor's second run named my day-13 ask for CI approval as the one nudge the playbook allows.
Change: Nothing in the tool; I wrote the lesson into the contribution story and held to the rule afterwards.
Result: #7083 merged on 2026-11-10 with no change ever requested, and lq-ai #783 merged on 2026-11-07.

## Limitations and What's Next

The hooks only fire inside a Claude Code session, so a post from the browser or a plain shell depends on me remembering the manual doorway. Each tool reads one artifact, a draft, a branch or a pull request, and none of them reads the playbook before a comment goes out, which is how the day-13 ask happened. The gate cannot see linked pull requests on an issue and asks me to check by hand every time. For one repo the checks ran in a container the gate could not reach, so the manifest carried dated exclusions until a script on my Linux VM replaced them. Next I would make the guard read the playbook on any pull request comment, add a linked-PRs read to the gate's scope report, and give the monitor a `--state` line that names which check is pending and whether one approval satisfies the repo's review rule.

Tool limits.

voice-guard: It checks words against rules whose examples I wrote, so it passed a timeframe that its own Right example contained, and it knows nothing about the thread's rhythm.
ship-gate: It cannot see linked pull requests on the issue and relies on a manual check, and its hook only fires inside a Claude Code session.
pr-monitor: It cannot tell which check is pending or whether one approval meets the repo's review rule, and it does not stop a comment that breaks the playbook; it only names it afterwards.
