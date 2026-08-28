# code-review — Two-axis review of a diff

Review the diff between `HEAD` and a fixed point along two axes that are
reported separately:

- **Standards** — does the code conform to this repository's documented
  standards, and to the smell baseline below?
- **Spec** — does the code faithfully implement what was asked for?

A change can pass one and fail the other. Code that follows every convention
while implementing the wrong thing passes Standards and fails Spec; code that
does exactly what was asked while breaking the repository's conventions does
the reverse. Reporting them separately is what stops one from masking the
other, so never merge or rerank the two lists.

This runs in one session. There are no sub-agents here, so read for one axis
at a time rather than trying to hold both while reading.

## 1. Pin the fixed point

The request names it: a commit, a branch, a tag, or a merge base. Resolve it
before reading anything (`git rev-parse`), and confirm the diff is non-empty.
A bad ref is a `blocked` result, not a review of nothing.

Use three-dot form so the comparison is against the merge base:

    git diff <fixed-point>...HEAD
    git log <fixed-point>..HEAD --oneline

## 2. Find the spec

In order: a path given in the request; a spec file under `docs/`, `specs/` or
similar matching the branch or feature; an issue reference in the commit
messages. If none of these produces a spec, skip the Spec axis and say so in
the result — do not substitute your own idea of what the change should do.

## 3. Find the standards

Whatever the repository documents about how its code should be written —
`CONTRIBUTING.md`, `CODING_STANDARDS.md`, `AGENTS.md`, `CLAUDE.md`, a style
section in the README.

On top of that, the Standards axis always carries the smell baseline below,
even when the repository documents nothing. Two rules bind it:

- **The repository overrides.** A documented standard always wins. Where it
  endorses something the baseline would flag, suppress the smell.
- **Always a judgement call.** Each smell is a labelled heuristic ("possible
  Feature Envy"), never a hard violation. Skip anything tooling already
  enforces.

### Smell baseline (Fowler, *Refactoring* ch. 3)

Each reads *what it is* → *how to fix*. Match against the diff, not against the
whole repository.

- **Mysterious Name** — a function, variable or type whose name does not reveal what it does or holds. → Rename it; if no honest name comes, the design is murky.
- **Duplicated Code** — the same logic shape in more than one hunk or file in the change. → Extract the shape, call it from both.
- **Feature Envy** — a method that reaches into another object's data more than its own. → Move the method onto the data it envies.
- **Data Clumps** — the same few fields or parameters keep travelling together. → Bundle them into one type.
- **Primitive Obsession** — a primitive or string standing in for a domain concept. → Give the concept its own small type.
- **Repeated Switches** — the same switch or if-cascade on the same type recurs across the change. → Polymorphism, or one shared map.
- **Shotgun Surgery** — one logical change forces scattered edits across many files. → Gather what changes together.
- **Divergent Change** — one module is edited for several unrelated reasons. → Split so each module changes for one reason.
- **Speculative Generality** — abstraction, parameters or hooks for needs the spec does not have. → Delete it; inline back until a real need shows.
- **Message Chains** — long `a.b().c().d()` navigation the caller should not depend on. → Hide the walk behind one method.
- **Middle Man** — a class or function that mostly delegates onward. → Cut it, call the real target.
- **Refused Bequest** — a subclass that ignores or overrides most of what it inherits. → Composition instead.

## 4. Read for Standards

Report, per file or hunk where it helps:

- every place the diff violates a documented standard — cite the file and the
  rule;
- any baseline smell — name it and quote the hunk.

Mark each finding as a hard violation or a judgement call. A documented
standard can be a hard violation; a baseline smell never is.

## 5. Read for Spec

Report:

- requirements the spec asked for that are missing or only partly done;
- behaviour in the diff nobody asked for — scope creep;
- requirements that look implemented but where the implementation is wrong.

Quote the spec line for each finding.

## 6. Result

Two lists, under their own headings, in that order. Then one line per axis:
how many findings, and the worst one within that axis. Do not pick a winner
across axes.

State what you could not check: a missing spec, a file outside read scope, a
check that needed to run something. Silence about a gap reads as a clean
review of it.
