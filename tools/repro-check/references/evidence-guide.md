# Evidence guide: where proof lives in a reproduction package

This file is the map for the checks in `rubric.md`. For each proof family it says where to look, in an eval bundle and in live mode, and what a passing example looks like in plain, checkable terms.

## Environment

Where it lives. In an eval bundle: the first lines of the "Candidate repro report" section, usually a line starting "Environment:" or a small table with the same content, and the issue's own version and OS lines under "## Issue" (often "Version:", "Operating system:", "Environment:", or a `conda info` or `pd.show_versions()` block). In live mode: the draft report's environment line, and the issue body on GitHub, including any template fields the reporter filled in.

What good looks like. The report names the version of the software under test (a release number or a commit), the operating system or platform, and how the software was installed. Then compare against the issue: if the issue was filed on 4.53.2 and the report ran 4.53.3, the report should say so ("the issue was filed against 4.53.2; I tested the current release"). If the issue is about Windows and the report ran on Linux, that difference must be named, and it should be part of any cannot-reproduce explanation. A report that gives no version or no OS fails the `env-recorded` check even if everything else is perfect, because nobody can tell whether the run tested the same thing.

## Steps

Where it lives. In an eval bundle: the middle of the "Candidate repro report", the part between the environment line and the expected/actual lines, usually a fenced block of shell commands or a numbered list, plus any input file or snippet shown. Compare against the issue's own "Steps to reproduce" or minimal example under "## Issue". In live mode: the draft report's steps block, and the issue's steps on GitHub.

What good looks like. Start from an empty directory or a fresh install and ask: could I type exactly this and get to the trigger? Every input the run needs is either shown in full or copied exactly from the issue, with the report saying so ("created test.txt with the exact 12 lines from the issue"). Every command is written out with its flags. UI steps name the key pressed or the button clicked. Watch for hidden dependencies: a private company repo, an internal config file, "our pre-commit hook", a screenshot that is described but not attached. Those make the steps unrepeatable for a stranger and fail `steps-rerunnable`. Also watch for steps that quietly differ from the issue's trigger (a different flag syntax, a different separator character): that is usually the cause of a wrong-target artifact.

## Behavior shown

Where it lives. In an eval bundle: the fenced output blocks, log excerpts, error messages, and any "Observed" or "Actual" lines in the "Candidate repro report", read side by side with the failure named in the issue's title and body (the error text, the panic message, the wrong output, the missing prompt). In live mode: the same blocks in the draft report, against the issue body on GitHub.

What good looks like. Put the issue's failure and the report's artifact next to each other and check they are the same thing. Same error text (or the same class of error with the same origin), same wrong output, same missing element. Examples of an artifact that does not match: the issue describes a panic and the artifact shows an argument-validation error; the issue describes a wrong result and the artifact shows a compile error caused by a typo in the command; the issue describes a crash and the artifact shows garbled text with the program still running; the artifact only shows the program starting up or the version being printed. Those all fail `behavior-shown` no matter how confident the surrounding words are. For a cannot-reproduce, the artifact should still show the trigger being run and the clean result, so a reader can see what was tried.

## Honesty

Where it lives. In an eval bundle: the sentences in the "Candidate repro report" that draw a conclusion ("confirmed", "reproduced", "this proves", "root cause", "cannot reproduce", "what differed"), and the "Candidate claim comment" where it repeats those claims. Read them against the artifacts in the same report. In live mode: the same sentences in the drafts.

What good looks like. The words claim exactly what the artifacts show and nothing more. A good reproduction says "matches the report" only when the artifact does match. A good cannot-reproduce says plainly that it could not reproduce, shows what was run, and lists what may have differed (OS, shell, version, input distribution); that is a pass, not a failure. Red flags that fail `honest-outcome`: "confirmed" written over a mismatched artifact; a root cause stated as fact with no code or trace shown; "100% reproducible", "ran it ten times", "guaranteed" used in place of an artifact; a report that describes only environment setup and then declares the bug confirmed. Confidence words are not evidence.

## Comms

Where it lives. In an eval bundle: the "Candidate claim comment" section, read against the issue title, body and thread highlights, and the repo-facts block lines "bug reports:" (the template asks) and "contribution policy:" (including any AI-use rule). In live mode: the draft claim comment, the issue thread on GitHub, the repo's issue template under `.github/`, `CONTRIBUTING.md`, and any `AI_POLICY.md` or similar file.

What good looks like. A claim comment that a maintainer can act on: it names the issue's specific symptom or the version tested, says what the author will do next (investigate, report, propose an approach), and promises nothing it cannot keep. Lines like "+1", "same here", "any updates?", "kindly assign me", "I will fix it in 2 days" are boilerplate or over-promises and fail `claim-specific`. For policy: read the contribution policy line first. If it says AI use must be disclosed on issues or comments (Ghostty-style "all AI usage in any form must be disclosed"), the package must contain a sentence saying whether AI was used and how; a package with no such sentence fails `policy-respected` on that repo. If the policy only sets conditions on pull requests (state the tool in the PR, understand and test the code) or says nothing, the check passes. If it asks that comments be in the contributor's own words, a boilerplate comment fails. For the template line, check each item the template asks for against the report; missing items count against `template-asks-met` (preferred) only.
