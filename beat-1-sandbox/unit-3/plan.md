# Plan for issue #53: PII scrubber fails to redact parenthesized US phone numbers

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53
My repro comment: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53#issuecomment-5769508403
Branch: `fix/53-parenthesized-phone` on my fork `bhargavchintam/pathreview-ai301-fa26-s3`
Written 2026-09-26, before any code change.

## Diagnosis

The failure is in the `phone_us` pattern on line 16 of `safety/pii_scrubber.py`:

```
\b(?:\+?1[-.]?)?\(?([0-9]{3})\)?[-.]?([0-9]{3})[-.]?([0-9]{4})\b
```

My repro showed `scrub('Call me at (555) 123-4567 or 555-123-4567')` returning `'Call me at (555) 123-4567 or [REDACTED]'` and `detect()` returning `[]` for the parenthesized form, while the dashed form is found as `phone_us`. Two things in the pattern explain that, and sehr-abrar pointed at the first one on the thread:

1. The separator class between the digit groups is `[-.]?`. It accepts a dash or a dot, and nothing else. In `(555) 123-4567` the character after `)` is a space, so the match dies at that point. The same class sits after the optional `+1`, which is why `+1 555 123 4567` (one of the four strings in `test_us_phone_formats`) also fails today.
2. The leading `\b` sits before `\(?`. A word boundary needs a word character on one side, and `(` is not one, so when a space or the start of the text comes before `(`, the boundary cannot match there. The regex can only start on the `5`, which means the `(` is never part of the match. This is not what stops the match today (point 1 does that first), but once the separator is widened it decides whether the redaction reads `[REDACTED]` or `([REDACTED]`. I have not yet run a changed pattern, so the exact shape of this second edit may change during the build.

What the evidence rules out: the problem is not in `scrub()` or `detect()` themselves, because both handle the dashed form through the same loop, and it is not a missing pattern, because `phone_us` already has optional parentheses.

## Scope

In scope: the `phone_us` value in `PII_PATTERNS` in `safety/pii_scrubber.py`, and removing the `@pytest.mark.xfail(strict=True, reason="issue #53: ...")` marker from the four tests in `tests/unit/test_pii_scrubber.py` that fail only because of the phone pattern: `test_us_phone_number_redaction`, `test_us_phone_formats`, `test_detect_phone_pii`, `test_phone_at_start_of_text`.

Not in scope: the fifth test that carries the #53 marker, `test_mixed_pii_and_text`. It fails because the `street_address` pattern matches "5 years developing Python appl" and redacts the word "Python". That is a different pattern and a different bug. Its marker stays, and I will note it in the PR so it can be filed on its own. Also not in scope: `phone_intl`, the other PII patterns, the return shape of `detect()`, and any change to `scrub()`.

## Files

- `safety/pii_scrubber.py`, line 16, the `phone_us` string.
- `tests/unit/test_pii_scrubber.py`, the four `xfail` decorators named above (lines 34 to 37, 46 to 49, 130 to 133, 191 to 194 on `main` at `2f4e82f`).

## Approach

1. On the branch, change the two separator classes in `phone_us` from `[-.]?` to `[-. ]?` so a space is accepted between the digit groups and after `+1`.
2. Make the opening parenthesis reachable: replace the leading `\b\(?` with a form that matches either `\(` or a boundary before the first digit, for example `(?:\(|\b)`. I will confirm this against the test strings and against `detect()` values before committing; if the simpler separator change alone makes all four tests pass and the redaction reads cleanly, I will stop at step 1 and record that here.
3. Remove the four `xfail` decorators listed under Scope. Leave the fifth.
4. Run the test file, then `make lint` and `make typecheck`, since CONTRIBUTING asks for both before a PR.

## Test plan

Before and after the change, from the repo root with the same `.venv`:

1. Re-run my repro snippet, `repro_53.py` (the issue's snippet plus the dashed control line).
   Before: `'Call me at (555) 123-4567 or [REDACTED]'`, then `[]` for the parenthesized detect.
   Expected after: `'Call me at [REDACTED] or [REDACTED]'`, and `detect('Call me at (555) 123-4567')` returns one entry with `type` `phone_us` whose `value` contains `555`, `123` and `4567`.
2. `.venv/bin/pytest tests/unit/test_pii_scrubber.py -q -rxX -p no:cacheprovider`.
   Before: `20 passed, 5 xfailed`.
   Expected after: `24 passed, 1 xfailed`, the one xfail being `test_mixed_pii_and_text`.
3. The four control cases keep passing: `test_phone_at_end_of_text` (dashed form), `test_international_phone_redaction` (`+44 20 7946 0958`), and the dashed and dotted strings inside `test_us_phone_formats`.
4. `make lint` and `make typecheck` report no new findings.

## Risks and unknowns

- Allowing a space as a separator makes the pattern match more digit runs, for example `555 123 4567` with no punctuation. That is wanted for `+1 555 123 4567`, but it could also redact things that are not phone numbers, such as three space-separated number groups in a table. I accept that for this issue and will say so in the PR.
- The `\b` edit in step 2 is the part I have not verified. If it changes what `detect()` reports for the dashed form, I will drop it and keep only step 1.
- I have not run `make lint` on this repo yet, so I do not know whether the line length of the new pattern trips ruff. If it does, I will wrap the string rather than disable a rule.

## Deviations

2026-09-26, during the build. One deviation from step 2 of the approach, and step 1 went as planned.

Step 2 said the start of the pattern would accept either `(` or a word boundary, for example `(?:\(|\b)`. Before editing the file I ran both candidate patterns against the seven test strings. `(?:\(|\b)` redacted `(555) 123-4567` cleanly but left the plus sign behind in `+1 555 123 4567` (result `+[REDACTED]`), because a word boundary cannot match between a space and `+` either. A negative lookbehind, `(?<!\w)`, matched both the `(` and the `+` cases and changed nothing for the dashed, dotted and `+44` controls, so I used that instead. The change is still one edit at the start of the same pattern; the mechanism is the same, only the form differs from the example in step 2.

Nothing else changed. The four markers came off, the fifth stayed, and the diff is one line in `safety/pii_scrubber.py` plus the four removed decorators. The posted plan comment described step 2 as "the start of the pattern accepts either `(` or a word boundary", which still describes what the lookbehind does, so I did not post a correction to the thread.

Test plan result: `repro_53.py` printed `'Call me at [REDACTED] or [REDACTED]'` and `detect()` returned `[{'type': 'phone_us', 'value': '(555) 123-4567', 'start': 11, 'end': 25}]`. `pytest tests/unit/test_pii_scrubber.py` went from `20 passed, 5 xfailed` to `24 passed, 1 xfailed` (the one being `test_mixed_pii_and_text`). The full unit suite: `379 passed, 49 xfailed`. `ruff check`, `black --check` and `mypy safety/` all clean. Commit `afd68d1` on `fix/53-parenthesized-phone`.
