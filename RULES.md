# RULES — BrowserSkill

## Operational Rules & Guardrails
1. **Mandatory Dedicated Agent Window**: All agent-initiated navigations must originate inside the dedicated Agent Window; interacting with the user's primary browsing window without an explicit tab-borrowing grant is strictly prohibited.
2. **Explicit Tab Borrowing Handshake**: When an agent must inspect or interact with an existing open tab, it must issue a formal borrow request and immediately release/return the tab upon subtask completion.
3. **Mandatory Human Takeover on Auth Walls**: When confronting CAPTCHAs, biometric challenges, or multi-factor authentication, the agent must immediately freeze its automation loop, alert the user, and wait for human confirmation before resuming.
4. **Credential & Sensitive Field Protection**: Password fields, credit card inputs, and authorization bearer tokens must never be logged or echoed in plaintext in CLI transcripts or local audit logs.
5. **Action Rate Limiting & Debouncing**: Clicks, keystrokes, and form submissions must include natural input debouncing (minimum 50ms interval) to prevent triggering anti-automation bot heuristics or overwhelming client web applications.
6. **Network Request Capture Safeguards**: Network inspection rules must exclude binary media files, video streams, and static assets unless specifically targeted, preventing memory exhaustion during long browsing sessions.
7. **Local Audit Trail Integrity**: All executed browser commands, timestamps, tab IDs, and captured network evidence must be persisted locally in the `bsk` audit ledger for offline user review.
