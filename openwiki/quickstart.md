---
type: developer quickstart
title: Quickstart and Task Router
description: Setup and navigation guide for coding agents changing ml4t-diagnostic, with stable workflows, optional dependency boundaries, validation gates, and task-specific reading paths.
tags: [quickstart, development, testing, routing, python]
verified:
  - by: openwiki/0.9.0
    at: 2026-11-10T03:14:31.080Z
sources:
  - id: openwiki-source-8037e2358a2c4f9b2c722a11
    resource: repo://AGENTS.md
  - id: openwiki-source-f317ee207e1653d2033c81a4
    resource: repo://CONTRIBUTING.md
  - id: openwiki-source-05ccef8d4cf1698187f20464
    resource: repo://pyproject.toml
  - id: openwiki-source-23775c3de52f3ab95a13cb8b
    resource: repo://README.md
  - id: openwiki-source-a2130df640e742bc8a6e2256
    resource: repo://src/ml4t/diagnostic/__init__.py
  - id: openwiki-source-2eda9102af0d36a34a3fa514
    resource: repo://src/ml4t/diagnostic/AGENTS.md
  - id: openwiki-source-d128b7e05164a8158baf1bca
    resource: repo://src/ml4t/diagnostic/integration/AGENTS.md
  - id: openwiki-source-f2ee67f4ff0424c4e8202fc7
    resource: repo://src/ml4t/diagnostic/reporting/AGENTS.md
  - id: openwiki-source-eccdf8a01c5db864b307e663
    resource: repo://src/ml4t/diagnostic/splitters/AGENTS.md
  - id: openwiki-source-568e4fa95e09171792834bb9
    resource: repo://src/ml4t/diagnostic/visualization/AGENTS.md
  - id: openwiki-source-316d5fab41c6f23b351725ae
    resource: repo://tests/RUNTIME_CATALOG.md
generated: { by: "openwiki/0.9.0", at: "2026-11-10T03:14:31.080Z" }
---

# Quickstart and Task Router

`ml4t-diagnostic` is the diagnostics and validation layer for quantitative trading workflows. It evaluates cross-sectional signals, constructs leakage-aware validation folds, corrects strategy statistics for selection bias, analyzes feature, portfolio, and trade behavior, and turns owned analytical results or backtest artifacts into reports. It can consume products from the wider ML4T ecosystem, but core signal analysis is local and does not require an external service or special hardware.

## Start with the supported environment

The supported runtime is **Python 3.12, 3.13, or 3.14** on Linux, macOS, and Windows (`requires-python = ">=3.12,<3.15"`). Use `uv` for both consumer and contributor environments.

For a normal installation:

```bash
uv add ml4t-diagnostic
```

For repository development, install the complete development environment from the checkout:

```bash
uv sync --all-extras --dev
```

The core distribution already includes Polars, pandas, PyArrow, NumPy/SciPy, scikit-learn, statsmodels, Pydantic, `ml4t-specs`, and trading-calendar support. Do not add a heavyweight backend to core merely because one workflow can use it. Optional capabilities are deliberately separated:

| Extra | Add it when the task needs |
|---|---|
| `viz` | Plotly/Matplotlib/Seaborn charts, Kaleido static export, or PDF assembly |
| `ml` | LightGBM, XGBoost, or supported SHAP builds |
| `perf` | optional Numba acceleration |
| `backtest` | the `ml4t-backtest` result bridge |
| `data` | `ml4t-data` integration |
| `factors` | factor-data sourcing through `ml4t-data` |
| `dashboard` | Streamlit dashboards and supported SHAP builds |
| `all` | all supported optional capabilities |

Install a selected extra with, for example, `uv add 'ml4t-diagnostic[viz]'`. LightGBM needs an OpenMP runtime on macOS. SHAP and Numba are excluded on Intel macOS with Python 3.14 because compatible wheels are unavailable. Current Kaleido static image/PDF export can require local Chrome or Chromium. Keep imports lazy or guarded at these boundaries: importing `ml4t.diagnostic` must not make core analysis depend on Plotly, and top-level optional plotting exports are set to `None` when the visualization import is unavailable.

## Choose a public entry point before choosing a file

Public API stability follows package `__all__` exports, not every importable implementation symbol. Begin with a stable workflow and then follow its owned result and domain guide:

```python
from ml4t.diagnostic import ValidatedCrossValidation, analyze_prepared_signal, analyze_signal
from ml4t.diagnostic.evaluation import FeatureDiagnostics, TradeAnalysis, TradeShapAnalyzer
from ml4t.diagnostic.integration import (
    generate_tearsheet_from_result,
    generate_tearsheet_from_run_artifacts,
)
from ml4t.diagnostic.selection import FeatureSelector
from ml4t.diagnostic.splitters import CombinatorialCV, WalkForwardCV
```

Use `analyze_signal` when starting with factors and prices; use `analyze_prepared_signal` only for an already-complete panel with supplied outcomes and bucket assignments. Use `WalkForwardCV` or `CombinatorialCV` when the fold design itself is the product; use `ValidatedCrossValidation` when the intended workflow is CPCV followed by Sharpe estimation and Deflated Sharpe Ratio significance. Backtest integration entry points normalize a live result or saved run artifacts into analytical/profile inputs, while rendering is owned by the visualization layer.

Before changing a signature, default, metric definition, annualization, sign, clock, or result field, read [Data Contracts, Time Semantics, and Owned Results](concepts/contracts-time-and-results.md). For package ownership, dependency direction, and the distinction between stable and internal surfaces, read [System Architecture and Public Surfaces](architecture/system-map.md).

## Route by change task

This is the shortest useful path through the hierarchy; do not preload the entire source tree.

| If the change concerns… | Read first | Then inspect and test |
|---|---|---|
| Factor/signal preparation, forward outcomes, IC, quantiles, spreads, turnover, events, or `SignalResult` persistence | [Signal Analysis Workflow](workflows/signal-analysis.md) | `src/ml4t/diagnostic/signal/`, then focused `tests/test_signal/` |
| Purged walk-forward/CPCV folds, embargo, calendars, fold persistence, or CPCV-to-DSR orchestration | [Leakage-Aware Cross-Validation and Statistical Validation](workflows/validated-cross-validation.md) | `splitters/` for fold ownership, `evaluation/stats/` for inference, and `tests/test_splitters/` plus relevant evaluation tests |
| Look-ahead auditing, feature quality, drift, importance, interactions, SHAP, or ordered feature triage | [Feature Diagnostics, Causality, and Selection](workflows/feature-diagnostics-and-selection.md) | `evaluation/`, `metrics/`, or `selection/` according to ownership; use matching evaluation, metrics, and selection tests |
| Portfolio risk, stress/live splits, wealth cones, capacity, holdings/fills, FIFO round trips, trade diagnostics, or factor attribution | [Portfolio, Trade, and Factor Analysis](workflows/portfolio-trade-and-factor-analysis.md) | `evaluation/portfolio_analysis`, `evaluation/factor/`, and `evaluation/trade_shap/`; run focused evaluation tests |
| A live `BacktestResult`, saved run directory, calendar resolution, normalization, profile construction, or HTML tearsheet entry point | [Backtest and Artifact Integration to Tearsheet](integrations/backtest-to-tearsheet.md) | `integration/` first, then `visualization/backtest/`; use `tests/test_integration/` and relevant visualization tests |
| A generic HTML/JSON/Markdown renderer, a Plotly chart, dashboard section, backtest report section, or static export | [Reporting and Visualization Boundaries](integrations/reporting-and-visualization.md) | `reporting/` only for structured-result transport renderers; `visualization/` for domain plots and composed reports |
| Alphalens, Empyrical, Pyfolio, or Zipline-compatible behavior | [Legacy Compatibility](integrations/legacy-compatibility.md) | `compat/` and `tests/test_compat/`; delegate calculations to native kernels rather than creating a second analytics implementation |
| A schema, serialization rule, pandas/Polars boundary, missing-value policy, observation/session clock, or configuration contract | [Data Contracts, Time Semantics, and Owned Results](concepts/contracts-time-and-results.md) | Trace the domain producer and consumer together, then run contract/result/config tests as well as the domain test |
| Public exports, package boundaries, or a new subsystem | [System Architecture and Public Surfaces](architecture/system-map.md) | Verify every affected `__all__`, optional import boundary, docs example, and package metadata test |

A useful ownership rule is: **native analytical kernels own definitions; integrations normalize external products; owned result types preserve data and provenance; visualization/reporting renders rather than recomputes**. Compatibility modules are migration adapters over those native kernels, not independent implementations.

## Validate in layers

Run the narrowest test that proves the changed behavior while iterating, then qualify the final revision with the repository gates. The standard validation commands are:

```bash
uv run ruff check src/ tests/
uv run ruff format --check src/ tests/
uv run ty check
uv run pytest tests/ -q -n auto --timeout 120
uv run mkdocs build --strict
pre-commit run --all-files
```

Pytest defaults exclude tests marked `slow` and `native_browser`; the configured default also enables strict markers, parallel execution, and disables benchmarks. Run slow tests explicitly when changing Monte Carlo, large-scale, benchmark, streaming, or expensive ML behavior. Real-browser report checks are serial platform qualification and are required when qualifying reports; do not infer that the ordinary parallel suite covered them. See [Development, Testing, and Safe Change Strategy](testing/development-and-test-strategy.md) for focused commands, markers, contract checks, packaging, and optional-runtime qualification.

For a user-visible fix, reproduce the behavior through the affected public API and retain regression evidence. Numerical definitions and defaults are compatibility decisions, not implementation cleanup: establish the intended behavior before changing them. Documentation and executable examples are part of the public package surface, so update and validate them with the code.

## Minimal reading sequences

- **Small core fix:** this page → relevant workflow page → domain producer/result → focused test → full applicable gates.
- **Cross-domain or API change:** this page → [system map](architecture/system-map.md) → [contracts](concepts/contracts-time-and-results.md) → both producer and consumer → contract and public-surface tests.
- **Report or integration change:** this page → [backtest integration flow](integrations/backtest-to-tearsheet.md) → [reporting boundaries](integrations/reporting-and-visualization.md) → integration tests → renderer/browser qualification.
- **Compatibility change:** this page → [legacy compatibility](integrations/legacy-compatibility.md) → native kernel semantics → adapter tests and native regression tests.
