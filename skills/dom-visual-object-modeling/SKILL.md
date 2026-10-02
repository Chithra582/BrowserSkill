---
name: "dom-visual-object-modeling"
description: "Scans page structure, interactive controls, text segments, and visual element bounds via VOM (Visual Object Model)."
license: MIT
---

# DOM Visual Object Modeling

## Overview
This skill parses active webpage DOM hierarchies into compact, semantic Visual Object Models (VOM), giving agents structured spatial awareness of interactive buttons, links, inputs, and text blocks.

## Key Capabilities
- **Noise Reduction**: Strips redundant HTML tags, hidden elements, and style clutter to minimize LLM token overhead.
- **Interactive Element Indexing**: Assigns persistent numerical VOM IDs to actionable buttons, inputs, and links.
- **Bounding Box Geometry**: Computes visual viewport coordinates and scroll visibility boundaries.
- **Screenshot Evidence**: Captures full-page or element-cropped screenshots with numbered overlay markers.

## Operational Workflow
1. **DOM Tree Traversal**: Scan current page layout via `page_content_extractor`.
2. **VOM Compilation**: Filter non-visible elements and synthesize compact semantic object representation.
3. **Target Element Resolution**: Map natural language intents to corresponding VOM element IDs.
4. **Visual Grounding**: Optionally cross-reference element bounds with high-resolution screenshot captures.
