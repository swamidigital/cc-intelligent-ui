# About this fork

A pinned copy of [razorback16/cc-intelligent-ui](https://github.com/razorback16/cc-intelligent-ui). Nothing changes here unless it is reviewed and merged by hand.

- **Based on:** upstream `62d4e7e` (0.3.2), reviewed for security and token use on 2026-10-22.
- **Changes from upstream:** the marketplace is named `cc-intelligent-ui` (not `razorback16`), and the update notice's steps say to merge upstream into this fork first. The daily check still reads upstream's `plugin.json`, as an alert that upstream has a release to review.

## Install

```
claude plugin marketplace add swamidigital/cc-intelligent-ui
claude plugin install cc-intelligent-ui@cc-intelligent-ui
```

Needs Typst 0.15 (`brew install typst`; `brew pin typst` keeps a later Typst from breaking the drawing).

## Bring in an upstream release

```
git fetch upstream
git log --oneline HEAD..upstream/main
git diff HEAD upstream/main -- hooks/ skills/ render/*.typ .claude-plugin/
```

Read the diff before you merge. Look most closely at `hooks/lib/plan.tsx` (`decisionOf`: the permission mode a plan approval sets), `hooks/lib/api.ts` (every host capability the mod calls), `hooks/lib/updates.ts` (network), and any new `processRun` or `spawn` call. Then:

```
git merge upstream/main          # keep this fork's marketplace name and update steps
claude plugin test .
git push
claude plugin marketplace update cc-intelligent-ui
claude plugin update cc-intelligent-ui@cc-intelligent-ui
```

Then `/reload-plugins` in Claude Code.
