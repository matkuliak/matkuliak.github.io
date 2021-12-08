RPC copy-one-to-many
====================

RPC input contains:

-   type of operation - 'merge' or 'replace',
-   type of source datastore - CONFIGURATION / OPERATIONAL,
-   type of target datastore - CONFIGURATION / OPERATIONAL,
-   source path in RFC-8040 URI formatting,

\* list of target paths in RFC-8040 URI formatting (target paths denote
parent entities under which configuration is copied).

Target datastore is optional input field. By default, it is the same as
source datastore. Other input fields are mandatory, so it is forbidden
to call RPC with missing mandatory field. Output of RPC describes result
of copy to target paths RPC. If one path failed for any reason, RPC will
be failed overall and no modification will be done to datastore - all
modifications are done in the single atomic transaction.

Description of RPC copy-one-to-many is on figure below.

![RPC copy-one-to-many](copy-one-to-many.svg)

RPC Examples
------------

### Successful example

The following example demonstrates merging of ethernet interface
configuration from single source into interfaces 'eth-0/2' (node
'dev02'), 'eth-0/3' (node 'dev02'), 'eth-0/100' (node 'dev03'), and
'eth-0/200' (node 'dev03').

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed example

The next example shows failed copy-one-to-many RPC - both target paths
are invalid since 'ext' list schema nodes doesn't contain
'interfaces:interfaces' child container.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**
