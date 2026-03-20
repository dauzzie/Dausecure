# Dausecure

See the implementation roadmap in [ROADMAP.md](./ROADMAP.md).

## MVP Name
Local Security IOC Analyst

## Core Pitch
A local, privacy-first AI security tool that ingests malware-analysis artifacts and Apple-platform telemetry, extracts candidate IOCs with deterministic logic, then uses a local LLM to summarize and prioritize them.

Optional external enrichment can be added through VirusTotal lookups for public indicators such as hashes, domains, and URLs.

## Platform Focus
This project is hyperfocused on `macOS` and `iOS` only.

## Additional Feature Focus
- browser/web protection as part of the overall security analysis and triage workflow

## Important Framing
Do not describe this as:

> "My LLM predicts IOCs from malware."

Describe it as:

> "My pipeline extracts and prioritizes likely IOCs from malware-analysis artifacts and security telemetry using deterministic parsing plus local LLM triage."

## Starting Source Index
Use [Awesome Malware Analysis](https://github.com/rshipp/awesome-malware-analysis?tab=readme-ov-file#malware-collection) as the initial resource index for malware-analysis collections, IOC tooling, and related threat-intelligence references.

## Resource Strategy
The `awesome-malware-analysis` repository is best used as a curated index of sources, tooling, and testing inputs for the MVP.

Important framing:

- do not position this as a system that "predicts IOCs from malware"
- position it as a pipeline that extracts and prioritizes likely IOCs from analysis artifacts and Apple telemetry
- use malware samples and public analysis artifacts as testing inputs and validation data, not as the only product story

## Most Useful Parts of the Source Index for This MVP
Based on the referenced repository, the sections that fit this project best are:

- `Malware Collection -> Malware Corpora`
  - useful for locating public sample repositories and analysis-linked artifacts
  - better for controlled research and validation than for making raw malware handling the center of the MVP
- `Open Source Threat Intelligence -> Tools`
  - especially relevant for IOC extraction and enrichment workflows such as `iocextract`, MISP, and related tooling
- `Online Scanners and Sandboxes`
  - useful for generating or consuming sandbox reports, behavior summaries, and structured analysis artifacts
- `Detection and Classification`
  - useful for YARA-driven matching, rule testing, hashing, and evidence-based scoring
- `Browser Malware`
  - directly relevant to the browser/web protection part of this project

## What Is Most Relevant for macOS and iOS
This source index is broad and not specifically focused on Apple platforms. For a `macOS` and `iOS` MVP, the most relevant usage is:

- general IOC extraction from public malware writeups, YARA results, sandbox outputs, and published IOC lists
- browser- and web-related malware analysis resources that support `browser/web protect`
- cross-platform extraction tools such as `iocextract` and YARA that can still be applied to Apple-focused artifacts
- your own normalized `macOS` telemetry as a first-class input

Important limitation:

- I did not find an `iOS`-specific section in the referenced repository page
- `macOS` appears only incidentally in tool descriptions, not as a dedicated Apple malware collection section
- that means the list is useful as a general malware-analysis index, but Apple-specific coverage should come from your telemetry pipeline and additional `macOS` / `iOS` threat-research sources over time

## Safe MVP Data Sources
Use safe artifacts first:

- malware writeups
- sandbox reports
- YARA results
- strings output
- public hashes already listed in reports
- published IOC lists
- normalized macOS telemetry from your own collector

Optional enrichment inputs:

- VirusTotal lookups for hashes, domains, and URLs that were already extracted locally

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
- browser- and web-related indicators where relevant

IOC-related tooling such as `iocextract` and MISP fit naturally into this stage.

### 3. Confidence Scoring
Use simple evidence-based scoring:

- found in title or verdict section: `+30`
- repeated across sources: `+20`
- appears in behavior section: `+20`
- appears only once in noisy text: `+5`
- matches allowlist: `-40`

### 4. Optional Threat Intel Enrichment
After deterministic extraction, enrich public indicators without making enrichment the source of truth.

Suggested VirusTotal usage for the MVP:

- file hash lookups for known malware or suspicious binaries
- domain reputation lookups for browser/web protection cases
- URL lookups for phishing or fake update scenarios
- evidence attachment such as last analysis stats, labels, and report links

Important constraints:

- keep local extraction and scoring as the primary detection logic
- prefer hash, domain, and URL lookup before any raw file upload workflow
- treat VirusTotal as enrichment and validation, not as the product's core detection engine

### 5. Incident Builder
Group related findings into a case:

- source report
- extracted IOCs
- related processes/files
- confidence
- notes

### 6. Local LLM Triage
Run locally with Ollama for summarization and prioritization over the extracted case data.

Integration assumptions for the MVP:

- local API endpoint at `http://localhost:11434/api`
- structured outputs where useful
- OpenAI-compatible API surface for easier app integration
- local execution to keep prompts and data on-device

## MVP Goal
Build a privacy-first pipeline that turns malware-analysis artifacts and Apple telemetry into normalized cases with extracted IOCs, confidence scores, optional VirusTotal enrichment, and local-LLM triage summaries.

## Non-Goals for the MVP
- raw malware execution as the central workflow
- speculative IOC prediction
- cloud-only analysis
- heavy enterprise SIEM integrations

## Suggested Next Steps
- define a normalized event and report schema
- build deterministic extractors and allowlists
- create a scoring engine with explainable evidence
- add a VirusTotal enrichment client for extracted hashes, domains, and URLs
- add local Ollama triage prompts with structured outputs
- build a simple analyst-facing case review UI
- add browser/web protection signals for `macOS` and `iOS` threat scenarios
