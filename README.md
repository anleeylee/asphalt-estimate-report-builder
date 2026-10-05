# Asphalt Estimate Report Builder

**Generate reproducible, audit-ready asphalt estimate reports** — JSON, Markdown, XLSX, PDF or CSV — from already structured and validated project data.

[![Python 3.12](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![CLI](https://img.shields.io/badge/CLI-command--line-blue)](#usage)
[![Platform: Windows](https://img.shields.io/badge/Platform-Windows-0078D6)](https://www.microsoft.com/windows)

**English** | [简体中文](./README.zh-CN.md)

---

## What it solves

An estimate is only useful if it can be defended — but stitching project data, takeoff, quotes and tickets into a coherent, traceable report is manual work that rarely survives an audit. This tool **assembles project.json, takeoff, specifications, quotes, supplier prices, delivery tickets and weather notes into a single report** with the required sections: project summary, takeoff, pavement sections, material quantities, order quantities, truckloads, cost basis, supplier evidence, quote comparison, delivery reconciliation, weather notes, risk/review flags, source register and calculation register.

It never discovers new facts, fills missing values or picks a contractor; **every important value traces to a source or a named deterministic engine run**.

## Features

- **Audit-ready report sections** — summary, takeoff, pavement sections, quantities, order tons, truckloads, cost basis
- **Evidence registers** — source register and calculation register for every value
- **Multi-format output** — JSON / Markdown / XLSX / PDF / CSV
- **No invented facts** — never fills missing values, never picks a contractor
- **Engine traceability** — every engine number records `engine_version` and its inputs
- **Risk & review flags** — surfaced in the report, never hidden

## Install

```bash
pip install -r requirements.txt
```

## Usage

```bash
python -m scripts.s10_estimate_report_builder --project ./project --format md
```

Run with `--help` for all options. Every script in this family shares the same CLI convention: `--project <path> --input <path> --output <path> --format json|csv|md|xlsx|pdf --config <path> --verbose --dry-run`.

## Outputs

- `estimate_report.json|md|xlsx|pdf|csv` (selected by `--format`)
- every engine number records `engine_version` and its inputs

## How it works

- **Standard lifecycle** — `DISCOVER → INGEST → EXTRACT → NORMALIZE → VALIDATE → CALCULATE → OUTPUT → AUDIT`
- **Evidence state machine** — every value moves `EXTRACTED → NORMALIZED → VALIDATED → VERIFIED`; stopping earlier is valid
- **Deterministic math** — tons, volume, coverage, truckloads and material cost come from the AsphaltCosts engine, never re-implemented here

## Quality gates

- deterministic schema validation on every record;
- golden sample fixtures with exact expected values (`tests/`);
- review queue: low-confidence values, ambiguous scales and conflicts land in `review_queue.json` / summary — never a silent fix;
- audit log: every run writes `run_id`, timings, input hashes, engine version and outputs to `<project>/audit/`.

## Testing

```bash
python -m pytest -q
```

## Security & privacy

Local files stay local by default. API keys live in environment variables (`ASPHALTCOSTS_API_KEY`, `ADI_AI_API_KEY`) — never in source code. Derived files are written to `working/` or `output/`; source files are never modified.

## License

MIT — see [LICENSE](LICENSE). Part of the [Asphalt Desktop Intelligence](https://github.com/anleeylee/asphalt-desktop-intelligence) toolkit.

## The calculation engine

[**AsphaltCosts.com**](https://asphaltcosts.com/) is the deterministic calculation layer: area → compacted volume → net tons → order tons (allowance applied once) → truckloads → material cost, with sourced planning defaults (145 lb/ft³ FHWA density, editable allowance and truck capacity). This tool feeds measured and validated inputs into that engine (or its labeled local mirror, `asphaltcosts-web-engine/1.0-mirror`) and never re-implements the formulas.
