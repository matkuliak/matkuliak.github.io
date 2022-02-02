---
order: 7
---

# Translation Units in general

## Module structure

Translation unit is a self contained project which implements a mapping
between OpenConfig based YANG models and device specific configuration.
It is used by the FRINX ODL to perform translation between device
specific configuration model and standard (OpenConfig) models. A unit
usually consists of:

- Handlers
    - Readers
    - Writers
- TranslateUnit implementation
- RPCs

## Handlers

Each complex node in YANG (container, list, augment...) should have a
dedicated handler (Reader, Writer)

- This enables extensibility, readability and the framework can easily
    filter and process the data this way
- Unless there is a need to also handle child nodes, in which case
    register the handler using **subtreeAdd** method from the registries

There are 2 types of handlers: Readers (Read operation) and Writers
(Create, Update, Delete operation)

One can implement just the readers or both readers and writers for YANG
models. Writers must have counterpart readers because of reconciliation.

Readers and Writers should use the **InstanceIdentifier** parameter they
receive in **readCurrentAttributes** or **writeCurrentAttributes** methods
to find information about keys for their parent nodes. E.g. Reader
registered under ID: /interfaces/interface/config will always receive
keyed version of that ID: /interface/interface[Loopback0]/config. So it
can use method **firstKeyOf** on **InstanceIdentifier** to get the keys.

**RWUtils** class contains methods for **InstanceIdentifier** manipulation.

Readers and writers can be easily tested and it is necessary to provide
unit tests for all of them. It's important to cover
**readCurrentAttributes** and **writeCurrentAttributes** with all possible
scenarios (all data there, no data there, partial data there...)

Writers may use **Preconditions.checkArgument()** before accessing the
device. Fail of the precondition check does not invoke default rollback
(opposite operation) on the writer where precondition is located.

## Base Handlers

When a handler for the same YANG node is implemented to conform various
devices, it tends to lead to a lot of boilerplate and duplicate code.
Therefore, we should implement a **base handler** for such handlers. How
does it work:

- create a base-project (if there isn't any) to group base handlers
    (e.g. for an interface handler, choose interface-baseproject)
- each base handler needs to be abstract and implement same interfaces
    as the original handler
- extract common functionality in the base handler. Common
    functionality means that it will conform the majority of the
    original handlers. If a handler does not share the extracted
    functionality, it needs to override original interface methods, to
    **hide** the extracted functionality.
- let original handlers extend base abstract handler
