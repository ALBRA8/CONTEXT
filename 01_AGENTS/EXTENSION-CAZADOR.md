# EXTENSION-CAZADOR — FICHA DE CONTEXTO

- Repository: `ALBRA8/Extension-CAZADOR`
- Status: UPDATED WAVE 3 / VERIFIED FROM REPOSITORY README
- Classification: browser extension + local web/AI utility; not a general-purpose agent.

## Identidad recuperada

The repository README describes the project as an open-source recreation/improvement of Thunderbit: a Chrome extension that scrapes web pages with AI in two clicks.

The repository contains four major pieces:

- Chrome MV3 extension (`extension/`): Vite + React + TypeScript.
- Local AI backend (`server/`): Node.js HTTP + `z-ai-web-dev-sdk`.
- Landing page (`landing/`).
- Dashboard web (`dashboard/`).

## Capabilities verified from README

### Extension

- Chrome sidePanel.
- AI Suggest Fields.
- Column editor with text/number/price/date/URL/email/phone/image types.
- Cost estimator before scraping.
- Confidence score per column.
- CSV and real XLSX export.
- Persistent history in `chrome.storage.local`.
- Manifest V3 permissions: sidePanel, scripting, activeTab, storage.

### Backend

- `POST /api/suggest-fields`.
- `POST /api/scrape`.
- Empty-row filtering.
- CORS for extension.
- Spanish prompts with anti-hallucination rules.

### Dashboard

- Overview, recent scrapes, scheduled UI, templates, API keys and settings.
- The README explicitly states that dashboard data is mock/demo data for this MVP.

## Architecture

Active tab → Chrome sidePanel → MV3 background service worker → local Node backend → GLM SDK.

The service worker extracts compact page text, calls the local AI backend and persists history. The backend returns structured scraping results.

## Known limitations verified from README

- GLM-only backend; no multi-model routing in this project.
- No subpage scraping, pagination or bulk URLs in the documented MVP.
- Scheduled scraping exists only in dashboard UI, not backend.
- Dashboard uses mock data and requires a real API for production.
- Chrome only.

## Critical distinction vs Flow Script Processor

`Extension-CAZADOR` is **not** automatically the same system as the Chrome extension called **Flow Script Processor** inside `ALBRA8/YOUTUBE-AUTOMATION`.

Flow Script Processor is documented as a specialized bridge for Google Flow/ImageFX generation and writes video/image assets into YOUTUBE-AUTOMATION project folders.

Extension-CAZADOR is documented as a Thunderbit-like web scraping/capture utility with its own local AI backend, landing and dashboard.

Until a repository-level relationship proves otherwise, CONTEXT treats them as **separate systems**.

## Evidence status

**VERIFIED FROM REPOSITORY README:** structure, stack, implemented MVP capabilities and limitations.

**NOT_VERIFIED:** independent audit of current HEAD and any undocumented relationship with Flow Script Processor.
