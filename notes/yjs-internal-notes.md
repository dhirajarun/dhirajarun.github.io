---
title: Yjs internal notes
category: notes
draft: true
---

- Document is the collection of types.
- type inclue
  - Array
  - Map
  - Text
  - XMLELEMENT


- All of the content inside is represented in linked list the same way not matter the type of data, types are view layer for the user to convert the linked-list structure into the respective format.

AbstractType
- every type implement this, this is baically the implemenation.
- _start: Item = start of the type
- _map = Map of item to store related Item
- _item = Related Item, most of the time this is parent

Item
- the linked list structure containing content
- the left, right and the order here is the document order, like abcdef
- origin - initial left Item
- rightOrigin

- inside item each content has a unique id, if a item contain content `abcd`, the first id let say is {client: A, clock: 2}, the first id refer to `a`, the id {client: A, clock: 4} refe to `c` and so on. next item for that client will have `id.clock` to be 6.

- the first item added by some client, will be given id {client: A, clock: 0}
- some more content added by same client, will be inserted in the last item it was created by the same client

- if new item added, between one item{client: B, clock: x} after 5 character, the item before will be {client: A, clock: x + 5}, item right will be {client: A, clock x + 5 + length}

## Relative Position

createRelativePositionFromTypeIndex(type: AbstractType, index: number)
createRelativePosition(type, item: ID | null, assoc: number)
createID(t.id.client, t.id.clock + index)

AbstractType._start
Item.left
Item.right
Item.deleted


if type._item === null, it means it is the Root type.

typeId = createID (client, clock)
tname  = findRootTypeKey(type) // name of the key by which type was created

new RelativePosition(typeId, tname, item, )
- contains type, tname, item = ID, assoc


## YsyncPlugin

- new ProseMirrorBinding(yXmlFragment, mapping)
- snapshot
- addToHistory
- colorMapping
- colors
- restore
- createMutex

