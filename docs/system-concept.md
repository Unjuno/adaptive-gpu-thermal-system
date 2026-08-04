# Adaptive GPU Thermal System — Initial Concept Specification

## 1. Status and scope

This document records the current system concept. It is an engineering hypothesis, not a validated design.

The project proposes a variable-capacity thermal-management system that coordinates GPU workloads with multiple independently controlled refrigerant modules. The intended outcome is to keep GPUs within an appropriate operating temperature band while reducing unnecessary cooling effort and preparing additional cooling capacity before predicted load peaks.

The wider system may also incorporate natural-water heat rejection, automated maintenance, recovery of genuinely available flow energy, and secondary use of rejected heat.

The following claims require simulation and experimental validation:

- total energy use can be lower than conventional cooling;
- staged refrigerant modules can efficiently absorb transient GPU heat loads;
- module regeneration can be completed during low-load periods;
- natural-water heat rejection is environmentally and economically viable at the intended site;
- maintenance automation can reduce lifecycle cost;
- flow-energy recovery and secondary heat use can be added without degrading the primary cooling function.

## 2. Primary objective

The objective is not to maintain the lowest achievable GPU temperature.

The control system should maintain an operating band that balances:

- stable GPU performance and avoidance of thermal throttling;
- long-term reliability of the GPU, memory, voltage-regulation components, and board;
- total energy consumed by the GPU and cooling infrastructure;
- condensation and dew-point constraints;
- available refrigerant-module capacity;
- the timing and magnitude of expected compute workloads.

The preferred optimization metric is useful computation per unit of total system energy, subject to thermal, reliability, environmental, and safety constraints.

## 3. Distinction from direct air delivery

The system does not use refrigerant pressure to blow air or refrigerant onto the GPU.

The refrigerant remains inside a sealed circuit. Heat is transferred from the GPU through a cold plate, evaporator, or another sealed heat exchanger.

```text
GPU die and package
        |
        | heat conduction
        v
Cold plate / evaporator
        |
        | heat transfer
        v
Sealed refrigerant circuit
```

Pressure is an internal thermodynamic and transport variable. The externally controlled result is GPU temperature and heat removal.

## 4. Refrigerant-module concept

The system contains multiple independently isolatable refrigerant modules.

A module may pass through the following states:

1. refrigerant heating;
2. refrigerant transfer;
3. cooling and condensation;
4. isolation in a ready state;
5. connection to the GPU-side heat exchanger;
6. heat absorption during GPU operation;
7. recovery of used refrigerant;
8. regeneration;
9. fault isolation or maintenance.

Modules operate with phase offsets. At a given time, one module may be cooling the GPU while another is regenerating and another is ready for immediate use.

```text
Module A: serving GPU heat load
Module B: cooling or condensing
Module C: ready and isolated
Module D: transferring refrigerant
Module E: maintenance or fault isolation
```

This arrangement is intended to provide variable cooling capacity, fault isolation, maintainability, and the ability to regenerate some modules while others remain available.

## 5. Stored cooling state

The module does not store only a pressure value. It stores a thermodynamic state that can later be used for cooling.

Relevant state variables include:

- refrigerant mass;
- temperature;
- pressure;
- liquid and vapor fractions;
- condensation state;
- estimated remaining heat-absorption capacity;
- expected holding loss;
- time required for regeneration;
- availability for connection to the GPU-side heat exchanger.

The system is therefore treated as a form of thermal storage or staged cooling-capacity storage.

A complete design must define the refrigerant, pressure range, temperature range, module geometry, valves, transfer process, and safe operating envelope.

## 6. Operating modes

### 6.1 Idle and low-load operation

During GPU idle time or low utilization, available heat-rejection capacity is used to regenerate refrigerant modules.

```text
Low GPU load
    -> recover used refrigerant
    -> reject heat through the water-side heat exchanger
    -> cool and, where required, condense the refrigerant
    -> isolate the module
    -> mark it ready for later use
```

This mode shifts part of the cooling work away from periods of maximum GPU demand.

### 6.2 Normal computation

Only the modules required to maintain the target temperature band are connected. Other modules remain ready or continue regeneration.

### 6.3 Predicted load increase

Before a scheduled or predicted workload begins, the controller estimates the required cooling capacity and prepares additional modules for immediate connection.

### 6.4 Peak load

Modules are added incrementally or in parallel according to measured GPU temperature, heat-load estimates, and available capacity.

```text
GPU load rises
    -> predict temperature trajectory
    -> connect one additional module
    -> measure response
    -> add further modules if required
    -> reduce module count after temperature stabilizes
```

### 6.5 Recovery after load reduction

After the load falls, cooling output is reduced to avoid unnecessary energy use and overcooling. Used modules are regenerated in sequence.

### 6.6 Capacity shortage or fault

If available cooling capacity is insufficient, the compute-control layer may apply temporary GPU power limits, delay lower-priority jobs, stagger GPU startup, or isolate failed thermal modules.

## 7. Target temperature band

The appropriate GPU temperature is an operating band rather than one universal fixed value.

Its lower boundary is constrained by ambient dew point and a condensation safety margin. Its upper boundary is constrained by thermal-throttling behavior, reliability targets, and a control safety margin.

```text
Lower boundary = dew-point temperature + condensation margin
Upper boundary = performance-limiting temperature - control margin
```

The target band must account for more than the reported GPU core temperature. Monitored temperatures may include:

- GPU core;
- GPU hotspot;
- high-bandwidth or graphics memory;
- voltage-regulation modules;
- printed circuit board;
- cold-plate surface;
- refrigerant inlet and outlet;
- ambient air and dew point.

A narrow fixed setpoint may cause rapid valve cycling and thermal oscillation. Initial control should therefore use a defined dead band or hysteresis. More advanced control may use model-predictive control once a validated thermal model exists.

## 8. Control inputs

### GPU and compute telemetry

- utilization;
- power consumption;
- clock frequency;
- voltage;
- core, hotspot, memory, and regulator temperatures;
- thermal-throttling indicators;
- rate of temperature change;
- active and scheduled jobs;
- expected GPU count, duration, and power profile.

### Refrigerant system

- module temperature and pressure;
- refrigerant inventory;
- estimated liquid and vapor fractions;
- estimated remaining cooling capacity;
- valve and pump states;
- module regeneration time;
- heat-exchanger temperatures and pressure drop;
- leak and fault indications.

### Natural-water system

- intake and discharge temperature;
- flow rate and pressure;
- water quality;
- heat-exchanger approach temperature;
- fouling indicators;
- pressure loss;
- intake and discharge environmental limits.

## 9. GPU scheduler integration

The thermal controller should exchange information with the compute scheduler.

```text
Read planned workload
    -> estimate future GPU power and heat
    -> verify ready refrigerant capacity
    -> prepare required modules
    -> start or stage the workload
    -> correct cooling from live measurements
    -> regenerate modules after completion
```

When cooling capacity is limited, the scheduler may:

- delay selected jobs;
- divide a large workload into staged GPU groups;
- ramp GPU power gradually;
- apply temporary power caps;
- reserve cooling capacity for priority workloads.

This converts the cooling system from a purely reactive subsystem into a workload-aware thermal resource.

## 10. Natural-water heat rejection

Seawater or lake water is not circulated through the GPU hardware. It is separated from the refrigerant by one or more heat exchangers.

```text
GPU heat
    -> sealed refrigerant loop
    -> refrigerant-to-water heat exchanger
    -> seawater or lake-water loop
```

This separation prevents salt, sediment, biological material, and water-quality variation from directly entering the refrigerant or GPU cooling circuits.

A deployable design must address:

- water-intake and discharge permissions;
- discharge-temperature limits;
- effects on aquatic organisms;
- intake screening and entrainment;
- corrosion and material compatibility;
- seasonal water-temperature variation;
- biofouling and sediment;
- pumping energy and hydraulic losses;
- leak detection and fluid isolation.

## 11. Automated inspection and maintenance

Natural-water piping and heat exchangers are expected to experience biofouling, sediment accumulation, corrosion, blockage, and gradual performance loss.

Robotic or automated maintenance functions may include:

- internal visual inspection;
- leak and crack detection;
- wall-thickness measurement;
- fouling and deposit removal;
- brush or jet cleaning;
- thermal-performance testing;
- location-tagged defect records;
- limited local sealing or coating;
- notification when a module requires human replacement.

Components that cannot be reliably repaired in place should be designed as replaceable modules.

## 12. Flow-energy recovery

Flow-energy recovery is secondary to cooling.

Where the site already provides a usable water flow, pressure difference, or elevation difference, a turbine or generator may recover part of that energy.

The recovery device must not materially reduce the flow required for heat rejection or increase pumping energy beyond the recovered output.

Artificially creating pressure with a pump and then recovering it through a turbine normally adds net losses. Any proposed generator must therefore be evaluated using the original hydraulic energy available at the site, additional pressure drop, efficiency, maintenance requirements, and effects on cooling reliability.

Recovered electricity may support sensors, valves, inspection robots, communications, auxiliary pumping, batteries, or aquaculture equipment.

## 13. Secondary heat use

Heat rejected from GPUs may be transferred through an intermediate loop to suitable low-temperature applications.

Potential applications include:

- aquaculture temperature control;
- juvenile fish or shellfish cultivation;
- algae cultivation;
- greenhouses;
- water preheating;
- agricultural or material drying;
- other site-specific low-temperature heat demands.

The compute, refrigerant, natural-water, and aquaculture circuits must remain physically separated through heat exchangers. Aquaculture feasibility depends on water quality, dissolved oxygen, pathogens, seasonal conditions, and species-specific temperature limits, not temperature alone.

## 14. Design variables

### GPU system

- number and model of GPUs;
- average and maximum power per GPU;
- concurrent utilization;
- peak duration and interval;
- workload predictability;
- target temperature band;
- permitted power-management actions.

### Refrigerant system

- refrigerant selection;
- total charge;
- module count and capacity;
- pressure and temperature ranges;
- module regeneration time;
- heat-exchanger area;
- refrigerant flow rate;
- piping resistance;
- holding loss;
- valve response and cycle life.

### Water side

- intake temperature and seasonal range;
- available flow and head;
- pipe length and diameter;
- pumping power;
- water chemistry;
- corrosion and fouling rates;
- discharge constraints;
- maintenance access.

## 15. Evaluation metrics

### Compute performance

- completed computation per unit time;
- clock stability;
- thermal-throttling duration;
- workload completion time;
- GPU availability.

### Thermal control

- average and maximum GPU temperature;
- hotspot temperature;
- deviation from the target band;
- temperature variation;
- response time to load changes.

### Energy

- GPU energy;
- cooling-system energy;
- natural-water pumping energy;
- total facility energy attributable to the workload;
- useful computation per unit of total energy;
- recovered flow energy;
- useful secondary heat delivered.

### Refrigerant modules

- heat absorbed per module cycle;
- regeneration time;
- number of ready modules;
- leakage;
- pressure loss;
- heat-exchanger degradation.

### Reliability and maintenance

- GPU and board failure rates;
- valve, pipe, pump, and heat-exchanger failure rates;
- downtime;
- proportion of faults handled automatically;
- lifecycle maintenance cost.

## 16. Safety requirements

Any physical implementation requires professional review and compliance with applicable law and standards. At minimum, the design must address:

- refrigerant toxicity, flammability, and environmental impact;
- pressure-vessel and pressure-relief protection;
- electrical isolation and grounding;
- leak and condensation detection;
- fail-safe valve positions;
- emergency GPU power reduction and shutdown;
- rotating machinery protection;
- intake and discharge environmental protection;
- safe robotic maintenance and retrieval;
- inspection and maintenance intervals.

## 17. Immediate engineering tasks

1. Define a single refrigerant module with ports, valves, sensors, and state transitions.
2. Select candidate refrigerants and define preliminary pressure-temperature envelopes.
3. Build a GPU thermal-load model from measured workload traces.
4. Establish mass, energy, and time balances for one module and the complete bank.
5. Define target-temperature-band logic and condensation constraints.
6. Simulate staged module dispatch and regeneration.
7. Compare total energy and performance with conventional direct-liquid cooling and established thermal-storage methods.
8. Model water-side heat rejection, seasonal conditions, pumping energy, and fouling.
9. Evaluate flow-energy recovery and secondary heat use only after the primary cooling model is stable.
10. Record assumptions, failures, and negative results alongside successful results.

## 18. One-sentence definition

The Adaptive GPU Thermal System is a research concept that prepares cooling capacity in multiple sealed refrigerant modules and dispatches that capacity according to GPU workload and temperature, while coordinating natural-water heat rejection, automated infrastructure maintenance, optional flow-energy recovery, and secondary heat use within one controlled thermal-management architecture.
