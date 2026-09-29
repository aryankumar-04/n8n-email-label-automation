# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-09-29

### Added
- **Initial Release of "Email Lable Automation"** n8n workflow.
- **Gmail Trigger Integration**: 1-minute scheduled polling for new incoming emails with automatic filtering of `SENT` and `DRAFT` messages.
- **5-Tier Label Taxonomy**:
  - `⚡ Action` (`#cc3a21`) for urgent items, legal matters, security alerts, and OTPs.
  - `💼 Work` (`#285bac`) for work-related correspondence and meetings.
  - `👤 Personal` (`#16a766`) for personal emails, social alerts, and travel.
  - `💵 Finance` (`#fad165`) for invoices, receipts, and order shipping.
  - `🗑️ Ignore` (`#666666`) for newsletters, promos, and spam.
- **Self-Healing Label Architecture**:
  - Automated detection of missing labels or modified label IDs against Gmail's live API.
  - Automatic REST API creation for missing labels with matching Gmail color palettes.
  - In-memory static data cache (`labelCache`) to avoid repetitive API lookups.
- **Sequential Batch Processing**:
  - Split In Batches queue processing (batch size 1) to reliably handle bursts of 10-15+ simultaneous incoming emails.
  - Automatic 1500-character body snippet truncation with HTML stripping.
- **AI Classification via NVIDIA NIM**:
  - Direct integration with `nvidia/nemotron-3-ultra-550b-a55b` via Header Auth.
  - Prompt structured for strict JSON output with `<think>` tag stripping.
  - Safe fallback to `⚡ Action` if model response is ambiguous or malformed.
- **Safety & Error Boundaries**:
  - Automatic movement of `🗑️ Ignore` emails to Gmail Trash (safe 30-day recovery, no permanent hard deletion).
  - Error-triggered cache flush on labeling failure with loud execution termination to guarantee clean recovery on subsequent poll.
- **In-Canvas Sticky Notes**: 7 color-coded n8n sticky notes providing visual architecture overview and setup guidance directly on the n8n canvas.
- **Comprehensive Documentation**: Complete setup guides, PRD specification, and troubleshooting manual.
