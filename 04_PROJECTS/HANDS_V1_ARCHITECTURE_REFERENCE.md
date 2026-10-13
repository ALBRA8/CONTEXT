# HANDS V1.0 — ARCHITECTURE REFERENCE

## Evidence status

`ARCHITECTURE / IMPLEMENTATION REFERENCE`

This record preserves the important architectural characteristics of the HANDS V1.0 work without duplicating the implementation source code. The implementation remains owned by the YOUTUBE-AUTOMATION repository.

## Purpose

HANDS is the controlled execution layer that gives YOUTUBE-AUTOMATION the ability to perform constrained desktop/browser/flow actions when normal APIs or integrations are insufficient.

It is an execution capability/layer, not a separate Video Editor Agent.

## Core execution loop

`OBSERVE → IDENTIFY → ACTION → OBSERVE → VERIFY`

The loop is designed so actions are based on observed state and are followed by observation and verification rather than blind execution.

## Contract model

The V1.0 design included:

- explicit action contracts;
- 9 contract states;
- explicit state transitions;
- `UNKNOWN` can never transition directly to `COMPLETED`;
- a defined error taxonomy with 10 error classes;
- persisted auditable execution sessions.

## Operations

The reported V1.0 implementation contained:

- 21 core operations;
- 11 Flow-specific operations;
- target hierarchy;
- structured observation.

## Security model

HANDS follows deny-by-default execution principles:

- permissions are denied unless explicitly allowed;
- allowlists constrain commands by binary plus permitted prefix;
- shell execution is not used;
- desktop/browser/flow operations are separated by controlled interfaces;
- workspace boundaries are enforced;
- filesystem traversal is constrained;
- anti-symlink protections prevent boundary escape.

## Workspace isolation

The V1.0 report defined an isolated workspace with 8 controlled areas and an explicit filesystem boundary.

## Evidence and auditability

Execution evidence is designed to be:

- structured JSONL;
- SHA-256 chained/recorded as applicable by the implementation;
- automatically redacted for secrets;
- retained as an audit trail;
- associated with persisted sessions.

## Concurrency / locks

The architecture includes:

- desktop lock;
- browser lock;
- flow lock;
- cross-process file lock;
- lock expiry handling.

These controls are intended to prevent conflicting physical/desktop execution and stale-lock deadlocks.

## Kill switch

The V1.0 design includes a kill-switch path resulting in `STOPPED`, releasing locks while preserving evidence.

## Operators

Desktop Operator and Flow Operator are specialized controllers/operators within the audiovisual execution architecture. They are not super-agents and do not replace YOUTUBE-AUTOMATION as orchestrator.

## Verification boundary

The architecture deliberately distinguishes:

- BUILT — implementation exists;
- TESTED — automated tests pass;
- AUDITED — defined audit completed;
- CONNECTED — bridge/integration wired;
- E2E VERIFIED — real external/physical production execution demonstrated.

HANDS/Execution Contract reached a connected bridge layer in later work, but real physical Google Flow/Desktop production remained a separate verification boundary.

## Architectural decision

Do not infer that the existence of HANDS justifies a separate Video Editor Agent. The original decision was to test YOUTUBE-AUTOMATION + HANDS + specialized operators in real production first and only create another specialist if evidence demonstrates that the existing architecture is insufficient.

## Source of truth

This document is a continuity reference. It is not the implementation source. For exact behavior, interfaces, tests and code, consult the current YOUTUBE-AUTOMATION repository.

## Status

- Historical HANDS V1.0 architecture: PRESERVED.
- Implementation repository: source of truth.
- Real physical Flow/Desktop E2E: NOT_VERIFIED by this record.
- Separate Video Editor Agent: NOT_IMPLEMENTED / intentionally deferred.
