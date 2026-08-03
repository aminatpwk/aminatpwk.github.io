---
layout: page
title: Agent-Based Simulation Performance
description: Rigorous bottleneck characterization of large-scale ABM workloads using DAMOV and hardware profiling
importance: 2
category: research
img: assets/img/projects/agent-based-simulations/soma-clustering.png
related_publications: true
---

**Agent-based modeling (ABM)** is a bottom-up method for studying complex systems. We model thousands, millions, even billions of individual agents—each one simple, with its own properties and behavior, each interacting with its neighbors. From these local interactions, global complexity emerges.

Presented as a poster at **ACACES 2026** (HiPEAC Summer School, Fiuggi, Italy) with a merit-based grant {% cite sokoli2026acaces %}.

## Why agent-based simulations matter

The ambition is growing: we do not want to simulate just thousands or millions of agents, but **billions**—the world we live in, at the speed it actually operates, at large scale and in real time. Nobody or any tool can do that today.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/agent-based-simulations/pyramidal-cell.png" title="A single biological agent (pyramidal neuron)" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/agent-based-simulations/tumor-growth.png" title="Cell-by-cell tumor growth simulation" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Each agent is simple; complexity emerges from their collective interactions—cell by cell, person by person, trader by trader.
</div>

 The EU recently committed to phasing out animal testing across 15 legislative domains, yet its own funding falls short of that ambition—pointing directly to agent-based simulations as an alternative.

## Versatile applications

Agent-based simulations are general. The approach applies everywhere:

<div class="row justify-content-sm-center">
    <div class="col-sm-10 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/agent-based-simulations/abm-applications.png" title="Agent-based modeling application domains" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    From epidemiology and medicine to finance, transport, ecology, and social sciences—the same bottom-up paradigm scales across domains.
</div>

## Research goal

To reach real-time, billion-agent simulation, we must first understand **where performance breaks down**. We started simulating real-world use cases to benchmark them, determine whether bottlenecks lie in software (data structures, architecture) or demand hardware acceleration, and guide future co-design.

To our knowledge, this is the **first work that rigorously characterizes where agent-based simulations bottleneck at scale**—a central contribution of this project.

## Methodology

We profiled [**BioDynaMo**](https://biodynamo.org/), the state-of-the-art agent-based simulation framework developed at CERN, using an epidemiology use case:

- **Scale:** 10M to 1B agents on Google Cloud C4 VMs (16 cores each)
- **Characterization:** [DAMOV](https://damov-v2.gforge.uni.lu/) methodology for workload analysis
- **Profiling:** `perf` at 1 Hz to capture hardware metrics in real time during simulation

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/agent-based-simulations/analytic-continuum.png" title="Large-scale agent field visualization" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/agent-based-simulations/soma-clustering.png" title="Epidemiology benchmark: agent clustering with interaction fields" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Left: large-scale agent distribution at billion-agent counts. Right: the epidemiology benchmark simulated with BioDynaMo, showing agent clustering and neighbor interactions.
</div>

We derived four metrics for the analysis:

| Metric | What it measures |
| --- | --- |
| **IPC** | Instructions per cycle—processor utilization |
| **LLC MPKI** | Last-level cache misses per kilo-instruction |
| **Memory-bound fraction** | Share of cycles stalled on memory |
| **LFMR** | Last-to-first miss ratio—cache line reuse after L1 miss |

## Key findings

1. **IPC sits around 0.4** on a 16-core machine. The processor is mostly idle.
2. **Memory-bound fraction exceeds 60%** at every agent count tested. This is a memory-bound workload.
3. **LFMR stays above 0.74** across the board. Most L1 misses skip past L2 entirely—the cache line arrives, the processor touches it once, and it is never reused. L2 is largely ineffective for this workload.
4. **Non-monotonic scaling behavior:** performance is worst at 10M agents, best at 50M, and degrades again beyond that. This remains an open question.
5. **A hard memory ceiling:** 200M agents already consumes 58 GB. Memory capacity sets the limit on how far this scales on our machines.

**Memory is standing in the way of simulating the world at the speed it runs.**

## What's next

- **Characterizing locality** in agent access patterns
- **Validating** hardware profiling results against a simulation-based approach
- **Extending to GPUs and PIM**, where we expect the memory bottleneck to worsen—not improve—once parallelism is fully embraced

## Related frameworks & code

- [BioDynaMo](https://biodynamo.org/) — state-of-the-art ABM framework (CERN)
- [ACACES 2026 profiling study](https://github.com/aminatpwk/acaces26-agent-based-modeling) — experiment scripts and analysis
- [CARTopiaX](https://github.com/aminatpwk/CARTopiaX) — scalable simulation framework
- NeuroDev — neural development simulation

This project bridges agent-based modeling research with computer architecture and performance engineering, connecting to affiliated work on [HBM-PIM memory systems](/projects/cispa-hbm-pim-research/).
