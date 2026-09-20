# Atlas

Atlas is an [Agent Skill](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) that
documents a whole project as one system, in one voice, with every claim grounded in the code.

Point an agent at a repository and say *"document this project"* or *"the docs don't match the
code anymore"*. It settles scope with you, reads 100% of the non-test source, drafts a page map,
writes the pages in parallel, and puts every page through two independent reviews before it
counts as done.

```bash
npx skills add NoMercyLabs/atlas
```

Or clone it straight into your skills directory:

```bash
git clone https://github.com/NoMercyLabs/atlas /tmp/atlas \
  && cp -r /tmp/atlas/skills/atlas ~/.claude/skills/
```

## What makes it different

Most documentation prompts are promises. This one is gates.

| Gate | What has to exist on disk | What rejects the work |
| --- | --- | --- |
| Scope | `docs-work/scope.md` | Nothing is read in depth before you answered what is in and what is out. |
| Coverage | `docs-work/slices/*.md` | `check_docs.py coverage` diffs the union of every slice's file list against a real walk of the tree. |
| Map | `docs-work/map.md` | `check_docs.py map` checks every source path is claimed or excluded with a reason, every concept is owned once, every link names a planned page. |
| Reviews | `docs-work/reviews/*.md` | `check_docs.py reviews` wants two `Verdict: PASS` files per page, each stamped with the page's content hash. Edit the page and the verdict goes stale. |
| Delivery | `docs-work/report.md` | `check_docs.py status` prints DELIVERED or INCOMPLETE with the fraction, and that block opens the report. |

The checker needs Python 3 and nothing else.

## The voice

Three commitments, and everything else in the skill is those three written down:

- Every statement is grounded in something observed in the code, config, or a command's output.
- The set holds together as one whole, with one vocabulary and no page contradicting another.
- The same thing is shaped the same way every time it appears.

Nothing language-specific ships with the skill. The toolchain answers (what makes a symbol
public, where defaults come from, what a test file is, what formats a snippet, what proves a
claim, what has already drifted) are derived from the repository in front of it, never recalled.

## Who does the work

Four roles, dispatched as subagents with a model per role:

| Role | Model | Why |
| --- | --- | --- |
| Scanner | sonnet | Highest volume, one per source slice, copies signatures verbatim. |
| Writer | opus | One per page, in parallel batches. A cheaper writer costs more fact-checks. |
| Fact-checker | opus | One per page. Runs the code, fetches the URLs, traces every claim to a line. |
| Reader | haiku | One per page. Naivety is the qualification: it notices the missing step a smarter model would fill in. |

## Documentation

- [Skill documentation](skills/atlas/README.md)
- [SKILL.md](skills/atlas/SKILL.md), the workflow the agent follows
- [The voice](skills/atlas/references/voice.md)
- [The checker](skills/atlas/scripts/check_docs.py)

## License

MIT, see [LICENSE](LICENSE).
