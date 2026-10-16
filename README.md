# append-only-review-demo

A small repo that compares two ways to handle code review feedback on a
GitHub pull request. Same change, same feedback, two different histories.

- **Amend and force push.** After each round of feedback, rewrite the
  commit and force push. The branch always holds one commit.
- **Append-only.** After each round of feedback, add a commit that says
  what changed. Squash when the pull request merges.

Both end with identical code on `main`. The difference is what a reviewer
can see while the review is happening.

The code is pseudocode about cars. It's only there so the commits have
something to change. Read the commits, not the code.

## The scenario

Starting point (`main`): a `Car` class in `src/car.pseudo`.

The change: add an `ElectricCar` that extends `Car`.

The review feedback, in two rounds:

1. "`accelerate` accepts negative amounts. Reject them."
2. "`battery` is ambiguous. Rename it to `chargePercent`."

## The branches

| Branch | What happened | What a reviewer sees |
|---|---|---|
| `feature/amend-and-force-push` | Each round was amended into the original commit and force pushed. | One commit. Rounds 1 and 2 can't be seen on their own. |
| `feature/append-only` | Each round got its own commit. | Three commits. Each round is its own small diff. |
| `result/squashed` | `feature/append-only`, squashed at merge. | One commit on top of `main`. |

The final code on all three branches is identical.

## See it yourself

On GitHub, compare the commit lists:

- [Append-only commits](https://github.com/zfand/append-only-review-demo/commits/feature/append-only)
- [Amend-and-force-push commits](https://github.com/zfand/append-only-review-demo/commits/feature/amend-and-force-push)

Or clone it:

```sh
git clone https://github.com/zfand/append-only-review-demo
cd append-only-review-demo
git fetch origin '+refs/heads/*:refs/heads/*'

# Append-only: one line per review round
git log --oneline main..feature/append-only

# What changed in round 2? One command.
git show feature/append-only

# Amend and force push: one commit. The rounds are gone.
git log --oneline main..feature/amend-and-force-push

# Same final code either way (no output means no difference)
git diff feature/append-only feature/amend-and-force-push
```

## Why it matters on GitHub

A GitHub pull request tracks a branch, not revisions of a commit. When the
branch is rewritten:

- The reviewer can't look at just what changed since their last review.
  They re-read the whole diff to find it.
- Inline comments on rewritten lines get marked outdated and lose their
  place in the code.
- There's no record of which commit answered which piece of feedback.

With append-only commits, each round is a small diff the reviewer can
check against their own comment. Squash at merge and `main` looks the same
as if the branch had been one commit all along.

## When amending is fine

Gerrit, Phabricator and Graphite track each revision of a change for you.
Reviewers can diff revision 2 against revision 3 even though the commit
was rewritten. If your team uses one of those, amend and force push all
you like. Plain GitHub doesn't do this, so the commits have to carry that
history.

## What about small changes?

Keeping changes small is about scope: one change should do one thing. It
doesn't mean review iterations should be hidden. Keep the change small.
Keep the iterations visible.

## The rule

See [CONTRIBUTING.md](CONTRIBUTING.md). Two lines.
