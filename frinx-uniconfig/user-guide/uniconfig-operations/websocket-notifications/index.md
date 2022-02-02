# WebSocket Notifications

## Background

[RFC 5277](https://tools.ietf.org/html/rfc5277) document defines a
mechanism where the NETCONF client indicates interest in receiving event
notifications from a NETCONF server by creating a subscription to
receive event notifications. The NETCONF server replies to indicate
whether the subscription request was successful and, if it was
successful, begins sending the event notifications to the NETCONF client
as the events occur within the system. These event notifications will
continue to be sent until either the NETCONF session is terminated or
the subscription terminates for some other reason. The event
notification subscription allows a number of options to enable the
NETCONF client to specify which events are of interest. These are
specified when the subscription is created.

This component is responsible for integration of the [RFC
5277](https://tools.ietf.org/html/rfc5277) NETCONF notification
mechanism into the UniConfig. The solution provides comprehensive
Northbound API to manage subscriptions lifecycle via RESTCONF and
WebSocket network transport for notification delivery to the
subscribers.

## Architecture

This section describes the high-level design of this component and
provides details of communication with the NETCONF device via the
Southbound interface.

[!ref text="Architecture"](../websocket-notifications/architecture)


## Operations

Provides a list of supported operations on subscriptions, includes
request examples and workflow diagrams.

[!ref text="Operations"](../websocket-notifications/operations)
