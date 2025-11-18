# Knowledge Base for Rewriting the Editor

## Current Status

- [Serlo Editor](https://serlo.org/editor) has been developed over five years as
  a visual editor for interactive educational content.
- Supports structured content (e.g., exercises with question + solution,
  interactive videos) unlike generic rich‑text editors.
- Combines freeform editing (Google Docs–like) with structured data handling
  (H5P‑like).
- Internally flattens JSON and applies lens‑like transformations for efficient
  updates.
- Rich‑text functionality inside components is handled through Slate.

### Deeper dive into the editor

- [Presentation: Introduction to the Serlo Editor for devs](https://docs.google.com/presentation/d/1gww-KXR9VB8ujCGBGKSP4fgfyl-cqKww1-1b5PyxxN0/edit?slide=id.g18f5599da96_0_100#slide=id.g18f5599da96_0_100)
- [The content format of the Serlo Editor](https://github.com/serlo/documentation/wiki/Content-format)

### Advantages (USPs)

- Prevents invalid content states (e.g., solution without exercise).
- More fluid and intuitive than form‑based tools like H5P.
- New components are easy to build; clear, modular abstraction.
- Offers WYSIWYG usability while preserving structured document integrity.

### Limitations

- Core architecture is complex and fully custom, making onboarding and iteration
  slow.
- No global selection: selection is locked inside contenteditable regions;
  multi‑element operations are impossible.

## Motivation for a Rewrite

- Simplify and modernize the underlying architecture to accelerate development.
- Enable full‑document selection and operations across components.
- Reduce framework complexity so focus can shift toward improving authoring
  workflows for educational content.
- Enable advanced features like real‑time collaboration and Google Docs–style
  comments.

## Possible Solutions

- **Using Modern Editor Frameworks:** Evaluate newer editor frameworks that
  emerged since the initial development and replace the current editor core with
  one of them. So we reduce long-term maintenance burden and benefit from
  community-driven improvements.
- **Rewriting the Existing Architecture:** Keep the core idea of a custom,
  structured JSON-based editor but redesign it with a simplified and easier to
  maintain architecture.

## Using modern editor frameworks

### Overview of modern editor Frameworks

- [Lexical](https://lexical.dev/)
- [ProseMirror](https://prosemirror.net/)
  - [Tiptap](https://tiptap.dev/)
  - [Remirror](https://remirror.io/)
  - [Prosekit](https://prosekit.com/)
- [Slate](https://docs.slatejs.org/)

## Introduction to Modern Editor Frameworks

Modern editor frameworks generally model documents as nested lists. This
structure simplifies operations like splitting nodes, managing selections, and
maintaining order (e.g., identifying elements before/after a position). However,
structured educational content often requires ordered objects (e.g., exercise =
[question, solution]), not just lists.

To integrate structured elements into such list-based frameworks, two main
approaches exist:

### 1. Using Atomic Nodes

- Many editors support atomic nodes (Lexical: _DecoratorNode_, ProseMirror:
  _atom nodes_).
- The structured component is treated as an opaque unit in the editor’s data
  model and the editor framework handles it as a single node without inspecting
  its internal structure. Often a React component can be used.
- Rendering and internal logic happen inside this custom node

### 2. Enforcing Structured Tuples in the Document Model

- Implement structured elements directly within the list-based model.
- Maintain tuple-like structures (e.g., exercise = [question, solution]) using
  schema constraints or normalizations.
- Examples:
  - ProseMirror: document schema can encode tuple constraints.
  - Lexical: add node transformations to maintain structure.
  - Slate: normalizations repair or complete the expected structure after edits.
