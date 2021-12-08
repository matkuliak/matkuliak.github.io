RPC validate
============

The external application stores the intended configuration under nodes
in the UniConfig topology. The configuration can be validated if it is
valid or not. The trigger for execution of configuration validation is
an RPC validate. RPC input contains a list of UniConfig nodes which
configuration should be validated. Output of the RPC describes the
result of the validation and matches all input nodes. It is valid to
call this RPC with empty list of target nodes - in this case, all nodes
that have been modified in the UniConfig transaction will be validated.

The configuration of nodes consists of the following phases:

1.  open transaction to device
2.  write configuration
3.  validate configuration
4.  close transaction

If one node failed in second (validation) phase for any reason, the RPC
will fail entirely.

> **note**
>
> The validation (second phase) take place only on nodes that support
> this operation.

Validate RPC is shown in the figure bellow.

![RPC validate](RPC_validation-RPC_validate.svg)

RPC Examples
------------

### Successful Example

RPC validate input has 1 target node and the output describes the result
of the validation.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC commit input has 1 target node and the output describes the result
of the validation. Node has failed because failed validation.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**
