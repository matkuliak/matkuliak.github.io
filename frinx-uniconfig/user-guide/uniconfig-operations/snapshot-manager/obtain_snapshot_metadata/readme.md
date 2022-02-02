# Obtaining snapshots-metadata

Snapshots metadata contains list of created snapshots with the date of
creation and list of nodes.

```bash RPC Request
curl --location --request GET 'http://localhost:8181/rests/data/snapshot-manager:snapshots-metadata?content=config' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
    "snapshots-metadata": {
        "snapshot": [
            {
                "name": "snapshot1",
                "creation-time": "2021-10-28 15:24:29.0",
                "nodes": [
                    "R1"
                ]
            }
        ]
    }
}
```
