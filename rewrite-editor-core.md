## Main idea

### End goal

The editor core should be a WYSIWYG editor for JSON structures. Unlike modern
editor frameworks like Lexical, ProseMirror, or Slate, it also supports plain
objects with ordered properties (the order of their properties is the order you
see in the editor). You can intuitively update or change any node in the JSON,
much like using functional lenses for direct, targeted edits.

### Node type model

Each document element belongs to a spefic node type. These node types act as
configurable schemas or classes, specifying structure, constraints, and behavior
of the corresponding nodes. For example there can be a node type describing a
multiple choice exercise as an object with the properties `question` and
`answers`:

```ts
const MultipleChoiceNodeType = Schema.object({
  // Definition of the shape
  schema: {
    question: TextContentNodeType,
    answers: Schema.array({
      schema: Schema.object({
        schema: {
          isCorrect: BooleanNodeType,
          answer: TextNodeType,
        },
        propertyOrder: ['isCorrect', 'answer'],
      }),
      minLength: 2
    }),
  },

  // Definition of other properties
  propertyOrder: ["question", "answers"],

  // Optional definition how this element shall be rendered
  render() { ... }
})
```

This is similar how the schema of an document is defined in
[zod](https://zod.dev/) or [io-ts](https://gcanti.github.io/io-ts/).

### Flat internal structure

To support efficient transformations deep inside the JSON structure, the
internal representation of the document is flat. Instead of a nested tree all
node are stored in a single map.

For example, the following nested JSON:

```json
{
  "type": "exercise",
  "exercise": [
    { "type": "pargaph", "content": "What is the capital of France?" }
  ],
  "solution": [
    { "type": "paragraph", "content": "The capital of France is Paris." }
  ]
}
```

Would be represented internally as:

```json
{
  "root": { "type": "exercise", "exercise": "1", "solution": "3" },
  "1": "2",
  "2": "What is the capital of France?",
  "3": "4",
  "4": "The capital of France is Paris."
}
```

For example changing the solution inside the JSON tree can be represented as the
transformation:

```typescript
{
  type: "insertText",
  nodeId: "4",
  offset: 27,
  text: " Paris is the city of lights."
}
```

Due to this architectual decision we can distinguish between two types of nodes:

- `TreeNodes`: The content is stored in a hierarchy of nodes like JSON – this
  representation is best for exchanging the content with external systems (like
  when saving /loading or when content is copied / pasted).
- `FlatNodes`: This is the internal representation of the document. A flat node
  has a unique id and can reference other flat nodes by their id.

## Open Questions

### FP-style or OO-style API

We have two dimensions in which the framework can grow: On the one hand we have
different schema types for node classes (e.g. text nodes, array nodes, union
nodes etc.) and on the other hand we have different operations that can be
performed on these nodes (e.g. insert text, select end or beginning, split node,
merge nodes etc.).

We can either have an OO-style in which each node type contains the methods /
operations for this node type:

```typescript
interface NodeType {
  insertText(node: FlatNode, offset: number, text: string): void;
  merge(node: FlatNode, withNode: TreeNode): void;
  // ...
}

// schema/text.ts

const TextNodeType: NodeType = {
  insertText(node, offset, text) { ... },
  merge(node, withNode) { ... },
  // ...
}

// schema/array.ts

function createArrayNodeType(itemType: NodeType): NodeType {
  return {
    insertText(node, offset, text) { ... },
    merge(node, withNode) { ... },
    // ...
  }
}
```

Or we can have a FP-style in which the operations are defined as functions that
take the node type as an argument:

```typescript
interface NodeType {
  // schema definition only
}

interface FlatNode {
  id: string;
  type: NodeType;
  // ...
}

interface TreeNode {
  type: NodeType
  // ...
}

// operations/text.ts

function insertText(
  nodeType: NodeType,
  node: FlatNode,
  offset: number,
  text: string
): void {
  ...
}

// operations/merge.ts

function merge(
  nodeType: NodeType,
  node: FlatNode,
  withNode: TreeNode
): void {
  ...
}
```

**_Open question:_** Which style is better suited for extensibility,
maintainability, and usability of the editor core?

### Notes

- The FP-style seems to be best for the beginning since in developing the
  prototypes we start with a fixed set of node types but need to add / change
  operations frequently.
- The OO-style might be better for actually developing the editor core since by
  then the set of operations might be more stable and we can focus on
  extensibility of node types.

## Collaboration

Collaboration can be either implemented by using operational transformation (OT)
or conflict-free replicated data types (CRDTs). However, there seem to be better
library support for CRDTs than for OT.

### CRDT Libraries

- [Yjs](https://yjs.dev/) for general-purpose CRDTs.
  - Integrations for ProseMirror, TipTap, CodeMirror, Quill, and others.
- [Loro](htts://loro.dev/) – seems to be a newer alternative to Yjs.

### OT Libraries

- [OT types](https://github.com/ottypes)
  - [json1](https://github.com/ottypes/json1) for JSON documents.
  - [rich-text](https://github.com/ottypes/rich-text) for rich text documents
    (Quill Delta format).
- [ShareDB](https://share.github.io/sharedb/) for central server architecture.

##### Notes

- API seems to be similar to CRDT libraries so that switching between OT and
  CRDT might be possible. However, editor bindings for ProseMirror & Co might be
  missing.
- Libraries seem to be less actively maintained than CRDT libraries.
