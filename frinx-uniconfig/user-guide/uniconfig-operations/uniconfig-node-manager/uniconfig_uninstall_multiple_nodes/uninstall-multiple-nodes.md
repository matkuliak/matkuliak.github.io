RPC uninstall-multiple-nodes
============================

This RPC uninstalls multiple devices at once. It uses the default
uninstall-node RPC. Devices are uninstalled in parallel.

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

RPC input contains devices (R1 and R2) and R2 is installed on two
different protocols.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Successful example

RPC input contains two devices (R1 and R2) and R2 is already uninstalled
on CLI protocol.

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
