---
name: "logged-in-browser-automation"
description: "Drives logged-in browser sessions in dedicated Agent Windows, executing clicks, form fills, and navigation."
license: MIT
---

# Logged-In Browser Automation

## Overview
This skill connects AI agents directly into the user's active Chromium browser (Chrome, Edge), automating web navigation and form interactions using existing login sessions without headless credential re-authentication.

## Key Capabilities
- **Dedicated Agent Window**: Launches automated tasks in a separate, isolated browser window preserving user focus.
- **Tab Lifecycle Control**: Creates, navigates, reloads, and terminates tabs via `browser_tab_controller`.
- **Form Automation**: Fills complex multi-field inputs, handles datepickers, and triggers custom select dropdowns.
- **Session Continuity**: Retains cookies, localStorage, and authentication tokens across task steps.

## Operational Workflow
1. **Window Setup**: Open or focus dedicated Agent Window.
2. **Page Navigation**: Navigate to target URL and await network idle state.
3. **Action Execution**: Execute click, scroll, or input sequences via `dom_interactor`.
4. **State Verification**: Confirm URL changes or DOM mutations before proceeding.
