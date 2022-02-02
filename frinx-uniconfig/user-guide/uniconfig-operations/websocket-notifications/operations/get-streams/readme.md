# Get available streams

This operation returns a list of the available for subscription
notification streams from the the *"ietf-restconf-monitoring"* module
according to [RESTCONF RFC 8040](https://tools.ietf.org/html/rfc8040).
This document defines stream model with the following parameters:

- *name* - The stream name. To preserve uniqueness for the stream name
    on the list UniConfig adds node name as a prefix before the original
    stream name.
- *description* - Description of stream content.
- *replay-support* - Indicates if replay buffer is supported for this
    stream. If 'true', then the server supports the 'start-time' and
    'stop-time' query parameters for this stream.
- *replay-log-creation-time* - Indicates the time the replay log for
    this stream was created. Available only if replay-support is
    supported.
- *access* - Contains a list of supported encoding formats for the
    stream and respective URL for subscription.

UniConfig extends stream model with the following parameters:

- *stream-extensions:substream* - contains a list of yang definitions
    used by the stream.
- *stream-extensions:is-active-subscription* - indicates existence of
    active subscriptions for the stream.

![get-streams](get-streams.svg)

## RPC Examples

### Successful example

```bash RPC Request
curl --location --request GET 'http://localhost:8181/rests/data/ietf-restconf-monitoring:restconf-state/streams/stream' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
    "stream":[
        {
            "name":"node1-NETCONF",
            "description":"default NETCONF event stream",
            "replay-support":true,
            "access":[
                {
                    "encoding":"JSON",
                    "location":"netconf-stream/node-NETCONF/JSON"
                }
            ],
            "stream-extensions:substream":[
                {
                    "name":"(urn:ietf:params:xml:ns:yang:ietf-netconf-notifications?revision=2012-02-06)netconf-capability-change",
                    "description":"Generated when the NETCONF server detects that\nthe server capabilities have changed.\nIndicates which capabilities have been added, deleted,\nand/or modified.  The manner in which a server\ncapability is changed is outside the scope of this\ndocument."
                },
                {
                    "name":"(http://www.versa-networks.com/oam?revision=2019-11-01)versa-alarm-notification",
                    "description":"Versa Notification Message"
                }
            ],
            "stream-extensions:is-active-subscription":false
        },
        {
            "name":"node1-oam",
            "description":"Versa notifications",
            "replay-support":true,
            "access":[
                {
                    "encoding":"JSON",
                    "location":"netconf-stream/node-oam/JSON"
                }
            ],
            "stream-extensions:substream":[
                {
                    "name":"(http://www.versa-networks.com/oam?revision=2019-11-01)versa-notification",
                    "description":"Versa Notification Message"
                },
                {
                    "name":"(urn:ietf:params:xml:ns:netmod:notification?revision=2008-07-14)notificationComplete",
                    "description":"This notification is sent to signal the end of a notification\nsubscription. It is sent in the case that stopTime was\nspecified during the creation of the subscription.."
                }
            ]
        }
    ]
}
```
