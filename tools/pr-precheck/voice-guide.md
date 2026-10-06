# Voice guide: how I talk upstream

## Who I am in threads

I am a first-time open source contributor with about six years of Python data and ML work behind me. In this repo I am a student in a course, working on one issue at a time, and I have not read most of the codebase yet. Readers can expect plain sentences, exact commands and output, and a clear line between what I ran and what I think.

## Rules I write by

### Rule: promise the next step, not the result

I say what I will do next (read a file, run a test, post a report). I do not promise a fix, a pull request, or a date, because I do not yet know how big the work is.

- Wrong: "I will have a PR up for this by Friday."
- Right: "Next I will read the phone regex in `safety/pii_scrubber.py` and post what I find here."

### Rule: show the output, do not describe it

When I say something happened, the exact command and its output are in the same comment. Words like "confirmed" or "reproducible" only appear next to an artifact.

- Wrong: "I ran the tests and they fail exactly as the issue says."
- Right: "`pytest tests/unit/test_pii_scrubber.py -q` gives `4 failed, 15 passed`; the four failures are pasted below."

### Rule: name what is specific to this issue

Every comment names something only this issue has: the symptom, the input, the file, the test name. If the comment would fit on any other issue, it is not ready.

- Wrong: "Hi, I would like to work on this issue. Please assign it to me."
- Right: "I want to take the `(555) 123-4567` case in `PIIScrubber.scrub()`. I will reproduce it with the four tests named in the issue first."

### Rule: say what I do not know

Where I have not checked something, I say so, in the same sentence, instead of leaving it out.

- Wrong: "The regex just needs an optional parenthesis group and it will work."
- Right: "I have not looked at the regex yet. My guess is the parenthesized form is missing from the pattern, but I will check before saying more."

### Rule: keep the tone plain and even

No excitement words, no praise for the project, no apologies for being new, no emoji. One short greeting at most.

- Wrong: "Hello sir! Amazing project, I love it, thank you so much for the chance!"
- Right: "Hi, first contribution here."

### Rule: say which part of the plan is a guess (added for the plan comment, Unit 3)

When I commit to an approach in front of maintainers, I mark what I have verified and what I have not, in the same sentence. A guess stated as a fact costs trust when the build shows otherwise.

- Wrong: "The separator class is the cause and widening it will fix all formats."
- Right: "The repro points at the separator class in the `phone_us` pattern; I have not yet run the change against the full test file, so the exact edit may move."

### Rule: name the maintainer's direction before my own (added for the plan comment, Unit 3)

If a maintainer or an earlier commenter already pointed at a cause or a fix, my plan names it first and says whether I follow it or why I differ. I do not repeat the diagnosis as if it were mine.

- Wrong: "I traced this to the separator handling."
- Right: "sehr-abrar's read on this thread is that the separator class has no option for a space; my reproduction agrees, and my plan builds on that."

### Rule: the title says what changed and where (added for the PR, Unit 4)

A maintainer decides from the title alone whether to open the PR now or later. My title names the component and the behavior that changed, in the repo's commit style, and nothing else. No "fix bug", no issue number as the only content, no "please review".

- Wrong: "Fix for issue #53"
- Right: "fix(safety): redact parenthesized and space-separated US phone numbers"

### Rule: the description promises exactly what the diff contains (added for the PR, Unit 4)

Every change the description names is a hunk in the diff, and every hunk in the diff is named in the description. If I removed four xfail markers and left a fifth, the description says four and names the fifth. "No other changes" appears only when `git diff main...HEAD` shows no other changes.

- Wrong: "Implements the plan in full, plus some small cleanups."
- Right: "One pattern changed in `safety/pii_scrubber.py` and four `xfail` markers removed in `tests/unit/test_pii_scrubber.py`; `test_mixed_pii_and_text` keeps its marker because it still fails for a different reason."

### Rule: a shortfall is stated as a fact with its reason, not as an apology (added for the PR, Unit 4)

When the PR delivers less than the plan, the description names what is missing, why, and where the decision was recorded, in one plain sentence. No "sorry", no "unfortunately", no promise to do it later.

- Wrong: "Sorry, I didn't get to the mixed-text case, will try to add it soon!"
- Right: "Not covered here: `test_mixed_pii_and_text`, which fails on a second defect outside the phone pattern; the plan's Deviations note records this and it belongs to its own issue."

### Rule: evidence in the PR is pasted, with the command that made it (added for the PR, Unit 4)

The Testing section carries the commands I ran and their output: the repro snippet before and after, the unit suite count, lint, format and type check results. A ticked checkbox with no output under it is a claim, not evidence.

- Wrong: "- [x] Unit tests pass"
- Right: "- [x] Unit tests pass: `make test-unit` on the branch gives `379 passed, 49 xfailed`; `pytest tests/unit/test_pii_scrubber.py -q` gives `24 passed, 1 xfailed` (was `20 passed, 5 xfailed`)."

## Things I never post

- "+1", "same here", "any updates?", or "bump".
- "Please assign this to me" without saying what I will do.
- A date, a deadline, or "guaranteed".
- "Confirmed" or "reproduced" without the command and output in the same comment.
- A root cause stated as fact when I have only a guess.
- "Same as above, can confirm" on a classmate's reproduction. My proof is my own run, in my own words.
- Anything I could not explain if a maintainer asked "how do you know?"
- A rewrite, refactor, upgrade or new option that the issue does not need, even if "while I am in there" is tempting.
- "Same approach as above" on a classmate's plan. My plan names my own files, my own steps and my own test.
- A ticked checklist item in a PR template that I did not actually run, or that I ran and cannot paste the output for.
- "Tests pass" with no command and no count beside it.
- "No other changes" or "exactly as planned" before I have read `git diff main...HEAD` top to bottom.
- A PR title that is only an issue number or only the word "fix".
- A description that leaves out AI assistance where the repo or the course asks me to say how I used it.
