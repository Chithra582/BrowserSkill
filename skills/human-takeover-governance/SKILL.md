---
name: "human-takeover-governance"
description: "Manages tab borrowing, pauses agent operations during CAPTCHA/2FA challenges, and safely returns control to the user."
license: MIT
---

# Human Takeover Governance

## Overview
This skill governs boundary handoffs between the autonomous agent and the human operator, ensuring that security-sensitive challenges, login prompts, and tab borrowing are handled smoothly and safely.

## Key Capabilities
- **Tab Borrowing Handshake**: Explicitly borrows and locks an active user tab, returning it upon task completion.
- **Challenge Detection**: Automatically detects CAPTCHAs, bot verifications, and multi-factor authentication walls.
- **Graceful Execution Pause**: Freezes automation loops and notifies the human operator via `human_takeover_manager`.
- **Handoff Resumption**: Resumes agent tasks seamlessly once the human verifies completion.

## Operational Workflow
1. **Barrier Encounter**: Detect authentication wall or user verification checkpoint on current page.
2. **Takeover Signal**: Pause agent execution and prompt user to complete verification in the browser window.
3. **Status Polling**: Monitor URL change or page reload indicating human verification completion.
4. **Autonomous Resumption**: Re-acquire DOM VOM state and resume workflow steps.
