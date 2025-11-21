## Main idea

### End goal

The editor core should be a WYSIWYG editor for JSON structures. Unlike modern
editor frameworks like Lexical, ProseMirror, or Slate, it also supports plain
objects with ordered properties (the order of their properties is the order you
see in the editor). You can intuitively update or change any node
in the JSON, much like using functional lenses for direct, targeted edits.

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
