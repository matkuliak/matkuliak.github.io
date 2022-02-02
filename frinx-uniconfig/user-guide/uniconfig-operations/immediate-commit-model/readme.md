# Immediate Commit Model

The immediate commit creates new transactions for every call of an RPC.
The transaction is then closed so no lingering data will occur.

For reading data (GET request), a sequential diagram was created for
better understanding of how the whole process works.

![Get Request](get-request.png)

Similarly, a sequential diagram for putting data (PUT request) was
created as well.

![Put Request](put-request.png)

The key difference in those diagrams is that editing data (PUT, PATCH,
DELETE, POST) + RPC calls in the database need to be committed, so there
is an additional call of the commit RPC. This commit ensures that the
transaction is closed. For reading data, it is necessary to close the
transaction differently, because no data were changed, so calling a
commit would be unnecessary.

RPC Examples
------------

### Successful example

RPC input contains a new interface that will be added to the existing
ones.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=R1/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces/interface=Loopback123' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
"interface":
    [
        {
            "name": "Loopback123",
            "config": {
                "type": "iana-if-type:softwareLoopback",
                "name": "Loopback123",
                "description": "testing",
                "enabled": true
            }
        }
    ]
}'
```

```json RPC Response, Status: 200
{
}
```

After putting the data into the database, they will be automatically
committed and can be viewed.

```bash RPC Request
curl --location --request GET 'http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=R1/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces/interface=Loopback123?content=nonconfig' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
"frinx-openconfig-interfaces:interface": [
        {
            "name": "Loopback123",
            "config": {
                "type": "iana-if-type:softwareLoopback",
                "enabled": true,
                "description": "testing",
                "name": "Loopback123"
            }
        }
    ]
}
```

### Failed Example

RPC input contains a value that is not supported.

```bash RPC Request
curl --location --request PUT 'http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=R1/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces/interface=Loopback123' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
"interface":
    [
        {
            "name": "Loopback123",
            "config": {
                "type": "iana-if-type:softwareLoopback",
                "name": "Loopback123",
                "mtu": 1400,
                "description": "testing",
                "enabled": true
            }
        }
    ]
}'
```

```json RPC Response, Status: 500
{
   "errors": {
      "error": [
         {
            "error-message": "UniconfigTransaction[transactionId=f5184f5d-c0bc-4abc-a591-eeddb704eac1, creationTime=2021-10-12T12:06:00.028925Z, readWriteTx=f5184f5d-c0bc-4abc-a591-eeddb704eac1]: The commit RPC returned FAIL status. \\n Bulk update failed because: Tue Oct 12 12:06:09.478 UTC\\r\\n!! SEMANTIC ERRORS: This configuration was rejected by \\r\\n!! the system due to semantic errors. The individual \\r\\n!! errors with each failed configuration command can be \\r\\n!! found below.\\r\\n\\r\\n\\r\\ninterface Loopback123\\r\\n mtu 1400\\r\\n!!% This operation is not supported: The interface owner has not registered support for MTU\\r\\n!\\r\\nend",
            "error-tag": "operation-failed",
            "error-type": "rpc"
         }
      ]
   }
}
```
