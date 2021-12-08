RPC replace-config-with-operational
===================================

RPC replaces the UniConfig topology nodes in the Config datastore with
UniConfig topology nodes from the Operational datastore. The RPC input
contains a list of the UniConfig nodes to replace from the Operational
to the Config datastore of the UniConfig transaction. Output of the RPC
describes the result of the operation and matches all input nodes. If
RPC is invoked with empty list of target nodes, operation will be
invoked for all nodes modified in the UniConfig transaction. If one node
failed for any reason, RPC will fail entirely.

![RPC replace-config-with-operational](RPC_replace-config-with-operational-RPC_replace_config_with_operational.svg)

RPC Examples
------------

### Successful Example

RPC replace-config-with-operational input has 2 target nodes and the RPC
output contains the result of the operation.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

Successful Example ++++++++++++++

The RPC input does not contain the target nodes, all touched nodes will
be invoked.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC input contains a list of the target nodes. One node has not been
mounted yet (AAA). The RPC output contains the result of the operation.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

If the RPC input does not contain the target nodes and there weren't any
touched nodes, the request will result in an error.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**
