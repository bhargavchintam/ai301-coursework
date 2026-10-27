# Contribution: Tighten up `archive_file`

**Contribution Number:** 2
**Student:** Bhargav Chintam
**Issue:** https://github.com/emfcamp/Website/issues/1779
**Pull Request:** https://github.com/emfcamp/Website/pull/2391

---

## Toolkit

This is the second, ungraded contribution; the toolkit's timeline for units 5 to 8 lives in `contribution-story.md`. The entries below only record what each tool did on this issue.

### Unit 5: the scout graded #1779 in direct mode on 2026-10-13 and ranked it first on fit

Direct grading of my three Issue Selection site picks accepted emfcamp/Website #1779 as the best fit for my profile ("a bounded input-validation fix: sanitise `year` so it can't contain slashes", labels Small and good first issue, a MEMBER opener, no assignee, no comments, no linked PR). I held it on the site from 2026-10-13 and started it on 2026-10-27, after the first contribution's PR was open and the Unit 8 monitor was installed. Nothing in the scout changed for this issue.

### Unit 6: the guard read the claim and repro-check graded it before it went up

The claim draft passed the guard on the first run, and repro-check (claim-only mode) accepted it with three voice notes I took: "following Mark's direction" overstated a one-line issue, the pasted command named a script a reader could not run, and the routes claim did not say where I had checked it. The posted claim pastes the throwaway script and its output. `CLAUDE.local.md` for this clone is 34 lines; the repo ships no CLAUDE.md or AGENTS.md and its CONTRIBUTING.md is six lines pointing at IRC, so the conventions came from `.github/workflows/test.yml`, the Makefile and the commit log.

### Unit 7: the gate blocked once on my own manifest syntax, then passed twice

`install.py --repo` placed the manifest template for this clone. Gate run 1 stopped on my fourth manifest row: the command used shell pipes, and a markdown table splits on `|`, so the gate ran half a command. Run 2 passed on the manifest and my rubric, with two voice notes (the Before and After blocks lacked their command line; the lint result was paraphrased) and one evidence gap (the docker suite was still a placeholder). Run 3 passed with one borderline note on stating the maintainer's reasoning as fact, which I reworded. The PR opened through the guard after the third pass.

### Unit 8: the monitor read the closed PR and drafted the close-out, which I revised and posted

The maintainer closed the issue and the PR an hour after the claim (already fixed in March, commit bb70629; typing keeps `year` an `int` and pathlib does the join). The monitor, run from the clone on the branch, read the state as CLOSED, classified it as my walk-away case "the issue gets fixed another way", and drafted a two-sentence close-out via the review-response form with notes to name the commit, keep it short and not compare fixes. I rewrote it in my own words (one sentence), the guard passed it, and it went up on the PR with Bhargav's yes. Then I released the Issue Selection site hold. What the monitor got right: no nudge, no argument, nothing beyond recording the end. What it could not see: the linked issue comment with the commit, which it asked me to fill in by hand.

---

## Why I Chose This Issue

My scout's direct grading on 2026-10-13 ranked this issue first on fit of my three Issue Selection picks: a bounded input-validation fix in a Flask application, Python, labels Small and good first issue, opened by a MEMBER (marksteward), with no comments, no assignee and no linked PR. The read-out's one unverified line was "I did not open the source to confirm `archive_file` is Python"; it is. I chose to do spack-packages #5400 first because its fix was stated by the upstream maintainer and reaches every Spack site, and this one second, after the first PR was open, because a held site claim should be worked or released and I had the time. What I hoped to learn was how a volunteer-run event site handles a defence-in-depth change, and how to write a unit test that proves a gap without a database.

The three lenses: fit, yes, this is exactly the input-validation work my profile names. Energy, yes, it is small enough to finish in a day and the test is the interesting part. Thread: open since 2024, zero comments, no assignee, no PR; the door was open.

Claim comment, posted 2026-10-27: https://github.com/emfcamp/Website/issues/1779#issuecomment-6436729886

The scout held up here. Its one shortfall was the same as on #5400: `responds-fast` was not sampled, and this repo's rhythm (last push 2026-10-10, a volunteer team, no stated review wait) is exactly the kind of thing a first-reply sample would have told me before I claimed.

**Toolkit**
- Tool: issue-scout in direct mode on 2026-10-13 (`claude "issue-scout: grade <three URLs>"`), rubric before the Unit 6 scope patch.
- What it produced: accept, ranked first on fit; checks maintainer-alive (Kunsi committed 2026-10-10), spec-present (MEMBER opener), unclaimed (no assignee, comments or PRs), ai-policy-allows (no AI statement) all passing; ships-releases failing (no releases), responds-fast not sampled.
- Difficulties: responds-fast not sampled; no difficulty note existed yet (the patch came later; it would read "straightforward": the issue names the function and the change is bounded).
- What changed: nothing for this issue; the same gh recipe for the first-reply sample is the patch I owe the scout.

---

## Understanding the Issue

### Problem Description

`archive_file()` in `apps/common/__init__.py` returns the path to a file under `exports/<year>/...`, the static archive of past events. It joins `year` into the path as given and only checks afterwards that the resolved path is still under `exports/` and exists. The issue (one line from the maintainer) says this is not currently exploitable but `year` should be sanitised so it cannot contain slashes.

### Expected Behavior

A `year` that is not a plain run of digits should never be joined into the path; it should be treated like a missing archive file (404, or `None` when `raise_404=False`), so no caller can select another year's directory with a crafted string, and the existing escape protection stays as it is.

### Current Behavior

On `main` at 8651f9e3, `archive_file("2022/../2024", "public", "schedule.json")` and `archive_file("../exports/2024", "public", "schedule.json")` both return `exports/2024/public/schedule.json`. An escape above `exports/` (`"2024/../../README.md"`) returns `None`, so the issue's "not currently exploitable" holds. Today's only caller, `apps/schedule/historic.py`, receives `year` from `<int:year>` routes.

### Affected Components

`apps/common/__init__.py`: `archive_file(year, *path, raise_404=True)` (line 384) and its wrapper `load_archive_file` (line 403). Caller: `apps/schedule/historic.py` (`historic_talk_data`, `item_historic`, `feed_historic`). Routes in `apps/schedule/__init__.py` use `<int:year>`. Tests: `tests/`, pytest with `SETTINGS_FILE=config/test.cfg`; the full suite runs in docker compose with Postgres, but this function needs no database.

**Toolkit**
- Tool: the repo memory (`CLAUDE.local.md`) as the distilled orientation, written from the workflow file, the Makefile, `pyproject.toml` and the one caller, plus `gh api` to read the files before cloning.
- What it produced: 34 lines naming the function, the caller, the routes, the exact CI commands (`uv run --group dev make check-syntax check-mypy`; pytest in docker compose) and the pins (Python 3.14, ruff 0.15.12).
- Difficulties: CONTRIBUTING.md states no process at all, so there was nothing to mine for PR conventions beyond the commit log's "area: what changed" style; and my GitHub account already had a repo named Website, so the fork became `Website-1` and my first clone pulled the wrong repository.
- What changed: the clone directory is named `emfcamp-Website` to avoid the same slip again; nothing in the template.

---

## Reproduction Process

### Environment Setup

macOS on Apple Silicon with uv 0.12.19, Homebrew Python 3.14.7 (the repo pins >=3.14,<3.15) and Docker 29.8.2. Fork `bhargavchintam/Website-1`, clone at `Downloads/AI301/beat-2/emfcamp-Website` with `upstream` pointing at emfcamp/Website. `uv sync --locked --all-extras --dev` installed 207 packages. Calling `archive_file()` needs `SETTINGS_FILE` set (`config/test.cfg`) and `PYTHONPATH=.`; the first run failed with `ModuleNotFoundError: No module named 'apps'` until I added the latter. No database is needed for this function; the full suite needs Postgres, which docker compose provides.

### Steps to Reproduce

1. On `main` at 8651f9e3, from the clone root, run the throwaway script `repro_1779.py` (pasted in the claim comment) with `PYTHONPATH=. uv run python repro_1779.py`; it calls `archive_file()` six times with `raise_404=False`.
2. Compare the two slash cases against the int and string controls and the escape case.
3. Observed result: both slash cases return `exports/2024/public/schedule.json`; the escape case and the no-archive year return `None`; exit 0.

### Reproduction Evidence

- **Commit showing reproduction:** no commit, since the reproduction is a script against the unmodified tree (`main` at https://github.com/emfcamp/Website/commit/8651f9e3); the fix commit on my fork is https://github.com/bhargavchintam/Website-1/commit/607376d2.
- **Screenshots/logs:**

```
$ PYTHONPATH=. uv run python ../../unit-8-work/emfcamp/repro_1779.py   # main at 8651f9e3, before any fix
archive_file(2024, ('public', 'schedule.json')) -> exports/2024/public/schedule.json
archive_file('2024', ('public', 'schedule.json')) -> exports/2024/public/schedule.json
archive_file('2022/../2024', ('public', 'schedule.json')) -> exports/2024/public/schedule.json
archive_file('../exports/2024', ('public', 'schedule.json')) -> exports/2024/public/schedule.json
archive_file('2024/../../README.md', ()) -> None
archive_file('1999', ('public', 'schedule.json')) -> None
exit=0
```

- **My findings:** the issue reproduces exactly as written, and the controls bound the fix: int and digit-string years must keep working, the `parents` check already blocks escapes and must stay, and the no-archive year already behaves as "missing", which is the behaviour a bad `year` should get too.

**Toolkit**
- Tool: repro-check in live mode on the claim draft (claim-only package), 2026-10-27, and the voice-guard on the same draft.
- What it produced: verdict accept; `claim-specific` and `policy-respected` passing, the repro checks marked not yet applicable; three voice notes (maintainer's direction overstated, a command a reader could not run, an unchecked routes claim).
- Difficulties: none in the tool; the notes were all right.
- What changed: the claim now pastes the script itself, says where the routes were checked, and calls the design my own.

---

## Solution Approach

### Analysis

The root cause is that `archive_file()` trusts `year` as a path component. `EXPORT_ROOT / str(year) / Path(*path)` is resolved and then checked with `EXPORT_ROOT in file_path.parents`, which only rejects paths that leave `exports/`. A string with slashes that resolves back under `exports/` passes, so `"2022/../2024"` reads 2024's files. The type hint says `int`, and the routes supply an `int`, but nothing in the function enforces it.

### Proposed Solution

Validate `year` before the join: accept only a plain run of digits (`re.fullmatch(r"[0-9]+", str(year))`, with `bool` excluded because `True` is an `int`), and treat anything else exactly like a missing file (`abort(404)`, or `None` under `raise_404=False`). Add `tests/test_archive_file.py`. Deliberately not changed: the `parents` check, the `*path` parts (the issue names `year`; offered to the maintainer), the `int` type hint, `load_archive_file`, `historic.py`, and the 404-or-None contract.

### Implementation Plan

1. Modify `apps/common/__init__.py`, `archive_file()`: add the digit check with a three-line comment naming #1779, before `EXPORT_ROOT` is computed.
2. Add `tests/test_archive_file.py`: int and string controls, a parametrised set of slash and non-digit years (`"2022/../2024"`, `"../exports/2024"`, `"/2024"`, `"2024/"`, `"2024/public"`, `"20 24"`, `"2024.0"`, `""`, `True`) that must be missing both ways, the no-archive year, and the `..` escape.
3. Run the new file against `main` (expect the slash cases to fail) and against the branch, then `uv run --group dev make check-syntax check-mypy`, then the docker suite once.

Deviations: none; the branch is what the claim described.

**Toolkit**
- Tool: the repo memory's Build and test section and the ship-gate's manifest template, which together fixed the check commands before any code was written; plan-check was not run separately on this plan, since the claim comment doubled as the plan and repro-check had graded it (the first contribution's lesson about running plan-check before building applies, and here the plan was built the same hour).
- What it produced: a two-file scope with the not-in-scope list and a four-step test plan with expected results on both sides.
- Difficulties: none met.
- What changed: nothing.

---

## Testing Strategy

The reproduction script re-run on the branch (commit 607376d2) flips the two slash cases and leaves the four controls unchanged:

```
$ PYTHONPATH=. uv run python ../../unit-8-work/emfcamp/repro_1779.py   # branch fix/1779-archive-file-year, after the fix
archive_file(2024, ('public', 'schedule.json')) -> exports/2024/public/schedule.json
archive_file('2024', ('public', 'schedule.json')) -> exports/2024/public/schedule.json
archive_file('2022/../2024', ('public', 'schedule.json')) -> None
archive_file('../exports/2024', ('public', 'schedule.json')) -> None
archive_file('2024/../../README.md', ()) -> None
archive_file('1999', ('public', 'schedule.json')) -> None
exit=0
```

The new test file proves the gap on both sides. Against `main` with the fix stashed:

```
FAILED tests/test_archive_file.py::test_year_that_is_not_digits_is_treated_as_missing[2022/../2024]
FAILED tests/test_archive_file.py::test_year_that_is_not_digits_is_treated_as_missing[../exports/2024]
FAILED tests/test_archive_file.py::test_year_that_is_not_digits_is_treated_as_missing[2024/]
3 failed, 10 passed in 0.14s
```

On the branch: `13 passed, 1 warning in 0.42s` (the warning is a pre-existing `SyntaxWarning` in `apps/volunteer/main.py`, not mine). The repo's checks the way CI runs them: `uv run --group dev make check-syntax check-mypy` gives `uv lock --check` ok, `221 files already formatted`, `All checks passed!`, `Success: no issues found in 183 source files`. The full suite in docker compose, once by hand: `162 passed, 17 skipped, 114 warnings in 29.25s`, exit 0.

**Toolkit**
- Tool: the ship-gate (manual doorway, `--base main`), running the manifest's four rows (check-syntax, check-mypy, the one test file natively, a two-file diff check) and then pr-precheck live.
- What it produced: run 1 STOP on my own manifest row (shell pipes inside a markdown table); run 2 PASS with two voice notes and one evidence gap; run 3 PASS with one borderline voice note.
- Difficulties: the manifest format itself; a command with `|` cannot live in a table row, which the template does not say.
- What changed: the diff-check row was rewritten without pipes (`[ "$(git diff --name-only ...)" = "$(printf '...')" ]`); the docker suite result and the two command lines went into the evidence and the PR text.

---

## Implementation Notes

### 2026-10-13

Direct grading of the three Issue Selection picks ranked #1779 first on fit. Claimed it on the site (a hold only). Decided to do #5400 first.

### 2026-10-27

Read the repo through `gh api` before cloning: CONTRIBUTING.md is six lines about IRC and ticket support; CI is `uv sync` plus `make check-syntax check-mypy` plus pytest in docker compose; Python pinned to 3.14; ruff 0.15.12. Forked (the fork became `Website-1` because my own account had a `Website` repo, and my first clone pulled my own repo by mistake; moved it aside and cloned the fork as `emfcamp-Website`). `uv sync` ok. Wrote `repro_1779.py`; first run failed on `PYTHONPATH`; second run reproduced the gap. Drafted the claim, guard pass, repro-check accept with three voice notes, revised, posted with Bhargav's yes (comment 6436729886). Wrote `CLAUDE.local.md` (34 lines). Branch `fix/1779-archive-file-year`: the digit check in `archive_file()` and `tests/test_archive_file.py`; repro after the fix flips the two slash cases; the test file fails 3 on main and passes 13 on the branch; check-syntax and check-mypy clean; committed as 607376d2 under my name and pushed to the fork. Ship-gate installed for this clone; manifest written; gate run 1 stopped on a table-row pipe, run 2 and run 3 passed; docker suite 162 passed, 17 skipped. Evidence and PR text corrected per the gate's notes. PR #2391 opened with Bhargav's yes through the guard and `gh pr create --body-file`.

**Toolkit**
- Tool: the voice-guard's `guard-log.md` and the ship-gate's `gate-log.md` as the timeline of record.
- What it produced: guard passes on the claim (01:01) and the PR text (02:14); gate STOP at run 1, PASS at runs 2 and 3.
- Difficulties: none.
- What changed: nothing.

---

## Pull Request

**PR Link:** https://github.com/emfcamp/Website/pull/2391

**PR Description:** Title "common: reject a non-numeric year in archive_file" (the repo's lower-case "area: what changed" style). Body: "Fixes #1779.", a Why section that states the gap and says the `parents` check is consistent with "not currently exploitable", a What changed section for the one function and the new test file with the not-changed list (`*path` offered to the maintainer), a Checks section pasting the before and after script output with its command, the test file on both sides, the lint and mypy output, and the docker suite result, and a Note disclosing in my own words that Claude was used as a coding assistant and that the change and the text are mine. The repo has no PR template and no AI policy.

**Maintainer Feedback:**

- 2026-10-27 (09:24 UTC, about an hour after the claim): marksteward on the issue: "unfortunately this was already fixed in March with bb7062943ad45d14491d52b934eab763bf46d479", with links showing the function now relies on type checking to keep `year` an integer and on pathlib for the join, "This means both your check and test are redundant, so I'll close the PR too, sorry." He closed the issue as completed and the PR with "Already fixed, see comment".
- 2026-10-27 (14:45 PT): I replied on the PR, one sentence, thanking him for the quick reply and the pointer and agreeing it stays closed (https://github.com/emfcamp/Website/pull/2391#issuecomment-6454380001). Released the site hold the same hour.

**Status:** Closed by the maintainer on 2026-10-27, not merged: the issue had been fixed in March 2026 by commit bb70629 and the issue text was stale. The claim, the reproduction and the PR all happened against the current `main`, where the string case still reproduces; the maintainers consider the type check sufficient, and that is their call.

**Toolkit**
- Tool: the ship-gate wrapping my pr-precheck and manifest, three runs through the manual doorway, then the guard on the PR body before `gh pr create`.
- What it produced: STOP (my manifest's pipe), PASS, PASS; the final report read "All 11 required checks pass, and both preferred checks pass" with one borderline voice note, which I applied.
- Difficulties: the gate reads the manifest as a markdown table, so a `|` in a command silently truncates the row; the stop's quoted output made it obvious within one run.
- What changed: the row; and the PR text pasted the commands and the lint output instead of paraphrasing them.

---

## Learnings and Reflections

### Technical Skills Gained

How `pathlib.resolve()` with a `parents` membership check bounds a path to a root but not to a subdirectory, and why validating the untrusted component before the join is the cheaper and clearer defence. Writing a pytest file that proves a gap on both sides of a fix without a database, with `werkzeug.exceptions.NotFound` for the `abort(404)` path. Running a uv-managed Python 3.14 project natively and its docker compose suite with Postgres from the same clone.

### Challenges Overcome

The real one was that the issue was stale: fixed seven months earlier by a commit that never referenced it, so the thread looked open and my reproduction on `main` still showed the string case. The maintainer's close was quick, clear and polite, and the ending cost one short reply. Two self-inflicted ones: a fork that landed under a different name because my own account already had a `Website` repo (the first clone pulled the wrong repository, caught by the commit log before any work), and a manifest row broken by shell pipes inside a markdown table (caught by the gate's first stop). Neither cost more than a few minutes because the tools quoted exactly what was wrong.

### What I'd Do Differently Next Time

Check the issue against the repo's history before claiming, not only against the code: `git log -S archive_file --oneline` or a look at the function's blame would have shown the March commit "Switch archive_file to use pathlib and make it more defensive" and told me the issue was stale before I spent the day; my scout's `no-failed-attempts` and `unclaimed` checks read the thread and the PRs, but nothing in it reads the commit log for the function the issue names, and that is a patch worth making. Check `gh repo view` on the fork name right after forking, before cloning. Write manifest commands with no `|` from the start. And run plan-check on the plan before building even when the plan and the build happen in the same hour; the first contribution taught that order and I bent it here because the change was small.

---

## Resources Used

- The issue, one line from the maintainer that defined the scope: https://github.com/emfcamp/Website/issues/1779
- `.github/workflows/test.yml` and the Makefile in emfcamp/Website, the source of every check command in the manifest
- The repo's developer documentation, for the uv and docker compose setup: https://emfcamp.github.io/Website/
- `pathlib` documentation on `Path.resolve()` and `Path.parents`, read to confirm why the existing check bounds the path to `exports/` and no further: https://docs.python.org/3/library/pathlib.html
- The Unit 8 move forms in the course starter, which pointed at `extra-round-template.md` for this file
