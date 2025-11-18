# Using modern editor frameworks

## Overview of modern editor Frameworks

- [Lexical](https://lexical.dev/)
- [ProseMirror](https://prosemirror.net/)
  - [Tiptap](https://tiptap.dev/)
  - [Remirror](https://remirror.io/)
  - [Prosekit](https://prosekit.com/)
- [Slate](https://docs.slatejs.org/)

## Overview of approaches to structured content

Modern editor frameworks generally model documents as nested lists. This
structure simplifies operations like splitting nodes, managing selections, and
maintaining order (e.g., identifying elements before/after a position). However,
structured educational content often requires ordered objects (e.g.,
`exercise = [question, solution]`), not just lists.

To integrate structured elements into such list-based frameworks, two main
approaches exist:

### 1. Using Atomic Nodes

- Many editors support atomic nodes (Lexical:
  [`DecoratorNode`](https://lexical.dev/docs/concepts/nodes#decoratornode),
  ProseMirror:
  [atomic nodes](https://prosemirror.net/docs/ref/#model.NodeSpec.atom)).
- The structured component is treated as an opaque unit in the editor’s data
  model and the editor framework handles it as a single node without inspecting
  its internal structure. Often a React component can be used.
- Rendering and internal logic happen inside this custom node

### 2. Enforcing Structured Tuples in the Document Model

- Implement structured elements directly within the list-based model.
- Maintain tuple-like structures (e.g., `exercise = [question, solution]`) using
  schema constraints or normalizations.
- Examples:
  - ProseMirror: document schema can encode tuple constraints.
  - Lexical: add node transformations to maintain structure.
  - Slate: normalizations repair or complete the expected structure after edits.
