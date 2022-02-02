# RPC revert-changes

This RPC revert changes that were configured within one transaction. If
a user wants to revert single transaction or multiple transactions, he
must find out transaction-ids and paste them into the body of RPC. The
transaction-id is part of the transaction-metadata, that is created by a
transaction tracker after commit/checked-commit RPC.

!!!
RPC revert-changes updates data only in the CONFIGURATION Snapshot. If
we want to write reverted data to the device, we must use RPC commit
after RPC revert-changes.
!!!

![RPC revert-changes](RPC_revert-changes.png)

## Ignore non-existent nodes

If a user wants to revert multiple transactions, some transactions
metadata may contain nodes that do not currently exist in UniConfig. In
this case, the RPC fails. The user has a choice of two options:

1.  remove transaction that contain non-existent nodes from the request
    body
2.  add 'ignore-non-existing-nodes' parameter to the RPC request body
    with a value of 'true' (default: 'false')

!!!
If the user does not use the 'ignore-non-existing-nodes' parameter,
the default value 'false' is automatically used.
!!!

## RPC Examples

### Successful examples

Before reverting a transaction we need to know its ID. We will use the
GET request to display all stored transaction-metadata.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/restconf/operations/device-discovery:discover' \
--header 'Accept: application/json' \
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
    }
}
```

Reverting changes of the single transaction.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/restconf/operations/device-discovery:discover' \
--header 'Accept: application/json' \
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
    }
}
```

Reverting changes of multiple transactions.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/restconf/operations/device-discovery:discover' \
--header 'Accept: application/json' \
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
    }
}
```

Reverting changes of multiple transactions, where the transaction with
id '2c4c1eb5-185a-4204-8021-2ea05ba2c2c1' contains non-existent node
'R1'. In this case 'ignore-non-existing-nodes' with a value of 'true' is
used, and therefore the RPC will be successful.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/restconf/operations/device-discovery:discover' \
--header 'Accept: application/json' \
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
    }
}
```

### Failed example

This is a case when revert-changes request contains a non-existent
transaction in the request body.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/restconf/operations/device-discovery:discover' \
--header 'Accept: application/json' \
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
    }
}
```

Reverting changes of multiple transactions, where the transaction
metadata with id '2c4c1eb5-185a-4204-8021-2ea05ba2c2c1' contains
non-existent node. In this case 'ignore-non-existing-nodes' with a value
of 'false' is used, and therefore the RPC fails.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/restconf/operations/device-discovery:discover' \
--header 'Accept: application/json' \
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
    }
}
```
