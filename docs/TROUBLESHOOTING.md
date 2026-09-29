# Troubleshooting Guide

This guide covers common issues, edge cases, and recovery steps when operating the **Email Lable Automation** n8n workflow.

---

## 1. Missing Labels in Gmail on First Run

### Symptom
None of the 5 labels exist in Gmail when importing the workflow.
### Expected Behaviour
This is expected behavior. The workflow contains a **Self-Healing Label Manager** sub-graph:
1. `Check Labels` checks for cached IDs vs live Gmail IDs.
2. Because none exist, `Labels OK?` evaluates to `FALSE`.
3. `What Went Wrong` classifies each missing label as `MISSING`.
4. `Create Label` issues a POST request to the Gmail REST API for all missing labels with designated background and text colors.
5. `Update` stores the newly returned label IDs into workflow static data (`labelCache`).
6. Execution proceeds immediately to `Prepare` without requiring user intervention.

---

## 2. "Bad Request" / 400 on `Label Email` Immediately After Creation

### Symptom
When a label is newly created, the very next node (`Label Email`) throws a 400 Bad Request or claims the label ID is not valid.
### Root Cause
Gmail API metadata indexes can experience propagation delays of 1-3 seconds between a label creation call and message modification calls referencing that label ID.
### How the Workflow Handles It
- The `Label Email` node is configured with `onError: continueErrorOutput`.
- If an error occurs, the execution branches to `Clear Cache and stop`.
- `Clear Cache and stop` deletes `$getWorkflowStaticData('global').labelCache` and throws an error to fail the execution.
- On the next scheduled polling run (1 minute later), the label is already fully indexed in Gmail. The workflow discovers the existing label via `Get Labels`, populates the cache, and processes the email cleanly.

> [!TIP]
> **Optional Hardening**: You can optionally enable **Retry On Fail** (e.g., 2 tries with 2000 ms delay) on the `Label Email` node in your instance if your Gmail account experiences latency during label initialization.

---

## 3. Workflow Static Data Cache Does Not Persist During Manual Test Runs

### Symptom
Running a test manually inside the n8n canvas always appears to check or re-heal labels, or static data appears empty.
### Explanation
In n8n, **Workflow Static Data** (`$getWorkflowStaticData('global')`) is only persisted across runs when executed via an **active trigger** (production runs). In manual executions initiated via "Test step" or "Test workflow", static data changes are kept in memory for the run duration only and reset upon completion.
To verify persistent caching, ensure the workflow is toggled **Active** and inspect active execution logs.

---

## 4. NVIDIA API 404 or Model Not Found

### Symptom
The `Classifier` node returns a `404 Not Found` or `Model not found` response.
### Root Cause
NVIDIA NIM periodically updates model slug names or deprecates older preview endpoints.
### Resolution
1. Visit [build.nvidia.com](https://build.nvidia.com/) and check the active model catalog.
2. Locate the latest Nemotron model identifier (e.g., `nvidia/nemotron-3-ultra-550b-a55b` or current equivalent).
3. Open the `Classifier` node in n8n.
4. Update the `"model"` field in the JSON request body:
   ```json
   "model": "nvidia/nemotron-3-ultra-550b-a55b"
   ```
5. Confirm your `Authorization: Bearer <API_KEY>` header credential is valid and has remaining API credits.

---

## 5. Gmail Color Palette Rejection (Hex Values)

### Symptom
A label creation request fails with `Invalid color` error.
### Root Cause
The Gmail API restricts label colors to a predefined palette of approved background and text color pairs. Arbitrary RGB/hex codes are rejected by Google's API.
### Resolution
The workflow's `Check Labels` node uses pre-validated colors that strictly conform to Gmail's palette:
- `⚡ Action`: `#cc3a21` (text: `#ffffff`)
- `💼 Work`: `#285bac` (text: `#ffffff`)
- `👤 Personal`: `#16a766` (text: `#ffffff`)
- `💵 Finance`: `#fad165` (text: `#000000`)
- `🗑️ Ignore`: `#666666` (text: `#ffffff`)

Do not modify these hex values unless substituting with an officially supported Gmail API palette color pair.

---

## 6. Emails in Sent or Drafts Being Processed

### Symptom
Sent messages or draft emails appear to trigger processing.
### How the Workflow Protects Against This
The `Prepare` code node explicitly checks message label IDs and ignores any item containing `SENT` or `DRAFT`:
```javascript
if (labelIds.includes('SENT') || labelIds.includes('DRAFT')) {
  continue;
}
```
This guarantees only true incoming emails are processed through the AI classification pipeline.
