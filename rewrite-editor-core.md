## Main idea

### End goal

The editor core should be A WYSIWYG editor for JSON structures. In comparision
to modern editor frameworks like Lexical, ProseMirror or Slate also plain
objects (concrete: plain objects with ordered properties) are supported. The
editor core supports changing / updating nodes inside the JSON structure – like
[functional lenses](https://medium.com/@dtipson/functional-lenses-d1aba9e52254)

### Flat internal structure

In order to support transformations deep inside the JSON structure, the internal
representation of the document should be flat. For example instead of having the
following JSON as internal state:

```json
{ "type": "document", children: [{
  "type": "paragraph",
  "children": [
    {
      "type": "text",
      "text": "Hello, world!"
    }
  ]
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
