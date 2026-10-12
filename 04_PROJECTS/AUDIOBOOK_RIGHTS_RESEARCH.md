# AUDIOBOOK / RIGHTS RESEARCH — CLOSURE RECORD

## Evidence status

This project is currently an **IDEA / PROPOSAL**, not a verified implemented system or completed legal-clearance workflow.

## Objective

Explore a workflow for producing audiobooks from suitable older/classic works while separating creative production from rights/clearance research.

## Proposed production side

KREA was identified as the likely production agent because its documented direction includes creating books, PDFs and other creative outputs.

## Proposed research sources

The conversation identified possible research targets such as:

- Project Gutenberg.
- Internet Archive.
- Digital libraries.
- Older/classic books.
- Defunct publishers.
- Author death dates.
- Possible heirs/rightsholders.
- Public-domain status.

These are research targets, not proof that any specific work is public domain or cleared.

## Architectural decision direction

The conversation moved away from putting broad legal/rights research into KREA, Leads or Chismoso by default. The preferred structural direction became a **Digital Legal Office / legal-agent pool** with specialist subagents by legal area, so domain responsibility is not overloaded onto unrelated specialists.

The proposed legal structure included a Director Jurídico plus specialist areas such as IP/Copyright, legal case/contract work, evidence/provenance and a Legal Gate.

## Separation of responsibilities

- KREA: creative/audiobook production side.
- Legal Office: legal research, rights/clearance and legal risk gating.
- Leads/Chismoso: remain in their own specialties unless a later approved contract delegates a specific task.

## Implementation status

- Audiobook production workflow: NOT_VERIFIED.
- Rights-clearance engine: NOT_VERIFIED.
- Completed public-domain determinations: NONE recovered from this conversation.
- Legal specialist pool: PROPOSED / NOT_IMPLEMENTED evidence.
- Legal clearance of any specific title: NOT_VERIFIED.

## Critical rule

No historical discussion of Project Gutenberg, Internet Archive, author death, heirs, defunct publishers or public-domain status should be interpreted as a completed legal determination. The workflow was designed, not legally executed.
