RPC calculate-diff
==================

This RPC creates a diff between the actual UniConfig topology nodes and
the intended UniConfig topology nodes. The RPC input contains a list of
UniConfig nodes to calculate the diff. Output of the RPC contains a list
of statements representing the diff after the commit. It also matches
all input nodes. If RPC is called with empty list of target nodes, diff
is calculated for each modified node in the UniConfig transaction. If
one node failed for any reason, the RPC will fail entirely.

![RPC calculate-diff](RPC_calculate-diff-RPC_calculate_diff.svg)

RPC Examples
------------

### Successful Example

The RPC calculate-diff input has two target nodes and the output
contains a list of statements representing the diff.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

Successful Example ++++++++++++++

If the RPC input does not contain the target nodes, all touched nodes
will be invoked.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

The RPC calculate-diff input has two target nodes. One of which has not
been mounted yet (AAA), the output describes the result of the
checked-commit.

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
