# VirusTotal Integration Notes

## Why It Fits
VirusTotal fits this project as an optional enrichment layer after local extraction.

That means Dausecure should:

- extract indicators locally from malware-analysis artifacts and Apple telemetry
- score those indicators with explainable local rules
- optionally query VirusTotal for additional context on public indicators

That means Dausecure should not:

- depend on VirusTotal to find the indicators in the first place
- replace local evidence scoring with vendor verdicts
- upload raw files by default in the MVP

## Best MVP Scope
Use VirusTotal only for indicator types that fit the current product story and have clear analyst value:

- SHA-256 or SHA-1 file hash lookup
- domain lookup
- URL lookup
- optional IP lookup later if network telemetry becomes first-class

## Recommended Pipeline Placement
1. Ingest reports, sandbox output, IOC lists, or local `macOS` telemetry.
2. Extract IOCs deterministically.
3. Normalize and de-duplicate indicators.
4. Query VirusTotal for supported indicators.
5. Attach enrichment to the case as evidence.
6. Run local LLM triage over the combined case data.

## Data Model Additions
Each IOC can optionally include an enrichment block such as:

- provider: `virustotal`
- lookup_type: `file_hash`, `domain`, or `url`
- lookup_value
- lookup_time
- permalink
- last_analysis_stats
- categories or labels
- reputation if present
- error state if the lookup fails or is rate-limited

## Privacy Boundaries
For the MVP:

- allow external lookup only for indicators already extracted locally
- prefer public hash, domain, and URL queries
- do not upload raw binaries by default
- if file upload is ever added, make it explicit in the UI and docs

This keeps the product aligned with the privacy-first story while still giving practical analyst value.

## Demo Story
The clearest VirusTotal demo for this repo is:

- ingest a public malware writeup or sandbox report
- extract a hash, domain, or URL locally
- enrich it with VirusTotal
- show a case summary that combines local evidence with VirusTotal context

For Apple-specific positioning, a strong variant is:

- observe a suspicious `macOS` download or browser-originated URL
- extract the related hash or domain
- enrich with VirusTotal
- summarize the case locally with Ollama

## Near-Term Build Tasks
- add a small VirusTotal client module
- define a normalized enrichment schema
- cache lookups locally to avoid repeated API calls
- surface enrichment separately from local confidence scoring
- include the VirusTotal permalink in analyst-facing case output
