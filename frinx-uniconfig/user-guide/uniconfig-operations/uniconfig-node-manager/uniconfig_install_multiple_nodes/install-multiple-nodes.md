RPC install-multiple-nodes
==========================

This RPC installs multiple devices at once. It uses the default
install-node RPC. Devices are installed in parallel.

RPC Examples
------------

### Successful example

RPC input contains two devices (R1 and R2).

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Successful example

RPC input contains devices (R1 and R2) and R2 uses two different
protocols.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Successful example

RPC input contains two devices (R1 and R2) and R2 is already installed
using CLI protocol.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC input doesn't specify node-id.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *
