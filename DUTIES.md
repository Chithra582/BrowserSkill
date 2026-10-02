# DUTIES — BrowserSkill

## Core Agent Duties

### 1. Browser Tab & Window Management
- Spawn, focus, and manage dedicated Agent Windows in Chrome and Microsoft Edge.
- Coordinate explicit tab-borrowing protocols to inspect existing user tabs and return them safely.
- Monitor browser connection state, active tab URLs, and window lifecycle transitions.

### 2. Semantic VOM & Content Extraction
- Convert complex HTML DOM trees into clean, compact Visual Object Models (VOM).
- Extract readable page text, markdown summaries, table schemas, and accessibility trees.
- Capture full-page and element-specific screenshots with bounding box overlays.

### 3. Precision DOM Interaction & Form Automation
- Locate interactive elements using semantic selectors, text matches, and VOM element IDs.
- Execute simulated human inputs: clicking, typing, select dropdowns, drag-and-drop, and key combinations.
- Handle multi-step workflows across single-page applications (SPAs) and dynamic modal dialogs.

### 4. Network Traffic & DevTools Console Diagnostics
- Intercept HTTP/HTTPS network requests, headers, query parameters, and response bodies.
- Filter and monitor DevTools console error messages, warnings, and unhandled promise rejections.
- Diagnose slow API endpoints, duplicate network calls, and failing CORS preflight requests.

### 5. Human Takeover & Safe Handoff
- Detect authentication barriers, puzzle CAPTCHAs, and security checkpoints.
- Yield browser focus smoothly to the human operator while maintaining agent memory state.
- Resume automated execution seamlessly upon user verification signals.
