# Architecture

## Components schema

![simplified-architecture](simplified-architecture.svg)]

## Streams available for subscription

Streams available for subscriptions are discovered automatically after
the successful mounting process of the NETCONF node. Dedicated service
makes the following RPC call to fetch a list of the streams from the
node.



```guess RPC Request
<rpc message-id="101" xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
  <get>
    <filter type="subtree">
      <netconf xmlns="urn:ietf:params:xml:ns:netmod:notification">
        <streams/>
      </netconf>
    </filter>
  </get>
</rpc>
```


```guess RPC Response
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="101">
  <data>
    <netconf xmlns="urn:ietf:params:xml:ns:netmod:notification">
      <streams>
        <stream>
          <name>NETCONF</name>
          <description>default NETCONF event stream</description>
          <replaySupport>false</replaySupport>
        </stream>
        <stream>
          <name>oam</name>
          <description>Versa notifications</description>
          <replaySupport>false</replaySupport>
        </stream>
      </streams>
    </netconf>
  </data>
</rpc-reply>
```

This operation for mounted nodes is also available via RESTCONF API with
the following request:

```bash RPC Request
curl --location --request GET 'http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=node1/yang-ext:mount/nc-notifications:netconf/streams/stream' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
 "stream": [
   {
       "name": "NETCONF",
       "replayLogCreationTime": "2020-09-29T09:49:54+00:00",
       "replaySupport": true,
       "description": "default NETCONF event stream"
   },
   {
       "name": "oam",
       "replayLogCreationTime": "2020-09-29T09:08:13+00:00",
       "replaySupport": true,
       "description": "Versa notifications"
   }
 ]
}
```

After the successful response, data about available streams are stored
under the *"ietf-restconf-monitoring"* module according to [RESTCONF RFC
8040](https://tools.ietf.org/html/rfc8040). Complete list of streams
available for subscription can be fetched by the
ws-notifications-get-streams-label operation.

## Subscription workflow

![subscription-workflow](subscription-workflow.svg)

### Creation of the subscription

Before the client can be connected and listen for the notifications from
the WebSocket endpoint, it is required to create a subscription by
sending a ws-notifications-create-subscription-label request. This will
start the workflow with the following execution steps:

1. Creation of a separate mount point and session for the notification
    stream (see ws-notifications-separate-netconf-session-label chapter
    for more details). Within this session, 'create-subscription' RPC
    call will be executed towards NETCONF device.

    ```guess RPC 'create-subscription' request
    <rpc message-id="101" xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
     <create-subscription xmlns ="urn:ietf:params:xml:ns:netconf:notification:1.0">
       <stream>NETCONF</stream>
        <startTime>2020-10-01T18:53:00+03:00</startTime>
        <stopTime>2020-10-04T18:53:00+03:00</stopTime>
     </create-subscription>
    </rpc>
    ```

    ```guess RPC response
    <rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="101">
      <ok/>
    </rpc-reply>
    ```

2. Initialization of the separate handler with a cache for the newly
    created subscription (see ws-notifications-cache-label chapter for
    more details)
3. Update WebSocket handler to accept incoming connections for the
    newly created subscription (see
    ws-notifications-websocket-session-label chapter for more details
    about WebSocket and messages format).
4. Return a proper URL for the incoming WebSocket connections.

### Creation of the WebSocket connection

1. After the successful creation of subscription and obtaining the URL
    for the WebSocket connection, a client can proceed with a standard
    HTTP connection upgrade request.
2. Following a successful upgrade request, a data transfer begins. Data
    transfer for notification streams suppose to be unidirectional, so
    as a consequence, any messages sent by the client will be ignored.
3. Client may close connection at any time. Please note that the
    notification stream will be closed if the last client is
    disconnected.

## Notifications cache

Successful subscription to the NETCONF stream triggers an immediate
stream of incoming notifications before the real subscriber is connected
to WebSocket. To avoid notifications loss during this period, the
component stores notifications inside the temporary cache. So, when the
new subscriber connects to the WebSocket, it receives notifications from
this cache first, and only after that starts to receive live
notifications from the real subscription session.

## Separate NETCONF session for subscription

NETCONF device may have the :interleave capability that indicates
support to interleave other NETCONF operations within a notification
subscription. This means the NETCONF server can receive, process, and
respond to NETCONF requests on a session with an active notification
subscription. However, not all devices support this capability, so the
common approach for devices 'with' and 'without' interleave capability
is to track notifications with a separate NETCONF session. In order to
support this functionality, UniConfig will create a separate NETCONF
session with a separate mount-point with every subscription. These mount
points and sessions are destroyed automatically when the corresponding
subscription is closed.

## WebSocket connection and data transfer

WebSocket connection plays a transport role in the delivery of NETCONF
event notifications to subscribers. Original event notification is a
valid and well-formed XML document with a mandatory *eventTime*
attribute and optional notification-specific content. The notification
handler will transform the XML document to the proper JSON
representation according to the [RFC
7951](https://tools.ietf.org/html/rfc7951) before sending it to
subscribers via a WebSocket connection.

If the subscription was created with a *startTime* parameter set to the
past, then the notification stream includes a pre-defined
*replayComplete* notification. This notification is sent to indicate
that all of the replay notifications have been sent to the subscriber.

If the *stopTime* parameter was specified during the creation of
subscription, then the stream may contain pre-defined
*notificationComplete* notification. This notification is sent to signal
the end of a notification subscription. The component will close the
WebSocket connection and underlying NETCONF session after this
notification automatically.

## Notification Examples

```json Notification example
{
  "ietf-restconf:notification": {
    "ietf-netconf-notifications:netconf-config-change": {
      "changed-by": {
        "session-id": 28,
        "source-host": "10.255.246.50",
        "username": "admin"
      },
      "edit": [
        {
          "operation": "replace",
          "target": "/interfaces:interfaces/interfaces:vni[interfaces:name='vni-0/3']/interfaces:description"
        }
      ],
      "datastore": "running"
    },
    "eventTime": "2020-10-09T13:55:16.823+03:00"
  }
}
```

```json replayComplete notification
{
  "ietf-restconf:notification": {
    "nc-notifications:replayComplete": {},
    "eventTime": "2020-10-09T14:14:34.674+03:00"
  }
}
```

```json notificationComplete notification
{
  "ietf-restconf:notification": {
    "nc-notifications:notificationComplete": {},
    "eventTime": "2020-10-09T14:15:04.676+03:00"
  }
}
```
