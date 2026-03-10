# GoaliePose

Computer vision for ice hockey goalie movement analysis.

GoaliePose is a computer vision project focused on detecting and tracking a **single goalie** in **fixed-camera video** to generate a clean skeletal/keypoint representation for coaching and technique review. The long-term goal is to turn raw footage into usable movement insights for goalie coaches and self-training players.

## Project Status

**Current phase:** Phase 0 — Product spec and benchmarks

This repository is currently in the product-definition and benchmark-building stage. The focus right now is to lock scope, define acceptance criteria, and create a small but meaningful benchmark set before model experimentation begins.

## Problem

Goalie coaching often relies on manual video review, which is slow, subjective, and difficult to scale. This project aims to automate the first technical layer of review by identifying a goalie’s body position frame-by-frame and producing stable tracked keypoints that can later support deeper movement analysis.

A correct result means:
- the goalie is found reliably in the frame
- body keypoints are placed accurately enough to form a meaningful skeleton
- motion can be reviewed consistently across a clip

A poor result means:
- keypoints drift or disappear
- the skeleton becomes unusable for review
- the footage loses coaching value

## Version 1 Scope

The initial version is intentionally narrow.

### Included in v1
- Fixed camera input only
- Single goalie only
- Batch processing only
- Keypoint/body tracking for goalie posture and movement review
- Benchmark-driven development

### Not included in v1
- Multi-camera support
- Live/real-time inference as a required feature
- Multiple players / full-rink understanding
- General hockey scene understanding
- Stick-state classification, unless the baseline proves stable enough

## Phase 0 Goals

Phase 0 exists to prevent wasted model work by defining exactly what success looks like.

### Deliverables
- One-page product spec
- Fixed ontology v1
- Acceptance criteria
- 20–30 benchmark clips selected manually
- 5–10 must-pass representative scenarios

### Exit criteria
Phase 0 is complete when:
- the v1 scope is frozen
- ontology labels are fixed
- acceptance criteria are written
- benchmark clips are selected and organized
- representative must-pass scenarios are documented

## Core Idea

Input:
- goalie practice footage from a fixed camera angle

Output:
- tracked body keypoints / skeletal representation of the goalie over time

Primary use case:
- post-session coaching review
- self-review by goalies
- movement and positioning analysis

## Users

This project is being designed for:
- ice hockey goalie coaches
- self-teaching goalies
- players looking to review technique through video

## Guiding Principles

- **Narrow scope first.** A small system that works is better than a broad system that fails.
- **Benchmark before scale.** Build around hard examples early.
- **Stability over novelty.** A reliable baseline matters more than flashy features.
- **Coaching utility first.** Output should be useful for review, not just technically impressive.

## Proposed Repository Structure

```text
goaliepose/
├── README.md
├── LICENSE
├── docs/
│   ├── product-spec.md
│   ├── ontology-v1.md
│   ├── acceptance-criteria.md
│   └── roadmap.md
├── benchmarks/
│   ├── clips/
│   ├── metadata/
│   └── scenarios/
├── data/
│   ├── raw/
│   ├── processed/
│   └── samples/
├── src/
│   ├── ingestion/
│   ├── detection/
│   ├── tracking/
│   ├── keypoints/
│   ├── evaluation/
│   └── visualization/
├── experiments/
├── notebooks/
└── tests/
```
## Phase Roadmap

### Phase 0 — Product spec and benchmarks

Define the problem, freeze v1 scope, and build the benchmark set.

### Phase 1 — Baseline pipeline

Build the first working pipeline for:
- clip ingestion
- goalie detection / isolation
- keypoint estimation
- tracking
- visual overlay output

### Later phases

Planned future work may include:
- stronger benchmark coverage
- robustness improvements
- better tracking stability
- richer analytics
- possible real-time or multi-camera extensions

## Benchmark Philosophy

The benchmark set should reflect real coaching conditions, not just ideal footage.

Examples of scenarios to include:
- clean centered goalie in stable lighting
- lateral movement across the crease
- butterfly transitions
- partial occlusion
- darker rink lighting
- varying goalie gear / jersey contrast
- camera distance changes
- fast recovery movements

The project should be judged against **must-pass scenarios**, not just average-case success.

## Acceptance Direction

The exact numbers will be finalized in Phase 0, but the system should eventually be evaluated on qualities like:
- keypoint stability
- tracking continuity
- robustness to lighting and motion
- coaching usefulness of output
- failure rate on benchmark scenarios

## Current Priorities

1. Write the one-page product spec
2. Finalize ontology v1
3. Define acceptance criteria
4. Curate the benchmark clip set
5. Identify the must-pass scenarios
6. Begin baseline pipeline design

## License

This project is licensed under the **Apache License 2.0**. See the `LICENSE` file for details.

## Notes

This repository is under active development. During the early phases, the priority is correctness of scope and evaluation setup rather than feature breadth.
