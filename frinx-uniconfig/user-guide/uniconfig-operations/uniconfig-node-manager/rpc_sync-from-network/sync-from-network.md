RPC sync-from-network
=====================

The purpose of this RPC is to synchronize configuration from network
devices to the UniConfig nodes in the Operational datastore of UniConfig
transaction. The RPC input contains a list of the UniConfig nodes where
the configuration should be refreshed within the network. Output of the
RPC describes the result of sync-from-network and matches all input
nodes. Calling RPC with empty list of target nodes results in syncing
configuration of all nodes that have been modified in the UniConfig
transaction. If one node failed for any reason, the RPC will fail
entirely.

![RPC sync-from-network](RPC_sync-from-network-RPC_sync_from_network.svg)

RPC Examples
------------

### Successful Example

RPC input contains nodes where configuration should be refreshed.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

Successful Example ++++++++++++++

RPC input does not contain the target nodes, all touched nodes will be
invoked.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC input contains a list of nodes where the configuration should be
refreshed. One node has not been mounted yet (AAA).

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
