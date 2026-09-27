# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

bhargavchintam

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53#issuecomment-5853463361

Text as posted on 2026-09-26:

Plan for #53, built from my reproduction above.

Diagnosis: the `phone_us` pattern on line 16 of `safety/pii_scrubber.py` only allows a dash or a dot between the digit groups (`[-.]?`), so the space after `)` in `(555) 123-4567` stops the match. sehr-abrar reached the same reading on this thread, and my repro agrees. A second detail I have not yet tested: the leading `\b` sits before `\(?`, and a word boundary cannot match between a space and `(`, so the opening parenthesis is never part of the match today. That decides whether the redaction reads `[REDACTED]` or `([REDACTED]`, not whether it happens.

What I will change, on branch `fix/53-parenthesized-phone` of my fork: the two separator classes in `phone_us` become `[-. ]?`, and, if it holds up against the test strings, the start of the pattern accepts either `(` or a word boundary. Then I remove the `xfail` marker from the four phone tests the issue names: `test_us_phone_number_redaction`, `test_us_phone_formats`, `test_detect_phone_pii`, `test_phone_at_start_of_text`.

Not changing: the fifth test carrying the #53 marker, `test_mixed_pii_and_text`. It fails because the `street_address` pattern matches "5 years developing Python appl" and redacts "Python", which is a separate bug in a different pattern. Its marker stays, and I will note it in the PR. Also not touching `phone_intl`, the other patterns, or `detect()`'s return shape.

Test: re-run my repro snippet, expecting `'Call me at [REDACTED] or [REDACTED]'` and a `phone_us` entry from `detect()` for the parenthesized form; `pytest tests/unit/test_pii_scrubber.py` going from `20 passed, 5 xfailed` to `24 passed, 1 xfailed`; the dashed, dotted and `+44` cases still passing; `make lint` and `make typecheck` clean.

Known cost: accepting a space as a separator also matches plain `555 123 4567`, which is wanted for `+1 555 123 4567` but could redact other space-separated number groups. I will say so in the PR.

AI use: I am using Claude to help with the build and to check my comments. I review every change and its test output myself.

---

## Your branch

**Branch**

`fix/53-parenthesized-phone` on my fork `bhargavchintam/pathreview-ai301-fa26-s3`: https://github.com/bhargavchintam/pathreview-ai301-fa26-s3/tree/fix/53-parenthesized-phone (commit `afd68d1`)

**Evidence**

Both runs from the repo root of my fork, same `.venv` (Python 3.12.8, pytest 9.1.1), macOS 27.0. `repro_53.py` is the snippet from my Unit 2 repro comment (the issue's snippet plus one dashed control line). structlog `pii_detected` lines are trimmed from the snippet output.

Before, on `main` at `2f4e82f`, right after creating the branch and before any edit:

```
$ git rev-parse --short HEAD
2f4e82f
$ .venv/bin/python repro_53.py
'Call me at (555) 123-4567 or [REDACTED]'
[]
[{'type': 'phone_us', 'value': '555-123-4567', 'start': 11, 'end': 23}]
$ .venv/bin/pytest tests/unit/test_pii_scrubber.py -q -rxX -p no:cacheprovider
XFAIL tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_number_redaction - issue #53: PII scrubber does not redact parenthesized US phone numbers
XFAIL tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_formats - issue #53: PII scrubber does not redact parenthesized US phone numbers
XFAIL tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_phone_pii - issue #53: PII scrubber does not redact parenthesized US phone numbers
XFAIL tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_phone_at_start_of_text - issue #53: PII scrubber does not redact parenthesized US phone numbers
XFAIL tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_mixed_pii_and_text - issue #53: PII scrubber does not redact parenthesized US phone numbers
20 passed, 5 xfailed in 0.15s
```

After, on `fix/53-parenthesized-phone` with the change applied (commit `afd68d1`):

```
$ git diff --stat
 safety/pii_scrubber.py          |  2 +-
 tests/unit/test_pii_scrubber.py | 16 ----------------
 2 files changed, 1 insertion(+), 17 deletions(-)
$ .venv/bin/python repro_53.py
'Call me at [REDACTED] or [REDACTED]'
[{'type': 'phone_us', 'value': '(555) 123-4567', 'start': 11, 'end': 25}]
[{'type': 'phone_us', 'value': '555-123-4567', 'start': 11, 'end': 23}]
$ .venv/bin/pytest tests/unit/test_pii_scrubber.py -q -rxX -p no:cacheprovider
XFAIL tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_mixed_pii_and_text - issue #53: PII scrubber does not redact parenthesized US phone numbers
24 passed, 1 xfailed in 0.12s
$ .venv/bin/pytest tests/unit -q -p no:cacheprovider -m unit (full unit suite, summary line)
379 passed, 49 xfailed, 6 warnings in 8.60s
$ .venv/bin/ruff check safety/pii_scrubber.py tests/unit/test_pii_scrubber.py
All checks passed!
$ .venv/bin/black --check safety/pii_scrubber.py tests/unit/test_pii_scrubber.py
All done! ✨ 🍰 ✨
2 files would be left unchanged.
$ .venv/bin/mypy safety/
pyproject.toml: note: unused section(s): module = ['agent.tools.market_analyzer', 'api.main', 'api.routes.health', 'api.routes.profiles', 'api.routes.reviews', 'core.logging', 'core.services.profile_service', 'core.services.review_service', 'ingestion.chunking.semantic_chunker', 'ingestion.chunking.structural_chunker', 'ingestion.parsers.skill_extractor', 'ingestion.pipeline', 'rag.generator.output_parser', 'rag.generator.review_generator']
Success: no issues found in 7 source files
```

What changed: the parenthesized number is now redacted and `detect()` returns a `phone_us` entry for it with value `(555) 123-4567`; the dashed control still works; the four phone tests pass without their markers (`20 passed, 5 xfailed` became `24 passed, 1 xfailed`); the one remaining xfail is `test_mixed_pii_and_text`, which is the `street_address` bug and stays out of scope on purpose.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Four runs, all on 2026-09-26, in this order:

1. Full run, first version of the rubric, evidence guide and procedure: `agreement: 16/20 scored items  (bar: 18/20: below the bar)`, with `categories: clear-accept 3/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`. The four misses were pkg-02, pkg-08, pkg-09 and pkg-14, all gold `accept`, all rejected by my rubric.
2. Partial run after I reworded four pass conditions, `--only pkg-02,pkg-08,pkg-09,pkg-14,pkg-11,pkg-16,pkg-04,pkg-18`: `agreement: 7/8 scored items`. pkg-02, pkg-08 and pkg-09 flipped to `accept`; pkg-14 stayed `reject`. pkg-11, pkg-16, pkg-04 and pkg-18 were canaries (wrong-cause, thread-convention and unbuildable) because every change loosened a check, and all four stayed `reject`.
3. Partial run after adding a fourth way to back a cause claim, `--only pkg-14,pkg-11,pkg-16,pkg-07,pkg-01`: `agreement: 4/5 scored items`. pkg-14 stayed `reject`, now on `diagnosis-consistent` only; the four wrong-cause canaries stayed `reject`.
4. Full confirming run, same files as run 3, saved with `--save-run`: `agreement: 19/20 scored items  (bar: 18/20: PASS)`, with `categories: clear-accept 6/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`. This is the committed `eval-run.txt`.

**Package analysis**

`pkg-14` (zellij-org/zellij#5174, OSC colour sequences leaking into the terminal on reattach). My rubric: `reject`, in all four runs. Gold label: `accept`.

By the final run, one required check decided it: `diagnosis-consistent`. The grader's evidence was: "Cache control ('the next attach is clean, the one after leaks again') isn't produced by the stated cause (a cache-independent stdin-timing race at reattach); the plan's fix ('with an empty cache the color data is refetched along the fresh-attach path once') invents an unstated cache-to-path-routing mechanism". The check fails when a control run shows behaviour the stated cause cannot explain and the plan does not explain it. The plan does offer an explanation for the cache control, and I wrote the pass condition so that "a control run counts as explained when the plan itself states how the cause produces that control's result". The grader read that explanation as a new, unsupported claim rather than as an explanation, and failed the check.

I think the gold label is right about this plan: the cause is an inference tied to three controls (fresh attach clean, 0.44.1 clean, cache clear gives one clean attach), and the plan says openly that the exact functions are "to be pinned in the PR after tracing". In runs 1 and 2 my rubric also failed it on `cause-claims-backed` and `starting-point-named`, and I fixed both of those by rewording. What is left is one grader judgment about whether a one-sentence explanation of a control counts. I did not reword `diagnosis-consistent` a third time, because the wording already says what I mean, and loosening it further risks the four wrong-cause packages (pkg-01, pkg-07, pkg-11, pkg-16), all of which are caught by exactly this check. I kept the miss.

**Check rationale**

The `cause-claims-backed` row, quoted from `tools/plan-check/rubric.md`:

> | cause-claims-backed | Each statement in the plan that names a cause or a fix site, read against the repro artifacts and the thread highlights | Every cause or fix-site statement is one of: (a) shown by an artifact in the repro evidence, (b) stated in the issue body or by any commenter in the thread highlights and not contradicted by the repro evidence, (c) marked as unverified anywhere in the plan (words such as "not yet verified", "unknown", "may", "to be pinned", "I have not checked"), or (d) an inference that the plan ties to named repro steps or control runs, where no step or control run in the repro evidence contradicts it. Fails when one such statement has none of the four. | required |

Why it reads this way. The first version allowed only three backings: an artifact, a maintainer statement, or a hedge in the same sentence. Run 1 used that to reject three good plans. pkg-02's fix site came from the issue reporter (a NONE author) who named `src/printer.rs:934`; pkg-08's came from a NONE commenter who named `delpaths_sorted` with line numbers; the grader's evidence for pkg-08 was "sourced only from maximilize (NONE), not a maintainer". An issue reporter or commenter who names a line is a real source, so (b) now reads "stated in the issue body or by any commenter in the thread highlights and not contradicted by the repro evidence". The "not contradicted" part is what keeps pkg-11 and pkg-16 rejected: their causes appear nowhere in the thread and the controls contradict them. Run 2 still failed pkg-14 on this check, because its cause is an inference from controls that nobody in the thread stated. A diagnosis is always an inference, so I added (d): an inference the plan ties to named repro steps or control runs, where no step or control contradicts it. I also widened (c) from "in the same sentence" to "anywhere in the plan", because pkg-14 hedges its fix site in the files section, not in the diagnosis sentence.

This is also the check where I applied the Unit 1 and Unit 2 feedback on purpose: each backing is its own lettered clause, so the grader's evidence names which one was missing, and there is no compound AND inside the condition.

**Trade-offs**

The rewording of `cause-claims-backed` changed three results: pkg-02, pkg-08 and pkg-09 went from `reject` in run 1 to `accept` in runs 2 and 4 (pkg-09 also needed the `thread-followed` change, since the collaborator there listed three options without preferring one). Each rewording loosened a required check, so I re-ran canaries from every category the change could touch: pkg-11 and pkg-16 in run 2, then pkg-01, pkg-07, pkg-11 and pkg-16 again in run 3 after adding clause (d), plus pkg-04 (thread-convention) and pkg-18 (unbuildable) in run 2. All of them stayed `reject` in every run, and in run 4 the four wrong-cause packages still fail on `diagnosis-consistent`, so clause (d)'s "not contradicted" condition is doing the work I wanted.

What the check now gives up: a plan whose cause is an inference, tied to named controls, and wrong in a way the controls do not expose, will pass this check. A control has to contradict the cause for (d) to fail. That is the case I accept missing, because the alternative (requiring a maintainer or an artifact for every cause) rejected three of the seven clear-accept packages in run 1. The other cost is the one I did not fix: pkg-14 stays rejected on `diagnosis-consistent`, described above.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
