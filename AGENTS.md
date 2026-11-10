# ML4T Diagnostic - Agent Guide

Statistical validation and diagnostics for ML-based quantitative trading workflows.

## Start Here

- Package index: [src/ml4t/diagnostic/AGENTS.md](src/ml4t/diagnostic/AGENTS.md)
- Public docs landing page: [docs/index.md](docs/index.md)
- Book mapping: [docs/book-guide/index.md](docs/book-guide/index.md)

## Where To Work

| Need | Location |
|------|----------|
| Signal analysis and IC workflows | `src/ml4t/diagnostic/signal/` |
| Purged CV, held-out tests, fold persistence | `src/ml4t/diagnostic/splitters/` |
| Statistical tests and inference | `src/ml4t/diagnostic/evaluation/stats/` |
| Feature metrics and importance | `src/ml4t/diagnostic/metrics/` |
| Trade SHAP diagnostics | `src/ml4t/diagnostic/evaluation/trade_shap/` |
| Factor attribution and exposures | `src/ml4t/diagnostic/evaluation/factor/` |
| Backtest bridges and tearsheet entry points | `src/ml4t/diagnostic/integration/` |
| Feature triage pipeline | `src/ml4t/diagnostic/selection/` |
| Plotly reporting and tearsheets | `src/ml4t/diagnostic/visualization/` |
| Result schemas and summaries | `src/ml4t/diagnostic/results/` |

## High-Value Entry Points

```python
from ml4t.diagnostic import ValidatedCrossValidation, analyze_prepared_signal, analyze_signal
from ml4t.diagnostic.evaluation import FeatureDiagnostics, TradeShapAnalyzer
from ml4t.diagnostic.integration import generate_tearsheet_from_result
from ml4t.diagnostic.selection import FeatureSelector
from ml4t.diagnostic.splitters import CombinatorialCV, WalkForwardCV
```

## Quality Commands

```bash
uv run ruff check src/ tests/
uv run ruff format --check src/ tests/
uv run ty check
uv run pytest tests/ -q -n auto --timeout 120
uv run mkdocs build --strict
pre-commit run --all-files
```

Default pytest selection covers source checks. The `native_browser` integration checks run
serially in every supported platform cell defined by
[the native test workflow](.github/workflows/test-matrix.yml); that stage is also required
when qualifying reports.

## Notes

- `ml4t.diagnostic` sits between `ml4t-engineer` and `ml4t-backtest` in the ML4T stack.
- Treat the documentation and book guide as part of the public package surface.
- Update this guide when public entry points, subsystem ownership, authoritative documentation
  paths, or quality commands change. Keep current issue state and private workspace references out
  of public guides.

## Maintainer-directed change batching

For our own fixes and agent work directed by the maintainer, use a bounded, coherent PR rather
than one PR per small defect or implementation step. One PR may close several related issues
when the changes serve the same user workflow, subsystem repair or compatibility objective and
can be reviewed and reverted together. Keep implementation, regressions, documentation and
follow-up repairs together; use focused commits and retain evidence for each defect. Do not
create separate issues or PRs merely for individual files, tests, assertions or platform variants.

Split unrelated changes, independently risky or breaking changes, separate ownership boundaries,
and work that needs independent release or rollback decisions. A general maintenance label alone
does not make a batch coherent. Reuse or safely consolidate existing owned submissions instead of
opening overlapping PRs; preserve contributor attribution, recoverable history and other owners'
active work. Do not rewrite or close someone else's work merely to reduce the queue count.

Run focused checks while iterating, then qualify the complete final revision with every applicable
required gate. Group related work before expensive CI; do not weaken checks, bypass review or
claim old-head checks qualify changed code. This maintainer-directed batching rule takes precedence
over a literal one-issue-per-PR reading of contributor intake guidance.

<!-- OPENWIKI:START -->

## OpenWiki

This repository has a generated `openwiki/` evidence index. It is optional just-in-time context, not required startup reading.

- Do not enumerate, preload, or search wikis at task start. Use retrieval when the user asks for it, when unfamiliar architecture or dependency behavior materially affects the task, or when source inspection leaves an important uncertainty. Stop once the question is grounded.
- When those conditions apply and OpenWiki retrieval tools are available, use `openwiki_search` for just-in-time context and `openwiki_read` for the relevant complete sections. If search returns `workspace_required`, ask which listed workspace to use and retry with its ID.
- Use `openwiki_list_workspaces` or `openwiki_list_wikis` when workspace membership itself needs to be discovered.
- If the retrieval tools are unavailable, read `openwiki/quickstart.md` and follow its links to the relevant pages.
- Treat source code and tests as authoritative. A brief's unknowns and review items are verification gaps, not automatic requirements.
- Prefer the narrowest quiet validation that proves the changed behavior. Preserve complete failure output.

Use the repository's documented maintenance process to refresh generated pages after source changes. Check its configuration and completed runs before relying on automatic updates. Do not hand-edit generated OpenWiki pages unless explicitly asked; prefer updating source code/docs and letting OpenWiki regenerate.

<!-- OPENWIKI:END -->
