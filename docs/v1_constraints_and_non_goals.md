# CreaseTrack v1 Constraints and Non-Goals

## 1. Purpose

- Freeze the practical boundary of CreaseTrack v1.
- Prevent scope creep during product and engineering decisions.
- Make new requests easy to classify as `YES`, `NO`, or `NOT-YET`.

## 2. Version Boundary Summary

CreaseTrack v1 is:
- fixed-camera only
- single-goalie only
- batch/offline processing only
- intended for controlled training footage
- focused on pose / landmark tracking for one goalie

CreaseTrack v1 is not:
- a live analytics system
- a multi-person tracking system
- a multi-camera fusion system
- a stick-state classifier
- a puck tracker
- an event detection engine
- a generalized hockey understanding model

## 3. Core Assumptions

- The goalie is visible enough for tracking through most of the clip.
- There is one primary person to track: one goalie only.
- The camera is mostly static for the full clip.
- Processing happens after recording; there is no live inference requirement.
- Inputs come from a single camera; there is no multi-camera fusion.
- Output is body pose / landmarks only; there is no stick-state output.
- The main use case is controlled training footage, not uncontrolled game footage.
- The goal is stable reviewable tracking, not full-scene interpretation.

## 4. Supported Conditions

Works best under these conditions:
- One goalie clearly visible for most frames
- Fixed camera with little or no pan, tilt, or zoom
- Controlled training footage
- Good rink lighting and usable subject contrast
- Lower body usually visible
- Moderate resolution and compression
- Limited background clutter near the crease
- Short to moderate partial occlusions that do not hide the goalie for long

## 5. Weak Conditions

May partially work, but results are unreliable:
- Brief partial occlusion by net, coach, or equipment
- Minor camera shake from a fixed setup
- Uneven lighting or mild shadowing
- Moderate compression artifacts
- Goalie smaller in frame but still identifiable
- Short periods where legs or skates are partly obscured
- Similar-looking nearby people that do not persist in the tracking area

These cases are not acceptance targets for v1.

## 6. Unsupported Conditions

Reject as out of scope for v1:
- Live or real-time inference requirements
- Multi-goalie or multi-player tracking
- Moving-camera footage
- Multi-camera fusion
- Stick-state classification
- Puck tracking
- Event detection or play classification
- General rink-wide scene understanding
- Autonomous coaching advice or diagnosis
- Clips where the goalie is absent for long periods

## 7. Known Failure Zones

Expect tracking quality to break down in these conditions:
- Severe occlusion
- Heavy camera motion
- Poor visibility
- Multiple similar subjects near the goalie
- Obstructed lower body
- Extreme zoom or heavy compression
- Bad lighting
- Long out-of-frame periods
- Fast viewpoint changes from operator movement or reframing

## 8. Non-Goals

CreaseTrack v1 does not aim to:
- understand the full hockey scene
- model team tactics or player interactions
- infer puck possession, save events, or play outcomes
- classify stick position, blade angle, or stick state
- support live bench-side or in-practice analytics
- merge viewpoints across cameras
- become a general-purpose sports tracking platform

## 9. Request Triage Rule (yes / no / not-yet)

`YES`
- Improves reliability, usability, or output quality inside the fixed-camera, single-goalie, batch-only pose-tracking boundary.
- Does not expand inputs, outputs, runtime mode, or tracking scope.

`NOT-YET`
- Valuable for later phases, but expands v1 beyond its frozen boundary.
- Examples: real-time mode, broader robustness work for uncontrolled footage, multi-camera support, stick-state output, event detection.

`NO`
- Outside the CreaseTrack product direction, even beyond v1.
- Examples: generalized hockey understanding, full-team analytics, puck-first product direction, unrelated sports support.

If a request cannot be defended as clearly inside the v1 boundary, default to `NOT-YET` or `NO`.

## 10. Exit Rule for v1 Changes

Any change that expands:
- inputs
- outputs
- runtime mode
- tracking scope

must be treated as a version-boundary change, not a minor v1 tweak.

If the request changes what footage is accepted, what entities are tracked, what artifacts are produced, or whether processing must happen live, it is not a normal v1 change.
