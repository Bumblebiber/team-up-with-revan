# team-up-with-revan

Revan, the review specialist for [team-up](https://github.com/Bumblebiber/team-up).

One repository, one specialist. This one holds a manifest, instructions, a
skill and an eval suite — no code, no model names, no install hooks.

## What Revan does

Reviews a diff along two axes and reports them separately:

- **Standards** — does the change follow the repository's documented standards,
  and the Fowler smell baseline where the repository documents nothing?
- **Spec** — does the change do what was actually asked for?

A change can pass one and fail the other, which is why the two lists are never
merged or reranked.

Revan reviews the **merged** result of a batch of changes, not one ticket at a
time: the defect worth catching is the one no single ticket contains, where two
diffs are each correct alone and wrong together. It runs in a fresh session and
is deliberately not given the plan — a reviewer who has read the argument for a
change reviews the argument instead of the code.

It reviews. It does not fix, patch, or rewrite the spec it reviews against.

## Install

```bash
git clone https://github.com/Bumblebiber/team-up-with-revan
team-up specialist inspect ./team-up-with-revan     # read-only, always first
team-up specialist install ./team-up-with-revan
team-up specialist approve review.revan@0.1.1 --project /abs/path/to/project
```

Approval binds the project, id, version, checksum and permissions together;
changing any of them requires approving again.

## Permissions

| | |
|---|---|
| filesystem | `project_readonly` |
| writes | `false` |
| network | `false` |
| commands | none |

## License

MIT
