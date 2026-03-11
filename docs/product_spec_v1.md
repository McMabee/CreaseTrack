# CreaseTrack v1 Product Specification Summary

> Purpose: Define exactly what CreaseTrack v1 is and is not so implementation stays focused, benchmark-driven, and resistant to scope creep.

## Document Control

| Field | Value |
| --- | --- |
| Product | CreaseTrack |
| Version | v1 |
| Status | Draft |
| Owner | Ty Mabee |
| Last Updated | 2026-03-10 |
| Phase | Phase 0 |
| Deliverable | `docs/product_spec_v1.md` |

## 1. Summary

CreaseTrack v1 is a batch video analysis tool for ice hockey goalie training footage. It takes a pre-recorded video clip from a fixed-position camera, detects and tracks one goalie, and produces an annotated video with body landmarks and skeleton overlay, along with structured frame-by-frame pose data.

## 2. Objective

V1 **is**:
- fixed-camera goalie pose tracking
- single-goalie analysis
- post-session batch processing
- focused on reliable landmark tracking from controlled footage

V1 **is not**:
- live tracking
- multi-player or multi-goalie tracking
- moving-camera support
- puck tracking
- automatic coaching advice
- stick-state classification

A successful v1 identifies the primary body landmarks of a single goalie and overlays a stable skeleton representation onto the clip for review. It does not attempt to identify stick position, blade angle, puck position, or advanced coaching analytics.

## 3. Problem Statement

Goalie coaches and self-coaching goalies often rely on manual video review to evaluate stance and body positioning. This process is slow, subjective, and difficult to repeat consistently across clips. Important posture information is not explicitly highlighted, so users must infer key details on their own. CreaseTrack v1 addresses this by automatically identifying and visualizing goalie body landmarks in pre-recorded footage, making stance review faster, more consistent, and easier to interpret.


## 4. Target User

- Primary user: Goalie coaches and self-coaching goalies.
- User context: Used after practice or after recording a rep to review pre-recorded footage.
- Assumptions: Pre-recorded footage, used from device with web-browser. Video recorded in well-lit arena.

## 5. User Value

- Core value: CreaseTrack v1 makes goalie stance review faster and easier to interpret.
- Practical benefit: The relative position of the goalie’s head, shoulders, elbows, hands, hips, knees, ankles, and feet becomes easier to inspect frame by frame.
- Success signal: The system produces a stable and interpretable skeleton overlay and structured pose output that helps the user review stance more effectively than raw footage alone.

## 6. v1 System Definition

### Inputs

| Item | Description | Format / Source | Required | Notes |
| --- | --- | --- | --- | --- |
| Source video clip | Pre-recorded video of one primary goalie for pose tracking and review | Uploaded MP4 (```video/mp4```, H.264/AAC). MOV may be accepted only as an ingest format and transcoded before processing | Yes | Fixed camera only. One primary goalie only. Batch/video processing only. The goalie must remain visible for most of the clip. Moving cameras, zooming, live streams, and multi-goalie scenes are unsupported in v1. |
| Runtime processing profile | Internal inference settings used to run the pose pipeline | Internal config file or CLI/runtime parameters | Yes | Must include ```running_mode=VIDEO```, ```num_poses=1```, ```min_pose_detection_confidence```, ```min_pose_presence_confidence```, ```min_tracking_confidence```, model identifier, and export schema version. Not user-facing in v1.
| Run metadata | Identifiers and version fields used to reproduce and compare processing runs | Internal manifest generated at run start | Yes | Must include `clip_id`, `run_id`, `model_id`, `config_version`, `schema_version`, and processing timestamp. |

**Note**: Benchmark and demo footage must be creator-owned or used with explicit permission. Raw clips should be retained only as long as needed for processing and evaluation.

### Outputs

| Item | Description | Format | Consumer | Notes |
| --- | --- | --- | --- | --- |
| Annotated overlay video | A processed version of the input clip with the tracked goalie’s body landmarks and skeleton overlaid frame by frame | MP4 | Coach, player, reviewer | Primary human-review artifact. Used to visually inspect tracking quality, body positioning, and landmark stability across the clip. |
| Per-frame pose landmark data | Structured pose output for one tracked goalie using the MediaPipe 33-landmark body topology | JSON | Internal pipeline, benchmark workflow, future analytics | Primary machine-readable output. Must contain one tracked subject (```goalie_1```) and one complete landmark record per processed frame using the 33-landmark body schema. COCO-17 export, if needed later, is derived and not the canonical v1 output.|
| Run metadata report | A structured summary of the processing run and generated artifacts | JSON | Developer, internal, pipeline | Used to record reproducibility and run context, including clip ID, run ID, model version, processing settings, frame count, export contents, and warnings or failure conditions if present. |

### Expected Output Format

Each successfully processed input clip must produce one clearly associated output bundle. That output bundle must contain, at minimum:
- one annotated overlay video
- one machine-readable keypoint data file
- one run metadata file

Unsupported inputs may be rejected before processing or may produce degraded outputs that are excluded from benchmark acceptance. Unsupported cases include moving cameras, more than one primary goalie, severe long-duration occlusion, and clips where the goalie leaves frame for extended periods.

#### Required output files
Example naming convention for input clip ```clip_001```:
- clip_001_tracked_overlay.mp4
- clip_001_pose_data.json
- clip_001_run_metadata.json

#### Output interpretation
```*_tracked_overlay.mp4```
A human-review artifact showing the original footage with detected goalie body landmarks and skeleton overlay rendered on each processed frame. This file is used to visually assess whether tracking is stable, interpretable, and useful for post-session review.

```*_pose_data.json```
A machine-readable output containing frame-by-frame pose results for the tracked goalie. At minimum, this file must include:
- clip ID or run ID
- frame index
- frame timestamp or frame number
- tracked goalie instance
- named body landmarks
- 2D landmark coordinates
- `x_norm`, `y_norm`, and `z_rel` are required for every landmark
- `visibility` is required when supported by the selected model; otherwise it must be `null`
- `presence` is required when supported by the selected model; otherwise it must be `null`

The file is intended for benchmarking, downstream analysis, and future feature expansion.

```*_run_metadata.json```
A supporting artifact that records how the output package was produced. At minimum, this file should include:
- clip ID
- run ID
- model or pipeline version
- processing settings used
- total processed frame count
- output file list
- warnings, errors, or failure notes if applicable

#### Packaging rule
Each input clip must map to one unambiguous set of outputs. Output naming and structure must preserve a clear one-to-one relationship between the source clip and its generated artifacts so that results can be reviewed, compared, and benchmarked without confusion.

## 7. Scope

### In Scope for v1
The program should:
- accept a fixed-camera video input from the user
- preprocess the video for pose inference
- identify and annotate body landmarks for one goalie
- overlay a skeleton representation onto processed frames
- produce an annotated output video
- produce structured frame-by-frame pose data

### Non-goals / Out of Scope
The program will not:
- process videos with moving cameras
- process videos with multiple goalies/players
- identify pucks
- find blade angle, or any advanced analytics
- return live feed from camera


### Frozen v1 Scope Rules

- Fixed camera only
- Single goalie only
- Batch processing only
- No stick-state classification

Scope rule: Any request that conflicts with the frozen v1 rules is out of scope unless this document is formally revised.

Note: The exporting schema may reserve an optional ```stick_landmarks``` section for future versions, but stick tracking is not a required v1 output.

## 8. Constraints

- Technical constraints:
  - Development is being performed by one university student.
  - Local development hardware is limited to AMD-based consumer hardware with 32 GB RAM.
  - Budget is limited, so v1 must avoid expensive cloud training/inference dependencies.
  - v1 should rely on accessible tooling, reproducible local workflows, and lightweight storage/export formats.

- Data constraints:
  - Benchmark and demo footage must be creator-owned or used with explicit permission.
  - v1 benchmark clips must use one primary goalie per clip.
  - v1 benchmark clips must use a fixed camera viewpoint.
  - The goalie must remain visible for most of the clip.
  - Heavy long-duration occlusion, moving cameras, and multi-goalie scenes are outside normal benchmark scope.
  - v1 body tracking is limited to body landmarks only; stick landmarks are reserved for future versions.

- Operational constraints:
  - v1 supports batch processing only.
  - Every processed clip must produce an annotated review artifact and machine-readable pose output.
  - Manual visual review is required during benchmarking to verify tracking quality.
  - Development should remain benchmark-driven, using a small curated clip set before broader expansion.
  - Runs should be reproducible through clip IDs, run IDs, config versions, and model/version metadata.

- Product constraints:
  - v1 must remain narrow enough to be implemented, tested, and demonstrated by a solo student developer.
  - v1 is a tracking and review tool, not an automated coaching or diagnosis system.
  - v1 must support only fixed-camera, single-goalie, post-session analysis.
  - v1 must not expand to live inference, puck tracking, multi-player analysis, or stick-state classification.
  - v1 should prioritize clear, reviewable outputs over advanced analytics.

## 9. Acceptance Criteria for This Document

- [x] Problem statement is defined.
- [x] Target user is defined.
- [x] User value is defined.
- [x] v1 input is defined.
- [x] v1 output is defined.
- [x] Constraints are documented.
- [x] Non-goals are documented.
- [x] v1 is frozen as fixed camera only.
- [x] v1 is frozen as single goalie only.
- [x] v1 is frozen as batch processing only.
- [x] v1 excludes stick-state classification.
- [x] Expected output format is described.
- [x] A reader can answer what goes in.
- [x] A reader can answer what comes out.
- [x] A reader can answer what is in scope.
- [x] A reader can answer what is out of scope.

## 10. Notes

This is the foundation document for all other Phase 0 work.
