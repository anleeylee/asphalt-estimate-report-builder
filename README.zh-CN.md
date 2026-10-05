# Asphalt Estimate Report Builder（沥青估算报告生成工具）

**从已结构化、已校验的项目数据生成可复现、可审计的沥青估算报告** —— 支持 JSON、Markdown、XLSX、PDF 或 CSV 输出。

[![Python 3.12](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![CLI](https://img.shields.io/badge/CLI-command--line-blue)](#使用)
[![Platform: Windows](https://img.shields.io/badge/Platform-Windows-0078D6)](https://www.microsoft.com/windows)

**[English](./README.md)** | 简体中文

---

## 解决什么问题

一份估算只有经得起辩护才有用——但把项目数据、算量、报价和送货单拼成一份条理清晰、可追溯的报告靠手工很难经得起审计。本工具**把 project.json、算量、规范、报价、供应商价格、送货单和天气记录组装为一份报告**，包含要求的所有章节：项目摘要、算量、路面结构层、材料数量、订购数量、车次、成本依据、供应商证据、报价对比、送货对账、天气记录、风险/复核标记、来源清单与计算清单。

它绝不发现新事实、填补缺失值或挑选承包商；**每条重要数值都追溯到来源或一次具名的确定性引擎运行**。

## 核心功能

- **可审计报告章节** ——摘要、算量、路面结构层、数量、订购吨数、车次、成本依据
- **证据清单** ——每条数值都有来源清单与计算清单
- **多格式输出** ——JSON / Markdown / XLSX / PDF / CSV
- **不编造事实** ——绝不填补缺失值、绝不挑选承包商
- **引擎可追溯** ——每个引擎数字记录 `engine_version` 与其输入
- **风险与复核标记** ——在报告中呈现，绝不隐藏

## 安装

```bash
pip install -r requirements.txt
```

## 使用

```bash
python -m scripts.s10_estimate_report_builder --project ./project --format md
```

加 `--help` 查看全部选项。本工具家族统一 CLI 约定：`--project <路径> --input <路径> --output <路径> --format json|csv|md|xlsx|pdf --config <路径> --verbose --dry-run`。

## 输出

- `estimate_report.json|md|xlsx|pdf|csv`（由 `--format` 选择）
- 每个引擎数字记录 `engine_version` 与其输入

## 工作原理

- **标准生命周期** ——`发现 → 摄取 → 提取 → 归一化 → 校验 → 计算 → 输出 → 审计`
- **证据状态机** ——每条数值按 `提取 → 归一化 → 已校验 → 已验证` 推进；允许停在更早状态
- **确定性计算** ——吨数、体积、覆盖面积、车次与材料成本来自 AsphaltCosts 引擎，绝不在本工具中重写公式

## 质量保证

- 每条记录做确定性 schema 校验；
- 黄金样例 fixtures 与精确期望值（`tests/`）；
- 复核队列：低置信度、比例不明与冲突进入 `review_queue.json` / 摘要——绝不静默修正；
- 审计日志：每次运行将 `run_id`、耗时、输入哈希、引擎版本与输出写入 `<project>/audit/`。

## 测试

```bash
python -m pytest -q
```

## 安全与隐私

本地文件默认留在本地。API 密钥存放在环境变量（`ASPHALTCOSTS_API_KEY`、`ADI_AI_API_KEY`）——绝不写入源码。派生文件写入 `working/` 或 `output/`；来源文件永不修改。

## 许可证

MIT —— 见 [LICENSE](LICENSE)。属 [Asphalt Desktop Intelligence](https://github.com/anleeylee/asphalt-desktop-intelligence) 工具集的一部分。

## 计算引擎

[**AsphaltCosts.com**](https://asphaltcosts.com/) 是确定性计算层：面积 → 压实体积 → 净吨数 → 订购吨数（损耗只计一次）→ 车次 → 材料成本，内置有出处的规划默认值（FHWA 密度 145 lb/ft³，损耗率与车容量可编辑）。本工具把测量并校验后的输入喂给该引擎（或其带标签的本地镜像 `asphaltcosts-web-engine/1.0-mirror`），绝不重写公式。
