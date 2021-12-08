RPC calculate-subtree-diff
==========================

This RPC creates a diff between the actual topology subtrees and
intended topology subtrees. Nodes could be from different subtrees, it
compares only the data hierarchy and values. RPC input contains
data-tree paths ('source-path' and 'target-path') and data locations
('source-datastore' and 'target-datastore'). Data location is the
enumeration of two possible values 'OPERATIONAL' and 'CONFIGURATION'.
Output of the RPC describes the status of the operation and a list of
statements representing the diff between two subtrees.

![RPC calculate-subtree-dif](RPC_calculate_subtree_diff-RPC_calculate_subtree_diff.svg)

RPC Examples
------------

### Successful example

RPC calculate-subtree-diff input has a path to two different testtool
models in the operation memory. Output contains a list of statements
representing the diff.

> **Node testtool with schema test-module**
>
> **Node testtool with schema test-module:**

> **Node testtool2 with schema test-module-mod**
>
> **Node testtool2 with schema test-module-mod:**

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC calculate-subtree-diff has an improperly defined datastore (AAA)
within the input. Output describes the Allowed values [CONFIGURATION,
OPERATIONAL].

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC input does not contain source node YIID, so the RPC can not be
executed.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**
