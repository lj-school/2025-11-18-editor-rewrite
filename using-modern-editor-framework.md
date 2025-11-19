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

## Variant: Using atomic nodes

### General throughts

- Actually this solution is similar to the current Serlo Editor where Slate is
  used for rich-text. However we replace in this variant the custom core with a
  modern editor framework so that we reduce maintenance burden and benefit from
  community-driven improvements.

### Prototype with Lexical

- Repo: https://github.com/elbotho/serlo-editor-lexical
- Demo: https://serlo-lexical.vercel.app/

#### Advantages

- Solution seems to be scalable to the current Serlo Editor complexity.
- Selection across a decator node is possible.

#### Limitations

- Selections still are limited. It is hard to create a secltion accross a
  decorator node. For example, when a selection starts inside a decorator node,
  the selection is limited to the boundary of the decorator node (like currently
  with Slate).

## Variant: Enforcing structured tuples in the document Model

### Prototype with Lexical

- Repo:
  https://github.com/kulla/2025-11-08-lexical-with-multiple-choice-exercise
- Demo:
  https://kulla.github.io/2025-11-08-lexical-with-multiple-choice-exercise/

#### Findings

- No other way than using node transformations to enforce the structure seems to
  be possible, see
  [this discussion](https://github.com/facebook/lexical/discussions/7750)

#### Limitations

- Implementing all necessary normalizations / node transformations is complex,
  especially for nested structures (see
  https://github.com/kulla/2025-11-08-lexical-with-multiple-choice-exercise/blob/a265396fc171ecd5078cff2b5a9de9c337df4276/src/plugins/exercise.tsx#L339-L589).
  Helpers are needed to simplify this task.
- Uneditable content is hard to implement in Lexical (currently CSS with
  `:before` and `content: "xyz"` is used as a workaround)

### Prototype with Prosekit (Prosemirror based)

In this prototype structured tuples are enforced in the document model / schema
of Prosemirror.

- Repo:
  https://github.com/kulla/2025-11-15-prosekit-with-multiple-choice-exercise
- Demo:
  https://kulla.github.io/2025-11-15-prosekit-with-multiple-choice-exercise/

#### Advantages

- Compared to Lexical the implementation of the necessary schema constraints and
  normalizations is simpler.

#### Limitations

- The UX is quite bad when content is copied / pasted. Also many wanted
  operations (like pressing Enter on multiple choice answers) does not work out
  of the box.
