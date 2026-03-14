# Dausecure Roadmap

## Product Direction
Dausecure is an Apple-focused local security agent system for `macOS` and `iOS`.

The product goal is to demonstrate:

- cybersecurity engineering capability
- AI-assisted local triage capability
- practical Apple-platform security focus
- explainable detection logic instead of vague AI claims

The intended shape of the product is:

- a `macOS` local agent for data collection and analysis
- an `iOS` companion experience for alerts, review, and lightweight security workflows
- a local LLM triage layer that summarizes and prioritizes cases without becoming the source of truth

## Scope Principles
- deterministic parsing and evidence scoring come first
- local-first and privacy-first defaults are core to the product story
- `macOS` is the primary endpoint for deep visibility
- `iOS` is a constrained companion surface, not a full low-level endpoint agent
- browser/web protection is a focused vertical inside the broader Apple security story

## Phase 0: Foundation
Goal: define a credible MVP boundary and the data model.

Deliverables:

- document the MVP architecture and product framing
- define a normalized schema for:
  - source artifact
  - extracted IOC
  - telemetry event
  - incident/case
  - LLM triage output
- define IOC types:
  - IP
  - domain
  - URL
  - hash
  - file path
  - process name
  - bundle ID
  - browser/web indicator
- define initial allowlists and suppression rules
- define a small sample dataset for testing

Exit criteria:

- one written schema covering ingestion, extraction, scoring, and triage
- a small corpus of safe public test artifacts and local sample telemetry

## Phase 1: Ingestion Pipeline
Goal: reliably ingest safe inputs into one internal format.

Inputs:

- markdown and HTML malware writeups
- JSON sandbox reports
- CSV or TXT IOC lists
- normalized `macOS` event JSON

Deliverables:

- parsers for each source type
- normalized document output
- source metadata capture:
  - title
  - source type
  - date
  - verdict section if present
  - body text
  - extracted metadata

Exit criteria:

- sample inputs from at least 3 source types convert into one consistent schema

## Phase 2: Deterministic IOC Extraction
Goal: build the non-LLM core of the system.

Deliverables:

- regex/parsers for:
  - IPs
  - domains
  - URLs
  - hashes
  - file paths
  - process names
  - bundle IDs
- evidence capture for each extracted IOC:
  - exact value
  - source file/report
  - surrounding text snippet
  - section label
  - extraction rule used
- de-duplication and normalization logic

Possible supporting tools:

- `iocextract`
- YARA-based workflows
- simple enrichment against local allowlists

Exit criteria:

- a repeatable extractor that returns explainable output on the sample corpus

## Phase 3: Scoring and Incident Builder
Goal: turn isolated findings into analyst-usable cases.

Scoring model:

- found in title or verdict section: `+30`
- repeated across sources: `+20`
- appears in behavior section: `+20`
- appears only once in noisy text: `+5`
- matches allowlist: `-40`

Deliverables:

- scoring engine with evidence reasons
- case builder that groups:
  - source report
  - extracted IOCs
  - related files/processes
  - confidence score
  - notes
- severity and confidence labels that are easy to explain

Exit criteria:

- each case shows why it was created and why the confidence score was assigned

## Phase 4: `macOS` Agent MVP
Goal: collect a small set of local Apple security signals worth triaging.

Initial signal ideas:

- process execution events
- suspicious file writes in sensitive locations
- launch agent / launch daemon persistence hints
- unsigned or newly dropped binaries
- network destinations observed by the local pipeline
- browser download or quarantine-related signals

Deliverables:

- lightweight local collector or background agent
- normalized event output for the same case pipeline
- local storage for recent cases and events

Exit criteria:

- local `macOS` signals can be turned into the same incident format as external artifacts

## Phase 5: Browser/Web Protect
Goal: show a focused user-facing security capability that fits both the Apple and AI story.

Focus areas:

- suspicious download indicators
- known-bad domains and URLs from public reports
- phishing or fake update patterns represented as explainable signals
- browser-related case grouping

Deliverables:

- browser/web indicator schema
- browser-related scoring rules
- analyst or user-friendly summaries for web-originated cases

Exit criteria:

- browser/web-originated cases appear as first-class incidents in the system

## Phase 6: Local LLM Triage
Goal: use AI where it adds value without making it the detection engine.

Model/runtime:

- Ollama via local API at `http://localhost:11434/api`

Responsibilities for the LLM:

- summarize a built case in plain language
- prioritize likely important findings
- produce structured triage output
- suggest analyst next steps

Responsibilities the LLM should not own:

- raw IOC extraction
- final source-of-truth detection logic
- unsupported claims about malware intent or guaranteed maliciousness

Exit criteria:

- structured triage output is consistent, local, and grounded in extracted evidence

## Phase 7: `iOS` Companion
Goal: give the project a credible `iOS` story without overclaiming endpoint access.

Possible scope:

- view recent alerts and cases
- review browser/web protection findings
- save notes and triage decisions
- show Apple-security hygiene reminders or checklists

Important constraint:

- `iOS` should be framed as a companion app or review surface unless you have a legitimate data source that supports deeper visibility

Exit criteria:

- the repo can honestly claim `macOS` agent plus `iOS` companion support

## Demo Milestones
These are the best milestones for demonstrating cybersecurity and AI capability publicly.

### Milestone 1
- ingest public malware-analysis artifacts
- extract IOCs deterministically
- score them into explainable cases

### Milestone 2
- add `macOS` telemetry ingestion
- correlate local events with artifact-derived indicators

### Milestone 3
- add local Ollama triage with structured output
- show analyst-facing case summaries

### Milestone 4
- add browser/web protection scenarios
- add `iOS` companion review flow

## Success Criteria
The MVP is successful if it can:

- ingest safe public artifacts and local `macOS` signals
- extract and normalize IOCs deterministically
- build explainable incident cases
- summarize and prioritize those cases with a local LLM
- clearly show Apple-platform focus and privacy-first design

## Immediate Build Order
1. Lock the schema for documents, telemetry, IOCs, cases, and LLM outputs.
2. Build the ingestion pipeline for reports, sandbox JSON, IOC lists, and local event JSON.
3. Implement deterministic extraction and normalization.
4. Add scoring and incident construction.
5. Add a basic `macOS` collector or test event feed.
6. Integrate Ollama for structured local triage.
7. Add browser/web protection rules and examples.
8. Add a minimal `iOS` companion or review layer.
