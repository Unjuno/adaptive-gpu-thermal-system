# Adaptive GPU Thermal System

An open research project for adaptive GPU temperature control using staged refrigerant modules, natural-water heat rejection, automated maintenance, and waste-heat and flow-energy recovery.

> **Project status:** Conceptual and unvalidated. No claim is currently made that the proposed system is novel, efficient, safe, commercially viable, or ready for deployment.

## 日本語概要

本プロジェクトは、GPUを常に最低温度まで冷却するのではなく、計算性能、長期信頼性、冷却設備の消費電力、結露条件を考慮した適切な温度帯に維持する熱管理方式を研究するものです。

低負荷時に複数の密閉冷媒ユニットを冷却・凝縮して利用可能な状態へ準備し、GPU負荷の上昇時に必要なユニットだけを段階的または並列に投入します。さらに、海水・湖水による放熱、自然水設備の自動保守、既存の水流・圧力差からのエネルギー回収、排熱の養殖・温室などへの二次利用も研究対象に含みます。

詳細は [`docs/system-concept.md`](docs/system-concept.md) を参照してください。

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

The detailed working specification is maintained in [`docs/system-concept.md`](docs/system-concept.md).

Planned documentation areas include:

- thermodynamic assumptions and state diagrams;
- refrigerant-module state transitions;
- GPU workload and thermal models;
- control algorithms;
- heat-exchanger design;
- natural-water intake and discharge constraints;
- robotic maintenance concepts;
- power-recovery and secondary-heat-use evaluations;
- safety, environmental, and regulatory requirements;
- experimental results and failure analyses.

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

## Current priorities

1. Define one refrigerant module and its state transitions.
2. Define the GPU thermal-load model and target operating band.
3. Establish mass, energy, and time-balance equations.
4. Compare the proposed architecture with conventional liquid cooling and thermal storage.
5. Build a simulation before considering physical hardware.
