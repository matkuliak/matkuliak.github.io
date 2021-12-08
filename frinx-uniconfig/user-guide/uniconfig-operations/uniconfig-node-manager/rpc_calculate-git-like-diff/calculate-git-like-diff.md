RPC calculate-git-like-diff
===========================

This RPC creates a diff between the actual UniConfig topology nodes and
the intended UniConfig topology nodes. The RPC input contains a list of
UniConfig nodes to calculate the diff. Output of the RPC contains a list
of statements representing the diff after the commit in a git-like
style. It checks for every touched node in the transaction if target
nodes are not specified in the input. If one node failed for any reason,
the RPC will fail entirely.

![RPC calculate-git-like-diff](RPC_calculate-git-like-diff-RPC_calculate_git_like_diff.svg)

RPC Examples
------------

### Successful Example

The RPC calculate-diff input has no target nodes specified so it will
look for all touched nodes in the transaction, and the output will
contain a list of all changes on different paths. Multiple changes that
occur under the same path are merged together.

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
