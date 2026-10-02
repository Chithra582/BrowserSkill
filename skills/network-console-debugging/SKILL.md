---
name: "network-console-debugging"
description: "Intercepts HTTP requests, inspects API response bodies, captures console errors, and diagnoses performance bottlenecks."
license: MIT
---

# Network and Console Debugging

## Overview
This skill provides comprehensive web debugging capabilities, intercepting network traffic, capturing DevTools console diagnostics, and recording request-response cycles to diagnose web application issues with concrete evidence.

## Key Capabilities
- **Traffic Interception**: Intercepts HTTP/HTTPS requests, headers, query parameters, and response bodies in real time.
- **Console Log Monitoring**: Captures `console.log`, `console.warn`, and uncaught JavaScript runtime exceptions.
- **Duplicate Request Detection**: Flags redundant or cyclical API queries causing server load or UI freezes.
- **Request Replay & Rules**: Replays requests with modified headers or applies mock rules to test debugging hypotheses.

## Operational Workflow
1. **Network Listener Activation**: Attach listener to active browser tab via `network_traffic_inspector`.
2. **Action Triggering**: Perform user or agent web actions while capturing traffic.
3. **Evidence Extraction**: Filter failing HTTP requests (4xx/5xx codes) and JavaScript console tracebacks.
4. **Diagnostic Reporting**: Compile structured JSON evidence logs detailing root-cause failures.
