# About this fork

A pinned copy of [razorback16/cc-intelligent-ui](https://github.com/razorback16/cc-intelligent-ui). Nothing changes here unless it is reviewed and merged by hand.

- **Based on:** upstream `0731fc0` (0.3.5), reviewed for security on 2026-11-17 and merged as that exact commit. Token use was not reviewed again; the prompt text the mod adds (`TYPST_PROMPT`) is the same as in 0.3.2 (`62d4e7e`), which was reviewed for security and token use on 2026-10-22.
- **Tests at 0.3.5:** with Claude Code 2.1.326, `claude plugin test .` passes 978 of 980 tests. The two that fail are in `tests/images.test.ts` (the preview band of pasted images above the prompt box), and they fail the same way on unchanged upstream `0731fc0`.
- **Changes from upstream:** the marketplace is named `cc-intelligent-ui` (not `razorback16`), and the update notice's steps say to merge upstream into this fork first. The daily check still reads upstream's `plugin.json`, as an alert that upstream has a release to review.
- **GitHub settings:** the `Deploy site to GitHub Pages` workflow (`.github/workflows/pages.yml`) is disabled on this fork. It publishes upstream's site, and GitHub Pages is not set up here, so it would only fail. The setting is not in the repo: `gh workflow enable pages.yml` turns it on again.

## Install

```
claude plugin marketplace add swamidigital/cc-intelligent-ui
claude plugin install cc-intelligent-ui@cc-intelligent-ui
```

Needs Typst 0.15 (`brew install typst`; `brew pin typst` keeps a later Typst from breaking the drawing).

Do not run upstream's one-line installer (`curl … install.sh | bash`, which the README names). It installs the mod from upstream, uninstalls this fork's copy, and turns on auto-update for upstream in `~/.claude/settings.json`.

## Bring in an upstream release

```
git fetch upstream
git log --oneline HEAD..upstream/main
git diff HEAD upstream/main -- hooks/ skills/ render/*.typ .claude-plugin/
```

Read the diff before you merge. Look most closely at:

- `hooks/lib/plan.tsx` (`decisionOf`: the permission mode a plan approval sets);
- `hooks/lib/api.ts` and the hook bodies of `hooks/register.tsx` (the host capabilities the mod calls; since 0.3.3 both call `$`);
- `hooks/lib/updates.ts` (network);
- `hooks/lib/open.ts` (the program a click runs);
- any new `exec` or `spawn` call (`exec` was named `processRun` before 0.3.3).

This lists every host call:

```
git grep -n -E '\$\.[a-z]+\.[a-zA-Z]+' -- hooks
```

`claude plugin test .` runs only the `*.test.ts` files. Run the `tests/*-sh-test.sh` scripts only from a throwaway clone: three of them (`install`, `root`, `fill`) delete the folder they start in when `mktemp` fails.

Then merge the commit you reviewed, not whatever `upstream/main` is by then:

```
git merge <reviewed commit>      # keep this fork's marketplace name and update steps
claude plugin test .
git push
claude plugin marketplace update cc-intelligent-ui
claude plugin update cc-intelligent-ui@cc-intelligent-ui
```

Then `/reload-plugins` in Claude Code.
