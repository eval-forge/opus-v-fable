# Opus vs Fable: The Lost Temple

Results page and playable builds for the Eval Forge "Lost Temple" eval: one prompt for a full Three.js first-person relic-heist game, run on Opus 5.5 (medium, high, xhigh, max) and Fable 5.2 (high, xhigh).

- `index.html` is the results page.
- Each `<model>-<effort>/` folder holds that entry's production build (open its `index.html`), and `<model>-<effort>/source/` holds the source exactly as merged in eval-forge/eval-forge.github.io under `evals/lost-temple/`.
- `play/<model>-<effort>/` wraps each build with the Eval Forge info bar (model, effort, time, cost, total tokens) and frames the untouched build below it. The build folders themselves are never edited.
- `previews/` holds main-menu screenshots used on the cards.
- `JUDGING.md` has the scoring rubric (Score = S × P), what the judge actually ran on each build, and the reasoning behind every score.

All six entries are in: PRs #7-#10, #12 and #14.
