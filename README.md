# Dausecure

## MVP Name
Local Security IOC Analyst

## Core Pitch
A local, privacy-first AI security tool that ingests malware-analysis artifacts and Apple-platform telemetry, extracts candidate IOCs with deterministic logic, then uses a local LLM to summarize and prioritize them.

## Important Framing
Do not describe this as:

> "My LLM predicts IOCs from malware."

Describe it as:

> "My pipeline extracts and prioritizes likely IOCs from malware-analysis artifacts and security telemetry using deterministic parsing plus local LLM triage."

## Starting Source Index
Use [Awesome Malware Analysis](https://github.com/rshipp/awesome-malware-analysis?tab=readme-ov-file#malware-collection) as the initial resource index for malware-analysis collections, IOC tooling, and related threat-intelligence references.

## Safe MVP Data Sources
Use safe artifacts first:

- malware writeups
- sandbox reports
- YARA results
- strings output
- public hashes already listed in reports
- published IOC lists
- normalized macOS telemetry from your own collector

This is enough to prove the system without making raw malware handling the center of the project.

## MVP Architecture

### 1. Ingestion
Inputs:

- markdown or HTML malware reports
- JSON sandbox results
- CSV or text IOC lists
- macOS event JSON from your collector

Output:

- normalized documents in one schema

### 2. Deterministic IOC Extraction
Use regex and parsers first:

- IPs
- domains
- URLs
- hashes
- file paths
- process names
- bundle IDs

IOC-related tooling such as `iocextract` and MISP fit naturally into this stage.

### 3. Confidence Scoring
Use simple evidence-based scoring:

- found in title or verdict section: `+30`
- repeated across sources: `+20`
- appears in behavior section: `+20`
- appears only once in noisy text: `+5`
- matches allowlist: `-40`

### 4. Incident Builder
Group related findings into a case:

- source report
- extracted IOCs
- related processes/files
- confidence
- notes

### 5. Local LLM Triage
Run locally with Ollama for summarization and prioritization over the extracted case data.

Integration assumptions for the MVP:

- local API endpoint at `http://localhost:11434/api`
- structured outputs where useful
- OpenAI-compatible API surface for easier app integration
- local execution to keep prompts and data on-device

## MVP Goal
Build a privacy-first pipeline that turns malware-analysis artifacts and Apple telemetry into normalized cases with extracted IOCs, confidence scores, and local-LLM triage summaries.

## Non-Goals for the MVP
- raw malware execution as the central workflow
- speculative IOC prediction
- cloud-only analysis
- heavy enterprise SIEM integrations

## Suggested Next Steps
- define a normalized event and report schema
- build deterministic extractors and allowlists
- create a scoring engine with explainable evidence
- add local Ollama triage prompts with structured outputs
- build a simple analyst-facing case review UI
