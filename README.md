# Adaptive GPU Thermal System

Adaptive GPU Thermal System is an open research concept for workload-aware GPU cooling using staged refrigerant modules.

The project investigates whether cooling capacity can be prepared during low-load periods, dispatched during GPU load peaks, and regenerated afterward while maintaining GPUs within an appropriate operating temperature band.

> **Status:** Concept only. No implementation, simulation result, efficiency claim, safety approval, or commercial-readiness claim is provided at this stage.

## 日本語概要

本プロジェクトは、複数の密閉冷媒ユニットを時間差で運転し、GPU負荷に応じて必要な冷却能力だけを投入する熱管理方式の構想です。

目的はGPUを常に最低温度へ冷却することではなく、計算性能、長期信頼性、冷却設備の消費電力、結露条件を考慮した適切な温度帯に維持することです。

低負荷時には冷媒ユニットを冷却・凝縮して準備し、高負荷時には必要な数だけ段階的または並列に投入します。使用後のユニットは、負荷が下がった時間に順次再生します。

## Core principles

- The refrigerant remains inside a sealed circuit.
- Pressure is an internal refrigerant-state and transport variable; it is not applied directly to the GPU.
- Heat is transferred through a cold plate, evaporator, or another sealed heat exchanger.
- Multiple independently isolatable modules provide staged cooling capacity and fault isolation.
- GPU telemetry and workload scheduling may be coordinated with thermal control.
- Seawater or lake water may be used on a separate heat-rejection loop.
- Water-side inspection, cleaning, and limited repair may be automated.
- Existing flow, pressure difference, or elevation head may be used for secondary energy recovery where technically justified.
- Rejected heat may be supplied to aquaculture, greenhouses, preheating, or other low-temperature uses through separated heat exchangers.

## Conceptual architecture

```text
GPU and compute equipment
        |
        | heat
        v
Cold plate / evaporator
        |
        v
Parallel sealed refrigerant modules
  - active cooling
  - recovery
  - cooling / condensation
  - ready and isolated
  - maintenance / fault isolation
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
  - compliant discharge

Water-side pipes and heat exchangers
        ^
        |
Automated inspection, cleaning, and repair systems
```

## Documentation

- [`docs/concept.md`](docs/concept.md) — detailed Japanese concept specification
- [`docs/system-concept.md`](docs/system-concept.md) — English engineering overview
- [`docs/README.md`](docs/README.md) — documentation index

## Current scope

The repository currently defines the project concept and its boundaries. The following are intentionally unresolved:

- refrigerant selection;
- operating pressure and temperature ranges;
- module geometry and valve arrangement;
- GPU-side heat-exchanger design;
- refrigerant quantity and module count;
- water intake and discharge design;
- generator placement and recoverable power;
- aquaculture or other secondary-heat targets;
- control software and simulation models.

## Safety and environmental notice

This project may involve pressurized refrigerants, electrical equipment, rotating machinery, natural-water intake and discharge, corrosion, condensation, and thermal hazards. Concept documents are not construction approval. Any physical implementation requires qualified engineering review, applicable legal and environmental approvals, pressure protection, refrigerant compliance, electrical protection, leak detection, and fail-safe shutdown design.

## Licensing

This repository uses multiple licenses:

- hardware designs: **CERN-OHL-W-2.0**;
- software and simulation code: **Apache-2.0**;
- documentation and general-purpose diagrams: **CC-BY-4.0**.

See [`LICENSE.md`](LICENSE.md) and [`LICENSES/`](LICENSES/).