# Create subscription

This operation creates a subscription to the NETCONF notification stream
and provides a WebSocket endpoint which can be used to listen for the
notifications.

Besides the required stream identifier parameter, this operation also
supports optional parameters:

- *start-time* - must be specified if user wants to enable replay and
    it should start at the time specified.
- *stop time* - used with the optional replay feature to indicate the
    newest notifications of interest. If stopTime is not present, the
    notifications will continue until the subscription is terminated.
    Must be used with and be later than *start-time*. Values in the
    future are valid.

!!!
Creation of new subscription for the stream will terminate all
existing subscriptions for this stream.
!!!

## Subscription Workflow

Subscription operation triggers the following workflow execution in the
background:

![subscribe-to-stream](subscribe-to-stream.svg)

## RPC Examples

### Successful example

```bash RPC Request
curl --location --request GET 'http://localhost:8181/rests/data/ietf-restconf-monitoring:restconf-state/streams/stream/netconf-stream/{stream-identifier}/JSON?start-time=2020-10-01T10:30:00%2B03:00&stop-time=2020-10-02T11:30:00%2B03:00' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
 "location": "ws://192.168.56.11:8181/rests/netconf-stream/{stream-identifier}/JSON"
}
```

### Failed example

This example demonstrated response to the request with wrong combination
of 'start-time' and 'stop-time' ('stop-time' must be later than
'start-time').

```bash RPC Request
curl --location --request GET 'http://localhost:8181/rests/data/ietf-restconf-monitoring:restconf-state/streams/stream/netconf-stream/ver3-NETCONF/JSON?start-time=2020-10-02T14:30:00%2B03:00&stop-time=2020-10-01T18:53:00%2B03:00' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 400
{
 "errors": {
   "error": [
     {
       "error-message": "Stop-time parameter has to be later than start-time parameter.",
       "error-tag": "operation-failed",
       "error-type": "application"
     }
   ]
 }
}
```

### Failed example

This example demonstrates response to the request with the wrong stream
identifier

```bash RPC Request
curl --location --request GET 'http://localhost:8181/rests/data/ietf-restconf-monitoring:restconf-state/streams/stream/netconf-stream/wrong-stream-id/JSON' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 400
{
 "errors": {
   "error": [
     {
       "error-message": "Mount point /(urn:TBD:params:xml:ns:yang:network-topology?revision=2013-10-21)network-topology/topology/topology[{(urn:TBD:params:xml:ns:yang:network-topology?revision=2013-10-21)topology-id=topology-netconf}]/node/node[{(urn:TBD:params:xml:ns:yang:network-topology?revision=2013-10-21)node-id=wrong}] was not found.",
       "error-tag": "missing-element",
       "error-type": "application"
     }
   ]
 }
}
```
