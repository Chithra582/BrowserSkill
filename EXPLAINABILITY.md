# EXPLAINABILITY.md

This document explains the internal mechanisms, data lineage, operational boundaries, and governance framework of **BrowserSkill** (`browser-skill`) in accordance with the **OpenGAP v0.1.0** specification for the **HiDevs GitAgent Passport** clearance pipeline.

> **Agent Name:** BrowserSkill (`browser-skill`)  
> **Specification:** OpenGAP v0.1.0  
> **Category / Domain:** Developer Tools / Logged-In Browser Automation, VOM Inspection & Web Debugging  
> **Compliance Standard:** OpenGAP Checkpoint 2 (Explainability & Decision Governance), OWASP LLM Top 10, MITRE ATLAS  

---

## How the Agent Decides

BrowserSkill bridges AI coding agents with live Chromium browsers (Chrome, Edge) to execute web automation and diagnostic debugging. Instructions flow through five deterministic stages:

### 1. Decision Architecture

The runtime intake, state classification, evaluation, and execution tracking operate across a deterministic, five-stage pipeline:

```
[ AI Agent Browser Command ]
              │
              ▼
[ 1. Window & Tab Scoping (Agent Window vs Borrowed) ]
              │
              ▼
[ 2. DOM Extraction & Semantic VOM Synthesis ]
              │
              ▼
[ 3. Intent Resolution & Element Selection ]
              │
              ▼
[ 4. Precision Input Dispatch & Network Logging ]
              │
              ▼
[ 5. State Verification & Audit Ledger Commit ]
```

### 2. Decision Logic & Routing Formulations

When resolving candidate interactive elements from a complex webpage Visual Object Model (VOM) against user natural language intent, the engine calculates an element relevance score $S_{\text{element}}$:

$$S_{\text{element}} = w_t \cdot T_{\text{semantic}} + w_v \cdot V_{\text{visibility}} + w_p \cdot P_{\text{proximity}} + w_r \cdot R_{\text{role}}$$

Where:
- $T_{\text{semantic}} \in [0, 1]$: String similarity and embedding distance between element text/label/aria-label and target intent.
- $V_{\text{visibility}} \in \{0, 1\}$: Binary visibility in current viewport ($1$ if visible, $0.2$ if requires scrolling).
- $P_{\text{proximity}} \in [0, 1]$: Spatial proximity to previous interaction focus or container heading.
- $R_{\text{role}} \in [0, 1]$: Semantic role alignment (e.g., matching a button role for click intents or input role for type intents).
- Parameter weights: $w_t = 0.45$, $w_v = 0.20$, $w_p = 0.15$, $w_r = 0.20$ ($\sum w_i = 1.0$).

The element with $\arg\max_e S_{\text{element}}$ is selected for interaction. If the top score falls below $\tau_{\text{interact}} = 0.65$, interaction is deferred and diagnostic screenshots are captured.

### 3. Thresholding & Refusal Decision Criteria

BrowserSkill enforces strict operational boundaries and deterministic refusal thresholds:
- **Refusal on ERR_AUTH_CHALLENGE_DETECTED**: Webpage renders CAPTCHA, Cloudflare turnstile, or 2FA challenge halts execution with code `ERR_AUTH_CHALLENGE_DETECTED`.
- **Refusal on ERR_UNBORROWED_TAB_ACCESS**: Agent attempts interaction on user personal tab without explicit borrow grant halts execution with code `ERR_UNBORROWED_TAB_ACCESS`.
- **Refusal on ERR_ELEMENT_NOT_FOUND**: Element resolution score falls below threshold ($S_{\text{element}} < 0.65$) halts execution with code `ERR_ELEMENT_NOT_FOUND`.
- **Refusal on ERR_NETWORK_CAPTURE_OVERFLOW**: Captured network buffer size exceeds memory limit (> 100 MB) halts execution with code `ERR_NETWORK_CAPTURE_OVERFLOW`.
- **Refusal on ERR_EXTENSION_DISCONNECTED**: Chromium extension communication disconnected or daemon down halts execution with code `ERR_EXTENSION_DISCONNECTED`.

### 4. Fallback Decision Mechanism

Continuous operational stability is maintained through layered fault recovery:
- **Element Selector Fallback**: If a VOM numerical ID expires due to dynamic SPA page rerendering, the engine falls back to CSS selectors, XPath attributes, or coordinatebased text matching.
- **Tab Borrowing Fallback**: If borrowing a user tab fails due to permission denial, the agent clones the destination URL into a clean tab within the dedicated Agent Window.
- **Network Evidence Failover**: When response payloads cannot be captured due to streaming chunk encoding, DevTools console error logs and HTTP response status codes are captured as fallback evidence.
- **Model Fallback Cascade**: High-level reasoning and synthesis default to `gemini-2.0-flash` with automatic failover to `gpt-4o` and `claude-3-5-sonnet`.

### 5. Human-in-the-Loop Governance

Human operators retain sovereign authority over the multi-agent execution lifecycle:
- **Zero-Disruption Agent Window**: Automations run in a dedicated, visually isolated browser window, allowing users to continue concurrent personal browsing undisturbed.
- **Explicit Tab Borrowing & Return**: Agents can borrow an existing user tab only upon explicit command, releasing the lock as soon as the subtask finishes.
- **Human Takeover Protocol**: For identity verification and payments, the agent yields control to the human operator and waits for explicit user confirmation before resuming.

---

## The Data It Uses

BrowserSkill operates under strict principles of data minimization, environment isolation, and privacy protection.

### 1. Ingested Input Data

The framework processes only operational data necessary to perform its functions:
- **Agent Task Instructions**: Natural language web navigation, data extraction, form filling, and debugging goals.
- **Active Browser Telemetry**: DOM node trees, accessibility labels, computed styles, scroll offsets, and viewport dimensions.
- **Network Traffic & Console Streams**: HTTP request headers, query strings, API responses, status codes, and DevTools console logs.

### 2. Configuration & Reference Data

- **Visual Object Model (VOM) Cache**: Compact, indexed representations of interactive elements mapped to unique integer IDs.
- **Domain Whitelists & Browser Profiles**: Target Chromium profile directories and permitted URL scopes.
- **Network Diagnostic Rules**: Interception rules and mock replay definitions for website troubleshooting.

### 3. Base Model & Inference Lineage

- **Control Daemon**: Native Rust core daemon (`bsk-core`, `bsk-protocol`) coordinating WebSocket communication between CLI and browser.
- **Browser Extension**: Manifest V3 Chromium extension running inside Chrome or Microsoft Edge.
- **Agent Integrations**: Direct shell CLI (`bsk`), Claude Code hooks, and DeepSeek Harness (DSH) native plugins.

### 4. Data Privacy, Storage, and Retention

- **OWASP LLM & MITRE ATLAS Hardened**: Defended against indirect prompt injection, credential leakage, and unauthorized external API dispatch.
- **Local Environment Isolation**: Agent execution workspaces, intermediate scratchpads, and vector stores reside strictly within designated local project directories.
- **Automated Secret Scrubbing**: API keys, database credentials, and personal credentials are automatically redacted prior to embedding or logging.
- **Zero Commercial Monetization**: Prompts, intermediate reasoning trajectories, and task deliverables are never commercialized or shared with third parties.

---

## Limitations

Understanding the operational boundaries and technical constraints of BrowserSkill is essential for effective deployment.

### 1. Chromium Environment Dependency
- **Limitation**: BrowserSkill requires a Chromium-based browser (Chrome, Edge) with the BrowserSkill extension installed; Safari and Firefox are currently unsupported.
- **Mitigation**: Detect missing browser installations and provide clear one-line installation scripts (`install.ps1`, `install.sh`).

### 2. Canvas & WebGL Visual Interaction
- **Limitation**: Websites rendered entirely inside HTML5 `<canvas>` elements (e.g., WebGL games, complex charting apps) lack semantic DOM nodes.
- **Mitigation**: Fall back to coordinate-based pixel clicking paired with bounding box screenshot analysis.

### 3. Dynamic Single-Page App (SPA) Mutation Lag
- **Limitation**: Highly dynamic SPAs (React, Vue) re-rendering DOM nodes rapidly can cause temporary VOM ID invalidation.
- **Mitigation**: Implement automatic mutation observer debouncing, awaiting DOM stability before resolving interactive element targets.

### 4. Third-Party Anti-Bot Heuristics
- **Limitation**: Aggressive security firewalls may detect rapid synthetic keyboard or mouse inputs.
- **Mitigation**: Introduce randomized micro-delays (50–150ms) between sequential typing and clicking actions to simulate human interaction cadences.

### 5. Multi-Profile Session Ambiguity
- **Limitation**: When multiple browser profiles are open simultaneously, agents may connect to an unexpected profile if unspecified.
- **Mitigation**: Allow explicit profile targeting via the `--profile` CLI flag and verify current profile identity prior to task execution.

---

## Summary & Compliance Checklist

| Checkpoint 2 Requirement | Corresponding Section | Status |
| :--- | :--- | :---: |
| **How the agent decides** | [How the Agent Decides](#how-the-agent-decides) | **Covered** |
| - Decision architecture & 5-stage pipeline | Section 1 | Verified |
| - Decision logic & routing formulations | Section 2 | Verified |
| - Thresholding & refusal decision criteria | Section 3 | Verified |
| - Fallback decision mechanism | Section 4 | Verified |
| - Human-in-the-loop governance & oversight | Section 5 | Verified |
| **The data it uses** | [The Data It Uses](#the-data-it-uses) | **Covered** |
| - Ingested input data & query streams | Section 1 | Verified |
| - Configuration & reference schemas | Section 2 | Verified |
| - Base model lineage & deterministic engines | Section 3 | Verified |
| - Data privacy, retention lifecycle & MITRE/OWASP | Section 4 | Verified |
| **Its limitations** | [Limitations](#limitations) | **Covered** |
| - Chromium Environment Dependency | Section 1 | Verified |
| - Canvas & WebGL Visual Interaction | Section 2 | Verified |
| - Dynamic Single-Page App (SPA) Mutation Lag | Section 3 | Verified |
| - Third-Party Anti-Bot Heuristics | Section 4 | Verified |
| - Multi-Profile Session Ambiguity | Section 5 | Verified |
