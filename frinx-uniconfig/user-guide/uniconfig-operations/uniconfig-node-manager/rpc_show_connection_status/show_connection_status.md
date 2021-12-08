RPC show-connection-status
==========================

This RPC verifies the connection status of the UniConfig nodes. The RPC
input contains a list of UniConfig nodes which connection status should
be verified. Output of the RPC describes the connection status of the
UniConfig nodes on the 'southbound-layer' and 'unified-layer'. Each
layer contains a 'connection-status' and 'status-message'. The
southbound layer contains an additional parameter called 'protocol'.

![RPC show-connection-status](RPC_show-connection-status-RPC_show_connection_status.svg)

RPC Examples
------------

### CLI Device Example

The RPC show-connection-status input has one target node and the RPC
output contains the result of the operation.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Netconf Device Example

RPC show-connection-status input has one target node and the RPC output
contains the result of the operation.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**
