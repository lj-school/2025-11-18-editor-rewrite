# Knowledge Base for Rewriting the Editor

## Current Status

- [Serlo Editor](https://serlo.org/editor) has been developed over five years as a visual editor for interactive educational content.
- Supports structured content (e.g., exercises with question + solution, interactive videos) unlike generic rich‑text editors.
- Combines freeform editing (Google Docs–like) with structured data handling (H5P‑like).
- Internally flattens JSON and applies lens‑like transformations for efficient updates.
- Rich‑text functionality inside components is handled through Slate.

### Advantages (USPs)

- Prevents invalid content states (e.g., solution without exercise).
- More fluid and intuitive than form‑based tools like H5P.
- New components are easy to build; clear, modular abstraction.
- Offers WYSIWYG usability while preserving structured document integrity.

### Limitations

- Core architecture is complex and fully custom, making onboarding and iteration slow.
- No global selection: selection is locked inside contenteditable regions; multi‑element operations are impossible.

## Motivation for a Rewrite

- Simplify and modernize the underlying architecture to accelerate development.
- Enable full‑document selection and operations across components.
- Reduce framework complexity so focus can shift toward improving authoring workflows for educational content.
