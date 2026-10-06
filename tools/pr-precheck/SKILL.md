---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer one question and no other: is this pull request ready to submit? "Ready" means the diff delivers the plan the author posted (or honestly says where it departs), the evidence shows the fix working on the reproduction the plan named, the diff carries nothing a reviewer must read around, and the repo's stated asks are met. Do not grade whether the fix is clever, whether the plan was the best plan, or whether the code style pleases you.

A PR package is five artifacts read against two references. The five artifacts are the candidate PR's title, description, commit list, diff, and test evidence. The two references are the plan the PR claims to implement (its scope, its not-in-scope line, its files, its test plan, and any deviation or deferral notes, together with the reproduction evidence that plan was built on) and the issue the plan belongs to (its title, body, thread highlights, and the repo-facts block that states the repo's PR template asks and contribution policy). Every check in `rubric.md` reads one of the five artifacts against one of the two references or against the repo's stated asks. Grade nothing that is not in the package.

## Inputs and modes

Run in exactly one of two modes. Decide which at the start of every run: a request that names a bundle file or bundle id is eval mode; a request that names a branch, an issue URL, or draft files in a working copy is live mode.

Live mode inputs, each from a named source:

- The plan: `plan.md` in the working copy of the fork (the file the author committed as `beat-1-sandbox/unit-3/plan.md` in week 3), including its dated "Deviations" section. A house-chain student reads the house plan the instructor routed them to instead; the file name is the one the student gives in the request.
- The diff: everything the branch changes relative to the repo's default branch. Produce it with `git diff main...HEAD` (three dots), run from the working copy on the branch. Also run `git log main..HEAD --oneline` for the commit list. Do not read the working tree for the diff; the branch's committed changes are the diff.
- The draft PR title and description: `pr_draft.md` in the working copy (first line is the title, the rest is the description), or the file the student names.
- The test evidence: `test_evidence.md` in the working copy (the reproduction's before and after output captured on the branch, and the output of the repo's own checks), or the file the student names.
- The issue: the issue URL or number the request names, read with `gh issue view <number> --comments` or `gh api repos/<owner>/<repo>/issues/<number>` plus its comments; the student's posted reproduction comment on that thread is the repro evidence. The repo's asks come from `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` in the working copy (fall back to `CONTRIBUTING.md` and any `AI_POLICY.md` at the repo root).

Read nothing else in the working copy: no other files, no other branches, no prior tool output.

Eval mode: the bundle is the whole world. Everything the checks read is inside the one package file (repo facts, issue, thread highlights, plan context with its repro evidence, and the candidate PR with title, description, commits, diff and test evidence). Fetch nothing, open no URL, read no other file, and never substitute what the live issue says today for what the bundle says. Every check in `rubric.md` runs, and the full verdict rule applies: a bundle gets accept or reject under the same rule a live PR gets.

## The scope seam (live mode only)

In live mode, before reading any input, read `scope.md` and take two things from it: the `Repo:` line under "Where your pull request lives", and the house rules. Then compare the repository the request names (the issue URL's owner/repo, and the working copy's `origin` or upstream remote) with the `Repo:` line. If they do not match, refuse: say which repository the request named, say that `scope.md` scopes this tool to the Path Review repository, grade nothing, and emit no verdict JSON. If the `Repo:` line still reads as a bracketed placeholder (`<ORG>/<PATH-REVIEW-REPO>` or any `<...>` text), stop before grading: say that the scope's repo line is unfilled, tell the student to get the Path Review repo link from the Unit 1 Check-In page or their instructor, and emit no verdict JSON.

When the repository matches, carry the house rules into the run as facts to report: the PR must come from a `fix/<issue-number>-<slug>` branch on the fork, one PR per issue, the repo's PR template must be used with every section filled including disclosure where it applies, and a classmate's PR on the same issue does not block this one. Report any house rule the branch or draft breaks under the verdict, beside the rubric's own checks; the rubric's `template-sections-filled` check already grades the template ask, so do not grade it twice.

Eval mode ignores `scope.md` entirely: the bundle's repo-facts block is the only source of the repo's asks, and no repository is out of scope for a bundle.

## The voice seam (live mode only)

In live mode, after the verdict is assembled and before the JSON block, read `voice-guide.md` and hold two pieces of outgoing text against it: the draft PR title and the draft PR description from `pr_draft.md`. These are the words a maintainer will read under the student's name; nothing else in the package is gated here (the plan and the test evidence are not outgoing text).

For each rule in the guide, read the title and description for a sentence that breaks it, and report every break as one line: the rule's name, the sentence quoted, and what the guide's "Right" example does instead. Report "voice guide: no rule broken" when nothing breaks. A voice break never changes the verdict: the verdict is the rubric's, and a PR with a clumsy sentence and a clean diff is still ready, while a PR with a perfect description and an untested fix is still held. The student fixes the sentence; the tool only points at it.

Eval mode ignores `voice-guide.md` entirely. The bundles are instructor-authored and carry no voice labels, so no voice line appears in an eval run's output.

## Component reads

Three components do the grading, and this frame only connects them:

- `rubric.md` defines the checks (name, evidence, pass condition, weight) and the verdict rule. Grade exactly the checks it lists, under the names it gives, and no others. The pass condition is applied as written; do not add a condition the row does not state, and do not relax one because the fix looks good.
- `references/evidence-guide.md` is the map: for each evidence family it says where in a bundle (or, live, in which file, diff or page) the evidence lives and what a pass looks like. When a check's evidence row names a family, go to the guide's heading for that family to find it.
- `procedure.md` is executed as written: its read order, its gathering move per check, its execution order, and its verdict assembly. Do not reorder the reads and do not grade a check from a different part of the package than the procedure names.

When `procedure.md` is silent on a step the run needs (a check with no gathering move, an input the read order does not place, a tie the verdict assembly does not break), do not improvise around it: report the gap in one line under the summary ("procedure gap: no gathering move for <check>"), grade that check `unclear` with the gap as its evidence line, and continue with the checks the procedure does cover.

When `rubric.md` has no check rows or no verdict rule, or `procedure.md` has no steps under its headings, refuse to grade: say which file is empty, say that this tool grades only with a filled rubric and procedure, and emit no verdict JSON. A frame cannot stand in for the rubric; the judgment lives in those files.

## Verdict and output

The verdict space is binary: `accept` means the PR is ready to submit (open it, or in eval mode the bundle would have been ready), and `reject` means hold it and fix what the deciding check names. There is no third verdict; `unclear` exists only as a per-check grade, and the rubric's verdict rule says how it enters the verdict.

Write the reply in this order: a one-paragraph summary naming the verdict and the deciding check with its evidence line (or, on accept, naming the preferred checks that failed, if any); the live-mode scope and voice reports when in live mode; then the fenced JSON block below, with every check from `rubric.md` listed in the procedure's execution order, each with its grade and one-line evidence, and the verdict. The JSON block is the last thing in the reply: valid JSON, nothing after it, no trailing prose.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

In eval mode `item` is the bundle id (for example `pkg-07`); in live mode it is the PR URL if the PR exists, otherwise the fork branch and issue number (`bhargavchintam:fix/53-parenthesized-phone for #53`).

## Grading discipline

Grade under these standing rules, in this order of authority:

1. Evidence first. Gather the record the procedure describes before grading any check, and grade every check from a quoted or paraphrased fact in the package. A claim with no artifact behind it ("tested locally", "no changes beyond the fix") is graded by what the diff and evidence show, not by what the description says.
2. Grade the thing, not the polish. The diff, the evidence, and the repo's stated asks are the graded objects. The length of the description, the number of commits, the presence of headings, and the tone of the prose never decide a check. A terse PR with a matching diff and a shown repro passes; a long one with a missing deliverable fails.
3. The rubric decides. A check passes or fails by its pass condition as written in `rubric.md`; this frame adds no conditions and the procedure adds none. When a package seems to deserve a different verdict than the rubric gives, report the rubric's verdict and say in the summary where the rubric and the reading differ; the rubric is revised between runs, never during one.
4. The procedure decides how. Read order, gathering moves, execution order and the deciding-check rule come from `procedure.md`. Two runs of the same package under the same files must produce the same grades.
5. Unclear is a fail by default. When `rubric.md`'s verdict rule says how `unclear` is treated, follow it. When it is silent, treat an `unclear` grade on a required check as a fail: a claim the package does not let the grader verify is not evidence, and a PR whose readiness cannot be verified is not ready.
6. Honest shortfalls are not fails. A deliverable the plan or the description defers with a reason, a deviation recorded with a date and a reason, or a limit stated plainly is graded as the rubric says, and the rubric accepts it. Only silent shortfalls, unshown tests, buried diffs and ignored asks hold a PR.
