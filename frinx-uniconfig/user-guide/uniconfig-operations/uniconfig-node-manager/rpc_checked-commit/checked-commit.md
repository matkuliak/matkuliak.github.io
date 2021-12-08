RPC checked-commit
==================

The trigger for execution of the checked configuration is RPC
checked-commit. A checked commit is similar to an RPC commit but it also
checks if nodes are in sync with the network before it starts
configuration. RPC fails if any node is out of sync. Output of the RPC
describes the result of the commit and matches all modified nodes in the
UniConfig transaction. If one node failed for any reason, RPC will fail
entirely.

In comparison to commit RPC, there is one additional phase between 'lock
and validate configured nodes' and 'write configuration into device'
phases:

1.  lock and validate configured nodes
2.  check if nodes are in-sync with state on devices
3.  write configuration into device
4.  validate configuration
5.  confirmed commit
6.  confirming commit (submit configuration)

Following diagram captures check if configuration fingerprints in the
transaction datastore and device are equal.

![RPC checked-commit](RPC_checked-commit-RPC_checked_commit.svg)

> **note**
>
> There is a difference between fingerprint-based validation in the
> phases 1 and 2. The goal of the first phase is validation if other
> transaction has already changed the same node by comparison of
> fingerprint in the UniConfig transaction and in the database. On the
> other side, the second phase validates if fingerprint in the
> transaction equals to fingerprint on the device - if another system /
> directly user via CLI has updated device configuration since the
> beginning of the transaction.

RPC Examples
------------

### Successful Example

RPC checked-commit input has 2 target nodes and the output describes the
result of the checked-commit.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Successful Example

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

RPC checked-commit input has 2 target nodes. One of them failed on
configuration (IOSXRN). The output describes the result of the
checked-commit.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

The RPC checked-commit input has 2 target nodes. One of them has failed
on the changed fingerprint (IOSXRN) and the other has a bad
configuration (IOSXR). The output describes the result of the
checked-commit.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

The RPC checked-commit input has 3 target nodes. One of them was deleted
so it is missing in the Config Datastore(IOSXR), the other has not been
mounted yet (AAA). The output describes the result of the
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
