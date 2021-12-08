RPC commit
==========

The external application stores the intended configuration under nodes
in the UniConfig topology. The trigger for execution of configuration is
an RPC commit. Output of the RPC describes the result of the commit and
matches all modified nodes in the UniConfig transaction.

The configuration of nodes consists of the following phases:

1.  lock and validate configured nodes - Locking all modified nodes
    using PostgreSQL advisory locks and validation of fingerprints - if
    another transaction tries to commit overlapping nodes or different
    transaction has already changed one of the nodes, then commit will
    fail at this step.
2.  write configuration into device - Pushing calculated changes into
    device without committing of this changes.
3.  validate configuration - Validation of written configuration from
    the view of constraints and consistency.
4.  confirmed commit - It is used for locking of device configuration,
    so no other transaction can touch this device.
5.  confirming commit (submit configuration) - Persisting all changes on
    devices and in the PostgreSQL database. UniConfig transaction is
    closed.

![RPC commit](RPC_commit-RPC_commit.svg)

The third and fourth phases take place only on the nodes that support
these operations. If one node failed in the random phase for any reason
the RPC will fail entirely. After commit RPC, UniConfig transaction is
closed regardless of the commit result.

> **note**
>
> If one of the nodes uses a confirmed commit (phase 4), which does not
> fail, then it is necessary to issue the submitted configuration (phase
> 5) within the timeout period. Otherwise the node configuration issued
> by the confirmed commit will be reverted to its state before the
> confirmed commit (i.e. confirmed commit makes only temporary
> configuration changes). The timeout period is 600 seconds (10 minutes)
> by default, but the user can change it in the installation request.

Next diagram describe the first phase of commit RPC - locking of changes
nodes in the PostgreSQL database and verification if other transaction
has already committed overlapping nodes.

Next diagrams describe all 5 commit phases in detail:

**1. Lock and validate configured nodes**

![Locking nodes](RPC_commit-locking-phase-Locking_phase.svg)

**2. Write configuration into device**

![Configuration phase](RPC_commit-configuration-phase-Configuration_phase.svg)

**3. Validate configuration**

![Validation phase](RPC_commit-validation-phase-Validation_phase.svg)

**4. Confirmed commit**

![Confirmed commit](RPC_commit-confirmed-commit-phase-Confirmed_commit_phase.svg)

**5. Confirming commit (submit configuration)**

![Confirming commit](RPC_commit-confirming-commit-Confirming_commit_phase.svg)

The last diagram shows rollback procedure that must be executed after
failed commit on nodes that have already been configured and don't
support 'candidate' datastore.

![Rollback operation](RPC_commit_rollback-Rollback_changes.svg)

RPC Examples
------------

### Successful Example

RPC commit input has 2 target nodes and the output describes the result
of the commit.

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

RPC commit input has 2 target nodes and the output describes the result
of the commit. One node has failed because failed validation (IOSXRN).

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC commit input has 2 target nodes and the output describes the result
of the commit. One node has failed because the confirmed commit failed
(IOSXRN).

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC commit input has 2 target nodes and the output describes the result
of the commit. One node has failed because of the time delay between the
confirmed commit and the submitted configuration (IOSXRN).

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC commit input has 2 target nodes and the output describes the result
of the commit. One node has failed due to improper configuration
(IOSXRN).

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC commit input has 3 target nodes and the output describes the result
of the commit. One node has failed due ot improper configuration
(IOSXR), the other has not been changed (IOSXRN), and the last has not
been mounted yet (AAA).

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC commit input has 2 target nodes and the output describes the result
of the commit. One node has failed due to improper configuration
(IOSXRN), the other has not been changed (IOSXR).

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC commit input has 2 target nodes and the output describes the result
of the commit. One node has lost connection (IOSXR), the other has not
been mounted yet (AAA).

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
