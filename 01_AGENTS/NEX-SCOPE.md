# NEX-SCOPE — FICHA DE CONTEXTO

- Repository: `ALBRA8/NEX-SCOPE`
- Classification after Wave 3 evidence review: **SYSTEM / SAAS PLATFORM**, not AGENT.
- Status: IMPLEMENTED SAAS / VERIFIED DOCUMENTATION.

## Why it is classified as SYSTEM

The current repository README explicitly describes NexScope as an **AI-powered YouTube niche finder** and a SaaS platform. It exposes application views, REST endpoints, persistence, authentication and user-scoped saved entities. The evidence supports a product/platform classification rather than an autonomous ALBRA agent classification.

## Verified capabilities from repository README

- niche keyword analysis;
- YouTube trends;
- content gaps;
- 30-video content plans;
- monetization estimates;
- competitor/channel analysis;
- conversational AI;
- saved niches/channels/plans/messages;
- YouTube Data API proxy;
- multi-user persistence;
- JWT/httpOnly authentication;
- AI response parser and cache.

## Architecture

- Next.js 16 App Router.
- React 19 + TypeScript.
- Tailwind CSS 4 + shadcn/ui + Recharts + Framer Motion.
- Prisma + SQLite.
- Z.ai Web Dev SDK.
- Zustand.
- JWT authentication in httpOnly cookies.

## Main API surface documented

- auth register/login;
- health;
- keywords;
- trends;
- content gaps;
- content plans;
- monetization;
- competitor analysis;
- chat;
- saved niches/channels;
- chat messages;
- content plans;
- dashboard;
- YouTube Data API proxy.

## ALBRA relationship

NEX-SCOPE is a discovery/market-intelligence system for YouTube decisions. It can inform YOUTUBE-AUTOMATION and Creative workflows, but it is not the audiovisual orchestrator and there is no source evidence in the README that it should be classified as a generic ALBRA agent.

## Evidence status

**VERIFIED:** SYSTEM / SAAS classification is supported directly by the current README's product description and architecture.

**NOT_VERIFIED:** whether a future ALBRA agent wrapper/orchestrator may be added around NEX-SCOPE; no such implementation is claimed here.
