# Procedure: how this skill grades a plan package

## Read order

1. Read the repo-facts block first. Write down two facts: the contribution policy line (does it require AI disclosure on comments or issues: yes or no), and the bug-report template line.
2. Read the issue title and body. Write down the failure in one line: the wrong behaviour and the trigger.
3. Read the thread highlights. For each line whose author association is OWNER, MEMBER or COLLABORATOR, write down whether it names a cause, a fix site, a preferred direction, or an open PR. Ignore lines from NONE authors for this list, but note any open PR they mention.
4. Read the repro-evidence block, all of it, before the plan. Write down: the environment, each numbered step with its artifact, and every control run with what it showed. Then write one line: "the evidence pins the failure to: ..." naming the component or condition the controls isolate.
5. Only now read the candidate plan. Write down its cause statement in one line, its list of changes, its files, its first step, its test plan, and any sentence marked as unknown or risk.
6. Read the candidate plan comment last. Write down any date or timeframe, any AI disclosure sentence, and whether it names the maintainer's direction or an open PR.

The order matters because the two hardest checks (`diagnosis-consistent` and `thread-followed`) compare the plan against things the reader must already hold in mind. Reading the plan first makes its story the frame, and the evidence gets read to fit it.

In live mode, replace step 4 with the student's posted repro comment on the issue (fetched with `gh issue view <number> --comments` or the API), and replace step 3 with the live thread. The candidate plan is `plan.md` and the comment is `comment.md` (or the file the student names). Do not read other files in the student's directory.

## Evidence gathering

For each check, the gathering move is fixed:

- `diagnosis-consistent`: take the cause line from step 5 and the step-4 notes. For each numbered step and each control run, write "explained" or "contradicts" next to it. One "contradicts" is the evidence.
- `cause-claims-backed`: list every sentence in the plan that says why the failure happens or where the fix goes. For each, find one of: an artifact in the repro evidence that shows it; a maintainer line from step 3 that states it; a hedge word in the same sentence or the risks section. Record which one, or "none".
- `targets-cause`: take the cause line and the list of changes. Ask whether the changes edit the thing the cause names. Record the change item and the mechanism it edits.
- `one-change`: take the list of changes. For each item, write the issue's failure line and ask whether the failure can stop without this item. Record the first item that is not needed, if any.
- `starting-point-named`: take the files list and the first step. Record the first named path or module, and the verb that starts the first step.
- `test-observable`: take the test plan. Record which repro step or command it names, and the exact expected result it states.
- `thread-followed`: take the maintainer list from step 3. For each cause, fix site or direction there, find where the plan or comment follows it or names it and explains the difference. Record "no maintainer signal", "followed", "named and differs with reason", or "silent".
- `policy-followed`: take the policy note from step 1. If disclosure is required, search the comment for a sentence about AI use. Record the sentence or "none".
- `not-in-scope-stated`: search the plan for "not in scope", "out of scope", "out:", "not touching", "deferred", "leaving". Record the line or "none".
- `open-prs-acknowledged`: take open PRs from step 3. Search the comment for each PR number. Record found or missing.
- `no-deadline`: search the comment for a day name, a date, "this week", "this weekend", "by ", "within N days", "soon" attached to a delivery. Record the phrase or "none".

Live mode: gather the thread and policy facts from GitHub (`gh issue view`, `gh api repos/<owner>/<repo>/contents/CONTRIBUTING.md`, any `AI_POLICY.md` in the repo root), and treat the student's posted repro comment as the repro-evidence block. If the student has no posted repro comment (house issue), the repro evidence is whatever the drafts quote; if they quote none, `diagnosis-consistent` and `cause-claims-backed` are graded against an empty evidence block and record that absence.

## Check execution

1. Execute the required checks in this order: `diagnosis-consistent`, `cause-claims-backed`, `targets-cause`, `one-change`, `starting-point-named`, `test-observable`, `thread-followed`, `policy-followed`. Then the three preferred checks. Every check is executed and reported even after a required check has failed; the verdict is assembled at the end, not on the first fail.
2. For each check, apply the pass condition in `rubric.md` literally to the gathered record from the section above. Grade `pass` when the condition holds, `fail` when the record shows it does not hold, and `unclear` only when the package does not contain the part the check reads at all (for example, no repro-evidence block, or no test plan section of any kind). Missing content inside a part that exists is a `fail`, not an `unclear`: a test plan that names no expected result exists and fails.
3. Each grade must carry one line of evidence quoted or paraphrased from the package: the contradicting control, the unbacked sentence, the scope-creep item, the first-step verb, the expected result, the maintainer line, the policy line. "Looks fine" is not evidence; when a check passes, quote the fact that satisfied it.
4. A check may be graded from the gathered record without re-reading the whole package. Re-read the package only when the record is missing the fact the check needs; then add it to the record so the next check does not repeat the search.
5. Never grade a check on the length, headings, or polish of the plan. A terse plan with a named file, a backed cause and an observable test passes; a long plan with headings and a wrong cause fails.
6. Do not skip a check because another check already failed on the same sentence. Report each check on its own evidence.

## Verdict assembly

1. Apply the verdict rule from `rubric.md`: `accept` only if all eight required checks are `pass`. Any required `fail` gives `reject`. Any required `unclear` is treated as `fail` and gives `reject`. Preferred checks never change the verdict.
2. In the summary, name the deciding check: on a reject, the first required check in execution order that failed, with its evidence line; on an accept, state that all required checks passed and list any preferred check that failed so the author can tighten the plan before posting.
3. In live mode, after the verdict, hold the comment against `voice-guide.md` and quote any rule the draft breaks. This never changes the verdict.
4. Emit the fenced JSON block last, in the shape SKILL.md gives, with every check listed (required and preferred) in execution order, each with its grade and its one-line evidence, and the verdict. Nothing follows the JSON block.
