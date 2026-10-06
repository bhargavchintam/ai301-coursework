# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/95

Opened on 2026-10-05 from my fork, against `codepath/pathreview-ai301-fa26-s3` `main`. The description uses the repo's PR template (`.github/PULL_REQUEST_TEMPLATE.md`) with every section filled: Summary, Issue (`Closes #53`), Changes, Testing (the repro snippet before and after, `make test-unit`, `make lint`, `black --check`, the mypy runs, and the integration-suite exit code, each with its output), Screenshots / Demo, and Notes for Reviewers (the recorded deviation from the plan, the fifth `xfail` marker that stays, the wider-match cost, the two classmate PRs on the same issue, and an AI-assistance disclosure). The plan it implements is my Unit 3 plan comment: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53#issuecomment-5853463361.

My own pr-precheck verdict on the draft: `accept`, from live mode run in the fork working copy on the branch, against `plan.md` (with its Deviations section), `git diff main...HEAD`, `pr_draft.md` and `test_evidence.md`, four times in all.

Run 1 (before opening) gave `accept` on all eleven required checks but reported three things to fix before opening: a plan-comment link in the draft that returned 404 (I had pasted the wrong `issuecomment` id), a ticked "Type checker passes (`make typecheck`)" box next to a sentence saying `make typecheck` fails in my venv (the voice guide's "evidence is pasted, with the command that made it" rule), and a disclosure sentence that said Claude helped draft the description and then "the words here are mine". I fixed all three: the right link; the typecheck box left unticked with the exact mypy commands I did run and their output; the disclosure reworded to "I edited this description myself and stand behind every sentence in it". Run 2 (before opening) gave `accept` with nothing to fix before opening, and pointed at one soft spot that broke no rule: a ticked "Integration tests pass" box next to `no tests ran`, exit 5. I left that box unticked with its reason and opened the PR with that text. Run 3 (after opening, on the exact text that went up) gave `accept` with no fix-before-opening items. Once all five CI jobs passed on the PR, I ticked the CI, integration and typecheck boxes with the run link, as the description had said I would, and run 4 on that final text gave `accept` again. Its summary line: "All eleven required checks pass. Both preferred checks pass too, so there is nothing to tighten before you submit." The JSON block of that run lists every check as `pass`, with evidence such as "Hunks are the phone_us line (Scope) and the four xfail removals (Scope/Files); the (?<!\\w) form is named as a departure in plan Deviations and in the description." for `diff-within-plan`.

CI on the PR: `frontend`, `lint`, `test-integration`, `test-unit` and `typecheck` all passed (https://github.com/codepath/pathreview-ai301-fa26-s3/actions/runs/37287153184).

**Branch**

`fix/53-parenthesized-phone` on my fork `bhargavchintam/pathreview-ai301-fa26-s3` (one commit, `afd68d1`; `git diff main...HEAD --stat` is `2 files changed, 1 insertion(+), 17 deletions(-)`).

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Four runs on 2026-10-05, in this order, all with `--include-calibration` so the four worksheet packages were graded alongside (they are never scored):

1. Full run, first version of SKILL.md, rubric.md, procedure.md and the evidence guide: `agreement: 16/20 scored items  (bar: 18/20: below the bar)`, with `categories: clear-accept 3/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`. The four misses were pkg-05, pkg-13, pkg-16 and pkg-19, all gold `accept`, all rejected by my rubric: three on `test-plan-covered` (a control the plan named had a stated result but no pasted output) and pkg-16 on `no-unrelated-hunks` (one line removed and re-added inside a hunk that also changed behavior). calib-01 (gold accept) was also rejected, on `repo-checks-run`, because a config-only diff had no suite line.
2. Partial run after four rewordings (`test-plan-covered`, `no-unrelated-hunks`, `repo-checks-run`, `diff-within-plan`), `--only pkg-05,pkg-13,pkg-16,pkg-19,calib-01,calib-04,pkg-07,pkg-14,pkg-20,pkg-18,pkg-03`: `agreement: 8/9 scored items`. pkg-13, pkg-16, pkg-19 and calib-01 flipped to `accept`; pkg-05 stayed `reject`, still on `test-plan-covered`. The other six were canaries (not-tested, standards-wall, unreviewable and silent-drift, since every change loosened a required check) and all stayed `reject`.
3. Partial run after a second rewording of `test-plan-covered`, `--only pkg-05,pkg-07,pkg-14,pkg-10,calib-04`: `agreement: 4/4 scored items`. pkg-05 flipped to `accept`; the three not-tested canaries and calib-04 stayed `reject`.
4. Full confirming run, same files as run 3, saved with `--save-run`: `agreement: 19/20 scored items  (bar: 18/20: PASS)`, with `categories: clear-accept 6/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`. The one miss is pkg-11, which run 1 had graded `accept`. This is the committed `eval-run.txt`.

**Package analysis**

`pkg-11` (BurntSushi/ripgrep#3477, the gitignore parser trimming a backslash-escaped trailing space). My rubric: `accept` in run 1, `reject` in run 4 (it was not in the partial runs). Gold label: `accept`.

The run-4 reject came from one required check, `description-matches-diff`. The grader's evidence line: "Description claims 'tests added for the escaped, unescaped, and double-backslash cases', but the one added test covers only the escaped and double-backslash cases; no plain unescaped trailing-space case is asserted. 'One function touched' also understates the diff (edited parse_gitignore_line plus a new helper)." In run 1 the same files gave the same check a pass with the evidence "'One function touched' is loose, since a helper is also added, but it is not a contradiction. The claimed test cases (escaped, unescaped trailing, double-backslash) are exercised in the added test."

Reading the package myself: the added test `escaped_trailing_space_is_kept` feeds `"foo\\   \n"`, which carries one escaped space followed by two unescaped trailing spaces, so the unescaped case is exercised by that input even though no separate assertion names it; and "one function touched" is loose wording about a diff that edits `parse_gitignore_line` and adds a helper it calls. Neither sentence claims a change the diff lacks or denies one it contains, which is what my pass condition fails on, so I agree with the gold label and with the run-1 reading. The run-4 grader read "tests added for ... cases" as a claim of three separate test cases. I did not reword the check for it: the condition already says "claims a change the diff lacks", the package sits on the line between loose wording and a false claim, and the same check is what catches pkg-03, pkg-06, pkg-09, pkg-17 and calib-03, where the description says "no changes beyond", "exactly as planned" or "the docs now document" over a diff that shows otherwise. I kept the miss and recorded it here.

**Check rationale**

The `test-plan-covered` row, quoted from `tools/pr-precheck/rubric.md`:

> | test-plan-covered | Each failure mode the plan's Test plan names as a repro (by number, by input, or by command) that the plan-context repro evidence also shows failing before the fix, read against the test-evidence section; the plan's other test-plan items read against the evidence's stated results | Every repro the test plan names that the repro evidence showed failing before has a run in the evidence with its output pasted, and that output is the expected result the plan states for it (the value, exit code, message, count, or absence). Fails on the first such repro with no pasted run, or whose run shows a result other than the plan's expected result. Two other kinds of test-plan item are covered by a stated result in the evidence and need no pasted output: a control (the unchanged path: the dashed form, the no-ampersand input, the valid tarball, the double-quoted twin), and a check of behavior the plan adds that the repro evidence never showed failing (a new warning, a new error message), because the repro runs are what show the bug fixed and the suite line covers the added behavior. | required |

Why it reads this way. The run-1 version said "Every named item has a run in the evidence whose stated result is the expected result the plan states for it", and the grader applied it literally: pkg-13's plan named a no-ampersand control and the evidence said only "No-ampersand control unchanged", pkg-19's plan named two controls and the evidence said "Controls ... unchanged", so both failed, and so did pkg-05. The gold labels treat a control as covered by its stated result, and I agree: a control is the path the fix must not change, so a sentence saying it did not change is the whole claim, and the repro runs are what have to show output. That became the second sentence of the condition. Run 2 still failed pkg-05, because its test plan also names "re-run with two same-name same-key bindings, expect one row plus a warning" and the evidence states "Same-key redefine prints the one-time warning" with no output. That item is not a control and not a failure from the issue; it checks a behavior the plan adds. calib-04 is the package that shaped the line I drew: its plan names two failure modes that the reproduction showed failing (`-j 9223372036854775807` and `-j 200000 -x echo`), the evidence re-runs only the first, and the gold label is `reject`. So the repros that must have pasted output are the ones "that the plan-context repro evidence also shows failing before the fix", and an added-behavior check joins the controls as a stated-result item. The first version was the one I rejected: it treated every test-plan sentence the same, which made three of the seven clear-accept packages fail on the plan's own thoroughness.

**Trade-offs**

The two rewordings of `test-plan-covered` changed four results: pkg-13 and pkg-19 went from `reject` in run 1 to `accept` in runs 2 and 4, and pkg-05 went to `accept` in runs 3 and 4 (pkg-16's flip came from `no-unrelated-hunks`, where "removes and re-adds lines with identical content" became "a hunk in which every `-` line reappears unchanged as a `+` line", so one re-added line inside a behavior-changing hunk no longer counts as churn). Both rewordings loosened a required check in the not-tested family, so I re-ran canaries from that category each time: pkg-07, pkg-14 and calib-04 in run 2, then pkg-07, pkg-14, pkg-10 and calib-04 in run 3. All stayed `reject`, and in run 4 all four not-tested packages still fail: pkg-07 and pkg-14 on `repro-rerun-shown` (the evidence runs the unchanged path), pkg-10 on `repro-rerun-shown` and `repo-checks-run`, pkg-04 on `repro-rerun-shown`, and calib-04 on `test-plan-covered` for its missing second repro.

What the check now gives up: a plan that adds a new behavior (a warning, an error message) and an evidence section that only asserts the new behavior works will pass this check, as long as the issue's own repro is re-run with output and the suite line is present. A control that actually broke, described as "unchanged", will also pass here. I accept both, because the alternative rejected three good PRs for not pasting runs of paths the fix was never meant to change, and the unit suite that `repo-checks-run` requires is where an added behavior or a broken control is supposed to show up. The other loosening, `repo-checks-run` passing a config-only diff whose plan names no suite, changed only calib-01 (never scored); no scored package has a config-only diff, which is how I know nothing else moved.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
