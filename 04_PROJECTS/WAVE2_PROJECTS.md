# ALBRA — PROJECTS WAVE 2

This document continues, without replacing, the first migration. Statuses distinguish implemented systems from proposals and experiments.

## P-0001 — ALBRA Agent Seed / AGENTE-GENERAL

- Repo: `ALBRA8/AGENTE-GENERAL`
- Purpose: generic domain-agnostic ALBRA seed from which specialized agents can be cloned.
- Final code before freeze: `8437f2a`.
- Certification: `00a353c`.
- Final reported state: READY_TO_FREEZE / frozen reference.
- Universal DNA: 106 components.
- Final reported gates: 487 tests on Python 3.10/3.11/3.12; CI green; audit matrix 40/40; parity 106/106; contamination clean; clean-room 17/17 + 2 optional; CORE_ONLY intact; security fail-open and HTTP execution boundary closed.
- Important historical evolution: `72dc5e4` → `4c642df` → `b272278` → `8af913e...` → `1d84d8f` → `914c016` → `3b51385` → `089a1dc` → `072e22a` → `8437f2a` / `00a353c`.
- Constraint: this is the generic seed, not a mandatory orchestrator or super-agent.

## P-0002 — YOUTUBE AUTOMATION

- Repo: `ALBRA8/YOUTUBE-AUTOMATION`.
- Goal: local audiovisual factory with automated production and optional publication.
- Core pipeline: script → images → voice → render → subtitles → music → optional YouTube publication.
- Includes factory scheduling, queue of ideas, URL mode, audio mode, avatars, scene editor and multi-platform kit.
- Current architecture decision: keep it as audiovisual orchestrator; add HANDS first; do not create a separate Video Editor Agent until a real production proves it necessary.

## P-0003 — HANDS V1.0

- Location: `YOUTUBE-AUTOMATION/backend/services/hands/`.
- Goal: physical execution layer for audiovisual operations when APIs/integrations are insufficient.
- State historically reported: BUILT · TESTED · AUDITED · NOT CONNECTED; later confirmed uploaded to repository after extension corrections.
- Security model: deny-by-default, allowlists, isolated workspace, locks, kill switch, evidence and session audit.
- Explicit decision: prepare and test hands before introducing another audiovisual agent.

## P-0004 — EEC / Mission Control

- Status: PROJECT / DESIGN PHASE.
- Goal: global visual control plane for ALBRA: agent offices, activity, calendar, meeting room, task board, client/prospect view, documents/conversations and heartbeat/background work.
- Architectural constraint: EEC should be a control plane, not a super-agent.
- Open design question: whether a separate orchestrator/Hermes is actually needed after EEC is completed.

## P-0005 — DESPACHO JURÍDICO DIGITAL ALBRA

- Repo: `ALBRA8/DESPACHO-JURIDICO`.
- Base V1.0 intentionally without legal knowledge loaded.
- Conversation architecture: legal-agent pool / despacho with specialists rather than overloading Krea, Chismoso or Leads.
- Proposed specialist family: Director Jurídico, IP/Copyright, Legal Case/Contract, Evidence/Provenance, Legal Gate.
- Proposed Legal Gate states: APPROVED, APPROVED_WITH_LIMITS, REVIEW_REQUIRED, BLOCKED, UNKNOWN.
- The repository README confirms the base organism and future specialization phases, but does not prove that the proposed specialist pool is already implemented.

## P-0006 — CRM ALBRA

- Repo: `ALBRA8/CRM-ALBRA`.
- Multi-tenant CRM with AI agent, durable automations, WhatsApp daemon, opportunities, clients, products, calendar, quotes, finance, reports, team and audit activity.
- Relationship: AGENTE-LEADS has optional CRM hooks; CRM remains a separate commercial system.

## P-0007 — NEX-SCOPE

- Repo: `ALBRA8/NEX-SCOPE`.
- AI-powered YouTube niche finder.
- Provides niche keywords, trends, content gaps, 30-video plans, monetization analysis, competitor analysis, chat and YouTube API proxy.
- Relationship: discovery/market intelligence feeding YouTube decisions; not the audiovisual orchestrator.

## P-0008 — RADAR-SECOP2

- Repo: `ALBRA8/RADAR-SECOP2`.
- Detects and prepares public procurement opportunities from SECOP II.
- Includes deterministic filters, explainable compatibility, tender analysis, Marco Lógico, Evidence Contract, MemoryDV, skills, Doctor, autonomy and MCP.
- Does not automatically submit offers.

## P-0009 — Audiobook opportunity / rights research

- Status: IDEA / PLANNING, not a completed implementation.
- Goal discussed: use Krea to create audiobook/product artifacts while another specialist handles discovery and rights research.
- Research sources considered: Project Gutenberg, Internet Archive, digital libraries, old/classic books, uncertain rights status.
- Legal concern: verify public-domain/copyright status, author death, heirs/rights holders and provenance before commercial use.
- Architectural direction: do not overload Krea, Leads or Chismoso; legal specialization may own rights clearance.

## P-0010 — CONTEXT migration

- Repo: `ALBRA8/CONTEXT`.
- Purpose: external, versioned, portable continuity memory for ALBRA.
- PR #1 was merged successfully; Wave 2 continues from the resulting `main` state and must not recreate Wave 1.
