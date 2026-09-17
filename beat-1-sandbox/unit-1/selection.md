# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53

**Verdict output**

Live-mode run on 2026-09-16 over three candidates (#53, #60, #68). The full output is pasted below without edits. Issue #53 is the first object in the JSON array and its verdict is `accept`.

**The verdict must record `accept` for this issue.** Choose an issue your own skill
accepts. If your skill rejects every candidate you try, that is a signal about your
rubric rather than about the issues: revise it and re-run — retries are unlimited and a
partial re-run costs about $0.20 — or run the skill on different candidates. Output
recording `reject` for the issue you chose earns no credit for this field.

````
## Summary

All three candidates are strong first issues from the same classroom repo (`codepath/pathreview-ai301-fa26-s3`), all opened by the same COLLABORATOR six days ago with the `good first issue` + `bug` labels, no assignees, no linked PRs, and no comments. Repo-level facts (maintainer alive, repo in use, no AI-contribution ban, maintainer response within 7 days on a sampled issue, but zero GitHub releases) are identical across all three.

**Ranked (all accepted):**

1. **#53 — PII scrubber fails to redact parenthesized US phone numbers** — runnable repro snippet, 4 named failing tests give a very concrete "done," single regex fix in `safety/pii_scrubber.py`. Best fit for a bounded evening's work with unambiguous verification.
2. **#60 — Faithfulness checker crashes when a context chunk has `text: None`** — runnable repro snippet, 1 named test, likely a one-line default-handling fix in `rag/evaluator/faithfulness_checker.py`. Smallest of the three.
3. **#68 — Keyword search raises `ZeroDivisionError` when the index is empty** — clearly described defect with relevant files and a 2–4 hr estimate, but no runnable repro snippet (narrative description only), which is the one thing that puts it slightly behind the other two under your "clear steps" preference.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53",
    "checks": [
      {"name": "maintainer-alive", "grade": "pass", "evidence": "5/5 recent default-branch commits by human 'Aburke225', latest 2026-09-16, within 90 days"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "archived: false; last push 2026-09-16 (today)"},
      {"name": "spec-present", "grade": "pass", "evidence": "Opened by COLLABORATOR 'Aburke225' with runnable repro code + observed error output"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Single regex fix in pii_scrubber.py for one phone format; not an umbrella issue"},
      {"name": "no-failed-attempts", "grade": "pass", "evidence": "No linked/cross-referenced PRs found in issue timeline"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: []; 0 comments; no linked PRs"},
      {"name": "ai-policy-allows", "grade": "pass", "evidence": "docs/CONTRIBUTING.md has no AI-related restriction; no AI_POLICY.md/AGENTS.md at root"},
      {"name": "maintainer-endorsed", "grade": "pass", "evidence": "Opener is COLLABORATOR and issue carries 'good first issue' label"},
      {"name": "responds-fast", "grade": "pass", "evidence": "Issue #52 (opened 2026-09-10) got a COLLABORATOR reply on 2026-09-16, 6 days"},
      {"name": "ships-releases", "grade": "fail", "evidence": "0 releases found via GitHub releases API"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/60",
    "checks": [
      {"name": "maintainer-alive", "grade": "pass", "evidence": "Same repo-level evidence as #53"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "Same repo-level evidence as #53"},
      {"name": "spec-present", "grade": "pass", "evidence": "Opened by COLLABORATOR with runnable repro snippet showing TypeError and the failing test name"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Single default-value handling fix in faithfulness_checker.py"},
      {"name": "no-failed-attempts", "grade": "pass", "evidence": "No linked/cross-referenced PRs found"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: []; 0 comments; no linked PRs"},
      {"name": "ai-policy-allows", "grade": "pass", "evidence": "Same repo-level evidence as #53"},
      {"name": "maintainer-endorsed", "grade": "pass", "evidence": "Opener is COLLABORATOR and issue carries 'good first issue' label"},
      {"name": "responds-fast", "grade": "pass", "evidence": "Same repo-level sample as #53"},
      {"name": "ships-releases", "grade": "fail", "evidence": "0 releases found"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68",
    "checks": [
      {"name": "maintainer-alive", "grade": "pass", "evidence": "Same repo-level evidence as #53"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "Same repo-level evidence as #53"},
      {"name": "spec-present", "grade": "pass", "evidence": "Opened by COLLABORATOR; body names the exact trigger (`index([])`) and exception (`ZeroDivisionError`) plus relevant files"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Single guard fix in keyword_search.py, estimated 2-4 hours"},
      {"name": "no-failed-attempts", "grade": "pass", "evidence": "No linked/cross-referenced PRs found"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: []; 0 comments; no linked PRs"},
      {"name": "ai-policy-allows", "grade": "pass", "evidence": "Same repo-level evidence as #53"},
      {"name": "maintainer-endorsed", "grade": "pass", "evidence": "Opener is COLLABORATOR and issue carries 'good first issue' label"},
      {"name": "responds-fast", "grade": "pass", "evidence": "Same repo-level sample as #53"},
      {"name": "ships-releases", "grade": "fail", "evidence": "0 releases found"}
    ],
    "verdict": "accept"
  }
]
```
````

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

Three runs, all on 2026-09-16, in this order:

1. Full run, first rubric: `agreement: 16/20 scored items  (bar: 18/20: below the bar)`. Categories line: `categories: claimed 4/4  clear-accept 4/8  dead-repo 3/3  policy 1/1  scope 4/4`. The four misses were issue-01, issue-04, issue-09 and issue-19. All four were gold `accept` and my rubric said `reject`.
2. Partial run after I reworded two checks, `--only issue-01,issue-04,issue-20`: `agreement: 3/3 scored items`. issue-20 was already correct. I re-ran it as a canary to see if the looser wording let it in.
3. Full confirming run, same rubric as run 2, saved with `--save-run`: `agreement: 18/20 scored items  (bar: 18/20: PASS)`. Categories line: `categories: claimed 4/4  clear-accept 6/8  dead-repo 3/3  policy 1/1  scope 4/4`. This is the committed `eval-run.txt`.

**Issue analysis**

`issue-09` (conda/conda#7617, "conda config clear option"). My rubric: `reject`. Gold label: `accept`. It was the same in run 1 and run 3, and I left it that way on purpose.

One required check decided it. The harness note reads `failed: no-failed-attempts, responds-fast (preferred)`, and the grader's evidence for the required one was: "Opened 2018-08-03 (>3 years before 2026-08-05 capture) with linked PR #11627 closed without merge." The bundle's repo facts say `this issue: assignees: none; linked PRs: conda/conda#11627 (closed)`, and the issue line says `opened by jakirkham (MEMBER) on 2018-08-03`. My check fails an issue that is more than 3 years old and has at least 1 linked PR closed without merge, so this one fails. Every other required check passed. For example `unclaimed` passed with "only claim comment is from 2022-01-20, well outside the 60-day window".

The gold note says "old but valid bounded feature; the 2022 claim is stale and the maintainer invited takers". That is a fair reading. In the thread the maintainer answered the 2022 claim with "Think you can just give it a try if you are interested." and posted "(bump)" on 2023-01-21. My check does not look at the thread at all. It only counts age and closed PRs, so it cannot see a maintainer keeping the door open. I kept the check because an eight-year-old issue where the one attempt died tells me the work is harder than it looks, and I have few spare hours. I accept that this costs me issue-09.

**Check rationale**

The `scope-bounded` row, quoted from `tools/issue-select/rubric.md`:

> | scope-bounded | The issue title, body, labels and the full comment thread | The issue asks for one bounded change. Grade the size of the work asked for, not the length or polish of the write-up: a short maintainer-filed issue passes, and a trailing "etc." on a list of examples of the same small fix does not fail. Several edits that together make one change (for example one new docs page plus pointers to it from existing pages) count as one bounded change. It fails if it is an umbrella, tracking or "mega" issue meant to be split into separate pieces of work; if it is a usage or support question; if a maintainer says the fix needs changes to core internals; if the fix asks for performance, concurrency or architecture work (threads, multi-processing, changing an algorithm's complexity); or if the approach is still being debated in the thread and no maintainer has stated the accepted approach. | required |

Why it reads this way. In run 1 the first wording of this check failed `issue-04`, a one-line bug filed by a COLLABORATOR with a `good first issue` label. The body is only "Including remove identity, fuse spiders, remove self loops, etc." and the grader's evidence was "Body's trailing 'etc.' leaves the list of missing rule previews open-ended with no maintainer-stated bounded set." It also failed `issue-01`, a docs task, with the evidence "body lists five distinct changes ... an umbrella of sub-items". Both times the check was grading how the write-up looked, not how much work was asked for. The course evidence guide warns about this: "Grade the size of the work being asked for, not the polish of the writeup." So I added the sentence about size over polish, the "etc." sentence, and the sentence that several edits making one change count as one bounded change. I also wrote down what "too big" means for me: performance, concurrency or architecture work fails. That part is my own limit, because I work full time and study.

**Trade-offs**

The new wording changed two results: `issue-01` and `issue-04` went from `reject` in run 1 to `accept` in run 2 and run 3. Loosening a scope check can let bad issues in, so I re-ran `issue-20` with `--only` as a canary. It is a well-written feature wish opened by `cursor[bot]`, and it still came back `reject`.

What the check gives up is `issue-19` (zxcalc/zxlive#517, "Selecting large subgraphs in proof mode freezes the UI"). Gold is `accept` and my rubric says `reject` in every run. In run 3 the grader's evidence was "explicit concurrency/architecture work bundled with multiple candidate fixes". The body asks to fix matchers that are "quadratic instead of linear" and suggests "multi-processing to use all the cores". The staff note calls it a "maintainer-diagnosed performance bug with named causes", which is true. I still would not take it as a first contribution in the hours I have, so I accept this miss. The rule will also reject any small performance fix that only sounds big, because it looks at the kind of work and not the line count.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. Fit and time. The issue is in Python, in one file (`safety/pii_scrubber.py`), and it is about a regex that misses the phone format `(555) 123-4567`. My work background is Python data and ML pipelines, where cleaning personal data out of text is a normal task, so I care about this kind of bug. The issue names four failing tests in `tests/unit/test_pii_scrubber.py`, so I can tell when I am done. With a full-time job and graduate classes I have a few evenings a week, and this fits in them.

2. What the verdict got right, and what it could not weigh. It was right that the repo is alive (five human commits, the newest on 2026-09-16), that nobody holds the issue (no assignee, no comments, no linked PR), and that the body has steps I can run. What it could not do is separate the three candidates. #53, #60 and #68 got the same grade on every check, so the rubric gave me three accepts and no ranking of its own. The order came from my fit profile. I also weighed things the rubric has no check for: four named tests give a clearer finish line than one test, and a regex fix can grow if I start adding other phone formats, so I will have to keep the change to the format in the issue. The one failed check, `ships-releases`, means little here because Path Review is a course repo that does not publish releases. It is only a preferred check, so it did not change the verdict.

3. Difficulty in claiming. The issue is tier-1 with a `good first issue` label, so I expect several classmates to pick it. The Path Review house rule says their claim comments do not block me, but it means my claim comment has to be specific and not just "I'll take this". I have not set up the project yet, so I do not know if the four tests fail on my machine the way the issue says. I will find that out in Unit 2 before I post anything. I have not commented on the issue.

Note on AI use: I used Claude to draft the rubric wording, run the harness and the live-mode command, and organize this write-up from the run records. I chose which disagreements to fix (issue-01, issue-04) and which to hold (issue-09, issue-19).

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
