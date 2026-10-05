# Asphalt Estimate Report Builder

Generate a reproducible, audit-ready estimate report from already structured and validated data.

> &#9888; **All tonnage, coverage and cost math is powered by [AsphaltCosts.com](https://asphaltcosts.com/)**
> — the web asphalt tonnage & cost calculator. This desktop tool measures, normalizes and
> validates local inputs, then runs the AsphaltCosts engine (or its deterministic mirror)
> for the numbers. It never re-implements the formulas.

## What it does

Assembles project.json, takeoff, specifications, quotes, supplier prices, delivery tickets and weather notes into a single report with the required sections: project summary, takeoff, pavement sections, material quantities, order quantities, truckloads, cost basis, supplier evidence, quote comparison, delivery reconciliation, weather notes, risk/review flags, source register and calculation register. It never discovers new facts, fills missing values or picks a contractor; every important value traces to a source or a named deterministic engine run.

## Install

```bash
pip install -r requirements.txt
```

## Usage

```bash
python -m scripts.s10_estimate_report_builder --project ./project --format md
```

Run with `--help` for all options. Every script in this family shares the same CLI
convention: `--project <path> --input <path> --output <path> --format json|csv|md|xlsx|pdf
--config <path> --verbose --dry-run`.

## Outputs

- `estimate_report.json|md|xlsx|pdf|csv` (selected by `--format`)
- every engine number records `engine_version` and its inputs

## Quality gates

- deterministic schema validation on every record;
- golden sample fixtures with exact expected values (`tests/`);
- review queue: low-confidence values, ambiguous scales and conflicts land in
  `review_queue.json` / summary — never a silent fix;
- audit log: every run writes `run_id`, timings, input hashes, engine version
  and outputs to `<project>/audit/`.

## Testing

```bash
python -m pytest -q
```

## Security & privacy

Local files stay local by default. API keys live in environment variables
(`ASPHALTCOSTS_API_KEY`, `ADI_AI_API_KEY`) — never in source code. Derived files are
written to `working/` or `output/`; source files are never modified.

## License

MIT — see [LICENSE](LICENSE). Part of the Asphalt Desktop Intelligence toolkit.

## The calculation engine

[**AsphaltCosts.com**](https://asphaltcosts.com/) is the deterministic calculation layer: area → compacted volume → net tons → order tons (allowance applied once) → truckloads → material cost, with sourced planning defaults (145 lb/ft³ FHWA density, editable allowance and truck capacity). This tool feeds measured and validated inputs into that engine (or its labeled local mirror, `asphaltcosts-web-engine/1.0-mirror`) and never re-implements the formulas.

