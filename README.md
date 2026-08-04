# Adaptive GPU Thermal System

An open research project for adaptive GPU temperature control using staged refrigerant modules, natural-water heat rejection, automated maintenance, and waste-heat and flow-energy recovery.

> **Project status:** Conceptual and unvalidated. No claim is currently made that the proposed system is novel, efficient, safe, commercially viable, or ready for deployment.

## 日本語概要

本プロジェクトは、GPUを常に最低温度まで冷却するのではなく、計算性能、長期信頼性、冷却設備の消費電力、結露条件を考慮した適切な温度帯に維持する熱管理方式を研究するものです。

低負荷時に複数の密閉冷媒ユニットを冷却・凝縮して利用可能な状態へ準備し、GPU負荷の上昇時に必要なユニットだけを段階的または並列に投入します。さらに、海水・湖水による放熱、自然水設備の自動保守、既存の水流・圧力差からのエネルギー回収、排熱の養殖・温室などへの二次利用も研究対象に含みます。

### 構想の基準文書

会話で確定した設計意図、重要な誤解の修正、未確定事項まで含む詳細な日本語記録は、[`docs/concept-master-ja.md`](docs/concept-master-ja.md) に保存しています。

将来チャット履歴や外部コンテキストが失われた場合は、この文書をプロジェクト再開時の基準としてください。文書一覧と優先順位は [`docs/README.md`](docs/README.md) にあります。

## Purpose

This project explores a variable-capacity thermal-management system that maintains GPUs within an appropriate operating temperature band instead of continuously driving them toward the lowest possible temperature.

The system is intended to coordinate GPU workload information with multiple independently controlled refrigerant modules. Cooling capacity is prepared during idle or low-load periods and deployed incrementally when GPU load and temperature rise.

## Core concept

```text
Low GPU load
    -> cool and condense refrigerant
    -> prepare multiple modules for later use

Normal GPU load
    -> use only the required modules
    -> maintain the target temperature band

Peak GPU load
    -> add modules incrementally or in parallel
    -> suppress rapid temperature rise

Load reduction
    -> reduce cooling output
    -> regenerate used modules
```

The refrigerant remains inside a sealed fluid circuit. It is not intended to blow air or refrigerant directly onto the GPU. Heat is transferred through a cold plate, evaporator, or other sealed heat exchanger.

## System scope

The research scope includes:

- staged and parallel refrigerant modules;
- target-band GPU temperature control;
- GPU telemetry and job-scheduler integration;
- natural-water heat rejection using seawater or lake water;
- automated inspection, cleaning, and maintenance of water-side infrastructure;
- recovery of usable flow energy where a genuine pressure or elevation difference exists;
- secondary use of rejected heat for aquaculture, greenhouses, preheating, or other low-temperature applications.

## Control objective

The goal is not minimum temperature. The target operating band must balance:

- GPU performance and avoidance of thermal throttling;
- long-term component reliability;
- total cooling-system power;
- condensation and dew-point constraints;
- available refrigerant-module capacity;
- expected future compute load.

The principal optimization metric should be useful computation per unit of total system energy, not cooling-device efficiency in isolation.

## Proposed architecture

```text
GPU and compute equipment
        |
        | heat
        v
Cold plate / evaporator
        |
        v
Parallel refrigerant modules
  - cooling in progress
  - regeneration in progress
  - ready and isolated
  - transfer in progress
        |
        | heat
        v
Refrigerant-to-water heat exchanger
        |
        v
Seawater or lake-water loop
  - refrigerant regeneration
  - optional flow-energy recovery
  - optional secondary heat use
  - environmentally compliant discharge

Water-side pipes and heat exchangers
        ^
        |
Inspection, cleaning, and repair robots
```

## Documentation

- [`docs/concept-master-ja.md`](docs/concept-master-ja.md): canonical Japanese concept record and context-preservation document;
- [`docs/system-concept.md`](docs/system-concept.md): initial English engineering concept specification;
- [`docs/README.md`](docs/README.md): documentation index and interpretation order.

Planned documentation areas include thermodynamic assumptions, module state transitions, GPU workload models, control algorithms, heat-exchanger design, water-side constraints, robotic maintenance, energy recovery, secondary heat use, safety, and experimental results.

## Repository structure

```text
hardware/       Hardware, piping, mechanical, and CAD designs
software/       Control, monitoring, and automation software
simulation/     Thermal, fluid, workload, and control simulations
robotics/       Inspection, cleaning, and repair software
docs/           Specifications, assumptions, and research notes
diagrams/       General-purpose diagrams and figures
LICENSES/       Full license texts
```

Directories will be added as work begins.

## Licensing

This repository uses multiple licenses according to material type:

- hardware designs: **CERN-OHL-W-2.0**;
- software and simulation code: **Apache-2.0**;
- documentation and general-purpose diagrams: **CC-BY-4.0**.

See [`LICENSE.md`](LICENSE.md) and the full texts under [`LICENSES/`](LICENSES/).

## Safety and environmental notice

This project may involve pressurized refrigerants, pressure vessels, electrical equipment, seawater or lake-water intake systems, rotating machinery, and thermal hazards. Concept documents and simulations are not construction approval. Any physical implementation requires qualified engineering review, applicable legal and environmental approvals, pressure-system protection, refrigerant compliance, electrical protection, leak detection, and fail-safe shutdown design.

## Current stage

The repository currently preserves the concept. Specific refrigerants, pressures, temperatures, dimensions, flow rates, valve arrangements, heat exchangers, generator output, and aquaculture targets are intentionally left unresolved until a later design phase.
