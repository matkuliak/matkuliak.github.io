# RPC delete-snapshot

RPC removes the snapshot from CONFIG datastore of UniConfig transaction.
RPC input contains the name of the snapshot topology which should be
removed. RPC output contains result of the operation.

![RPC delete-snapshot](RPC_delete-snapshot-RPC_delete_snapshot.svg)

## RPC Examples

### Successful Example

RPC input contains the name of the snapshot topology which should be
removed. RPC output contains the results of the operation.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/snapshot-manager:delete-snapshot' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "name" : "snapshot1"
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "overall-status": "complete"
    }
}
```

### Failed example

RPC input contains the name of the snapshot topology which should be
removed. The input snapshot name does not exist. RPC output contains the
results of the operation.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/snapshot-manager:delete-snapshot' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "name" : "snapshot1"
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "error-message": "Snapshot with name snapshot1 does not exist. Cannot delete snapshot.",
        "overall-status": "fail"
    }
}
```
