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

## Things I never post

- "+1", "same here", "any updates?", or "bump".
- "Please assign this to me" without saying what I will do.
- A date, a deadline, or "guaranteed".
- "Confirmed" or "reproduced" without the command and output in the same comment.
- A root cause stated as fact when I have only a guess.
- "Same as above, can confirm" on a classmate's reproduction. My proof is my own run, in my own words.
- Anything I could not explain if a maintainer asked "how do you know?"
