RPC create-snapshot
===================

RPC creates a snapshot from the nodes in UniConfig topology. Later, this
snapshot can be used for manual rollback. RPC input contains the name of
the snapshot topology and nodes that the snapshot will contain. Output
of the RPC describes the result of operation and matches all input
nodes. You cannot call an RPC with empty target-nodes. If one node
failed for any reason, RPC will be fail entirely.

![RPC create-snapshot](RPC_create-snapshot-RPC_create_snapshot.svg)

RPC Examples
------------

### Successful Example

RPC input contains the name for the topology snapshot and nodes that the
snapshot contains. RPC output contains the result of operation.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC input contains the name for the topology snapshot and nodes that
will be contained in the snapshot. You cannot call an RPC with empty
target-nodes. If one node failed for any reason, RPC will be fail
entirely.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed example

The RPC input includes nodes that will be contained in the snapshot, but
a snapshot name is missing. RPC output contains the result of the
operation.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC input contains a name for the topology snapshot and a node that will
be contained in the snapshot. One has not been mounted yet. RPC output
contains the result of the operation.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC input does not contain the target nodes, so the RPC can not be
executed.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**
