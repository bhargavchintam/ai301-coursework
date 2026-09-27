# Evidence guide: where evidence lives in a plan package

This file is the map for the checks in `rubric.md`. `procedure.md` says when to gather each family; this file says where it is and what a pass looks like.

## Diagnosis and grounding

Where it lives. In an eval bundle: the plan's cause statement is in the "Candidate plan" section, usually under a "Diagnosis" or "Cause" heading or in the first sentence starting "Diagnosis:". The behaviour it must explain is in the "Repro evidence" block: the numbered steps, the fenced artifacts, and every line starting "Control" or "Control run". Maintainer statements about cause are in "Thread highlights", on lines whose author association is OWNER, MEMBER or COLLABORATOR. In live mode: the diagnosis in the draft `plan.md`, the student's posted repro comment on the issue, and the issue thread on GitHub.

What good looks like. Write the cause in one line, then go through every repro step and control run and ask: does this line still hold? A cause holds when each artifact is what that cause would produce. It fails when a control run shows the blamed component working (the cause says a module is missing but a control shows the module printing its message), or when the cause needs something the evidence rules out (the cause blames a post-read cast but a control shows the data already changed before the cast runs). For `cause-claims-backed`, list each sentence that names a cause or a fix site and tag it: artifact, maintainer, or flagged as unverified. A sentence with no tag is the failure.

## Scope

Where it lives. In an eval bundle: the plan's "Scope", "In scope", "Not in scope", "Out", or "Proposed changes" text, plus its "Files" or "Files and areas" list. In live mode: the same headings in the draft `plan.md`.

What good looks like. Count the things the plan will change. One bounded change is a set of edits that all serve the same cause the diagnosis names, in the files that mechanism lives in, plus the tests that prove it. Anything the failure does not need is scope creep: a dependency upgrade, a library migration, a module split, a new abstraction shared across runtimes, a new user option, a CI matrix. One such item is enough to fail `one-change`. The not-in-scope line is separate: a plan can be bounded without stating what it leaves out, which is why `not-in-scope-stated` is only preferred.

## Executability

Where it lives. In an eval bundle: the plan's "Files" list and "Approach", "Changes" or "Steps" section. In live mode: the same in the draft `plan.md`.

What good looks like. A stranger reads the first step and knows which file to open and what to type. A path such as `src/output.rs` or a function such as `delpaths_sorted` in `src/jv_aux.c` is a starting point; "the client attach path in zellij-server" is a module-level starting point and still passes because a reader knows where to begin. A first step that reads "profile", "investigate", "look into", "figure out where", or "poke around" is not a starting point, and neither is a step whose location is "somewhere around". Order of work matters less than the first step being a code change at a named place.

## Test plan

Where it lives. In an eval bundle: the plan's "Test plan" or "Test" section, read next to the "Repro evidence" steps and artifacts. In live mode: the test plan in the draft `plan.md` next to the student's posted repro comment.

What good looks like. The test plan re-runs something from the repro evidence (a numbered step, a command, a control run) and states what the output must be afterwards: a value, an exit code, a message text, a line that must no longer appear, a class that must be absent. "Step 3 must show the pushed colour without leaving the view" passes. "Sync succeeds 10 of 10 runs" passes. "The prompt should feel fast", "scrolling should work", "the panic should go away", and "run the full suite and make sure nothing regresses" with no repro step named all fail.

## Honesty

Where it lives. In an eval bundle: the plan's "Risk", "Risks and unknowns", "Open question" lines, and any sentence with "not yet", "unknown", "may", "I have not". Live mode adds the "Deviations" section of `plan.md` after a build.

What good looks like. Honesty is graded through `cause-claims-backed`: a claim about code the author has not shown evidence for is fine when it is marked as a guess, and not fine when it is stated as fact. Words that signal false certainty over an unverified claim: "clearly", "the defect is", "the root cause is", "simply", when neither an artifact nor a maintainer backs them. A stated unknown ("the exact clamp site may move one level during implementation") is a pass, not a weakness. After a build, an honest deviation is a dated note in the plan saying what changed and why; a deviation that only exists in the diff is the failure.

## Comms

Where it lives. In an eval bundle: the "Candidate plan comment" section, read against "Thread highlights" (maintainer-named causes, directions, open PRs) and the repo-facts block's "contribution policy" line. In live mode: the draft `comment.md`, the issue thread on GitHub, `CONTRIBUTING.md` and any `AI_POLICY.md` in the repo.

What good looks like. Three separate reads. First, `thread-followed`: if a maintainer named a cause or direction, the comment or plan either follows it or names it and explains the difference; silence while going a different way fails. Second, `policy-followed`: if the policy says AI use must be disclosed on comments or issues, the comment must contain the disclosure sentence; a policy that only conditions pull requests, or says nothing, passes without one. Third, the preferred reads: an open PR named in the thread should be acknowledged, and the comment should not promise a date ("this weekend", "this week", "by Friday").
