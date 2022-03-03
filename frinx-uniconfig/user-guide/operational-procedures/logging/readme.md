# Logging Framework

## Logback Configuration

UniConfig distribution uses Logback as the implementation of the logging
framework. Logback is the successor to to the log4j framework with many
improvements such as; more options for configuration, better
performance, and context-based separation of logs. Context-based
separation of logs is used widely in UniConfig to achieve per-device
logging based on the set marker in the logs.

Logback configuration is placed in 'config/logback.xml' file under
UniConfig distribution. For more information about formatting of logback
configuration, look at the
<http://logback.qos.ch/manual/configuration.html> site. This section
describes parts of the configuration in the context of UniConfig
application.

### Appenders

The following appenders are used:

1. 'STDOUT': Prints logs into the console.
2. 'logs': Used for writing all logs to the output file on path
    'log/logs.log'. The rolling file appender is applied.
3. 'netconf-notifications', 'netconf-messages', 'netconf-events', and
    'cli-messages': Sifting appenders that split logs per node ID that
    is set in the marker of the logs. Logs are written to different
    subdirectories under 'log' directory and they are identified by
    their node ID. The rolling file appender is applied.
4. 'restconf': Appender used for writing of RESTCONF messages into
    'log/restconf.log' file. The rolling file appender is applied.

### Loggers

There are 2 groups of loggers:

1. Package-level logging brokers: Loggers that are used for writing
    general messages into the console and a single output file. Logging
    level is set by default to 'INFO'. For debugging purposes it is
    handy to change logging threshold to 'TRACE' or 'DEBUG' level.
    Covered layers: UniConfig, Unified, Controller, RESTCONF, CLI,
    NETCONF. Used appenders: 'STDOUT' and 'logs'.
2. Loggers used for logging brokers: These loggers should not be
    changed since the state of logging can be changed using RPC calls.
    Classpaths point to specific classes that represent implementations
    of logging brokers, the logging level is set to 'TRACE'. Used
    appenders: 'netconf-notifications', 'netconf-messages',
    'netconf-events', 'cli-messages', and 'restconf'.

### Updating Configuration

- Logback is configured to scan for changes in its configuration file
    and automatically reconfigure itself when the configuration file
    changes.
- Scanning period is set by default to 5 seconds.

### Example configuration

In the logback.xml file you can edit level of logging for each component
of UniConfig:

```
<!-- root logger -->
<root level="INFO">
    <appender-ref ref="STDOUT"/>
    <appender-ref ref="logs"/>
</root>

<!-- UniConfig layer part -->
<logger name="io.frinx.uniconfig" level="DEBUG"/>

<!-- Unified layer part -->
<logger name="io.frinx.unitopo" level="INFO"/>

<!-- NETCONF part -->
<logger name="org.opendaylight.netconf" level="INFO"/>

<!-- CLI part -->
<logger name="io.frinx.cli" level="TRACE"/>

<!-- translation unit framework part -->
<logger name="io.frinx.translate.unit.commons" level="INFO"/>
<logger name="io.fd.honeycomb" level="INFO"/>

<!-- RESTCONF part -->
<logger name="org.opendaylight.restconf" level="DEBUG"/>
<logger name="org.opendaylight.aaa" level="INFO"/>


<!-- controller part -->
<logger name="org.opendaylight.daexim" level="INFO"/>
<logger name="org.opendaylight.controller" level="INFO"/>
<logger name="org.opendaylight.yangtools" level="INFO"/>
```

#### INFO

**This is recommended level for production environments.** INFO messages
display behavior of applications. They state what happened. For example,
if a particular service stopped or started or you added something to the
database. These entries are nothing to worry about during usual
operations. The information logged using the INFO log is usually
informative, and it does not necessarily require you to follow up on it.

#### DEBUG

With DEBUG, you are giving diagnostic information in a detailed manner.
It is verbose and has more information than you would need when using
the application. DEBUG logging level is used to fetch information needed
to diagnose, troubleshoot, or test an application. This ensures a smooth
running application.

#### TRACE

The TRACE log level captures all the details about the behavior of the
application. It is mostly diagnostic and is more granular and finer than
DEBUG log level. This log level is used in situations where you need to
see what happened in your application.

## Logging Brokers

The logging broker represents a configurable controller that logs one
logical group of messages from a single classpath. Logging of multiple
messages from the same classpath simplifies configuration of loggers in
Logback since only one logger per broker must be specified. The logging
broker can be controlled using RESTCONF RPCs; there are multiple
operations where it is possible to trigger logging for the whole broker,
or just for specified node IDs. Configuration of the logger in the
logback file that is assigned to the logging broker should not be
changed at all.

### Implemented Logging Brokers

The following subsections describe currently implemented logging
brokers.

#### RESTCONF

- It is used for logging authenticated HTTP requests and responses;
    information about URI, source, HTTP method, query parameters, HTTP
    headers, and body.
- Per-device logging cannot be enabled for this broker; all logs are
    saved to 'log/restconf.log' file.
- It is possible to configure HTTP headers in which the content must
    be masked in logs (using asterisk characters). This is useful
    especially if there are some headers which contain private data
    (such as Authorization or a Cookie header). Hidden HTTP headers are
    marked using header identifiers.
- It is also possible to configure HTTP methods for which the
    communication (requests and responses) should not be logged to a
    file.
- Requests and responses are paired using a unique message-id. This
    message-id is not part of the HTTP request, it is generated on the
    RESTCONF server.
- Requests and responses contain Uniconfig transactions for easier matching
  with the log-transactions.

**Example: - Request and corresponding response with the same message-id**

```
08:51:21.508 TRACE org.opendaylight.restconf.nb.rfc8040.jersey.providers.logging.RestconfLoggingBroker - HTTP request:
Message ID: 3
Uniconfig transaction: b6639cb4-55f2-449e-a91e-d2ad490198d2
HTTP method: POST
URI: http://localhost:8181/rests/operations/logging:enable-device-logging
Source address: 0:0:0:0:0:0:0:1
Source port: 37472
User ID: admin@sdn
HTTP headers:
    User-Agent: [curl/7.69.1]
    Authorization: ***
    Host: [localhost:8181]
    Accept: [*/*]
    Content-Length: [116]
    Content-Type: [application/json]
Request body:
{
  "input": {
    "broker-identifier": "netconf_notifications",
    "device-list": [
      "xr6",
      "xr7"
    ]
  }
}

08:51:21.518 TRACE org.opendaylight.restconf.nb.rfc8040.jersey.providers.logging.RestconfLoggingBroker - HTTP response:
Request message ID: 3
Uniconfig transaction: b6639cb4-55f2-449e-a91e-d2ad490198d2
Status code: 200
HTTP headers:
    Content-Type: [application/yang-data+json]
Response body:
{
  "output": {
    "message": "Successfully updated logging broker [netconf_notifications]",
    "status": "complete"
  }
}
```

#### CLI messages

- Broker used for logging of all CLI requests and responses.
- These CLI requests and responses are paired with unique message-id
    attribute, which is generated.
- Per-device logging is supported - logs for CLI messages are stored
    under 'log/cli-messages' directory and named by '[node-id].log'
    pattern.

Example - sending POST RPC for installing CLI device, and getting
requests with corresponding responses paired with same Message-ID:

```
15:11:33.119 TRACE io.frinx.cli.io.impl.cli.CliLoggingBroker - Message-ID:1 - Sending CLI command:
show configuration commit list | utility egrep "^1 "
15:11:33.697 TRACE io.frinx.cli.io.impl.cli.CliLoggingBroker - Message-ID:1 - Received CLI response:
Wed Sep 22 13:11:29.776 UTC
1    1000005360            cisco     vty0:node0_0_CPU0   CLI         Mon Sep  6 07:53:03 2021
15:11:33.724 TRACE io.frinx.cli.io.impl.cli.CliLoggingBroker - Message-ID:2 - Sending CLI command:
show running-config
15:11:34.459 TRACE io.frinx.cli.io.impl.cli.CliLoggingBroker - Message-ID:2 - Received CLI response:
Wed Sep 22 13:11:30.116 UTC
Building configuration...
!! IOS XR Configuration 5.3.4
!! Last configuration change at Mon Sep  6 07:53:03 2021 by cisco
!
hostname XR5
interface MgmtEth0/0/CPU0/0
 ipv4 address 192.168.1.214 255.255.255.0
!
interface GigabitEthernet0/0/0/0
 shutdown
!
interface GigabitEthernet0/0/0/1
 description init description
 shutdown
!
interface GigabitEthernet0/0/0/2
!
ssh server v2
ssh server netconf port 830
netconf-yang agent
 ssh
!
end
```

#### NETCONF Messages

- A broker is used for logging of all NETCONF messages incoming or
    outgoing, except the NETCONF notifications (a distinct broker has
    been introduced for notifications).
- NETCONF RPC's and responses can be matched using the 'message-id'
    attribute that is placed in the RPC header.
- Per-device logging is supported, logs for NETCONF messages are
    stored under the directory 'log/netconf-messages' and named by the
    '[node-id].log' pattern.

**Example: - Sending NETCONF GET RPC and receiving response**

```
11:10:15.038 TRACE org.opendaylight.netconf.logging.brokers.NetconfMessagesLoggingBroker - Session: 641 - Sending NETCONF message:
<rpc xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="m-179">
    <get>
        <filter xmlns:ns0="urn:ietf:params:xml:ns:netconf:base:1.0" ns0:type="subtree">
            <netconf xmlns="urn:ietf:params:xml:ns:netmod:notification"/>
        </filter>
    </get>
</rpc>

11:10:15.055 TRACE org.opendaylight.netconf.logging.brokers.NetconfMessagesLoggingBroker - Session: 641 - Received NETCONF message:
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="m-179">
    <data>
        <netconf xmlns="urn:ietf:params:xml:ns:netmod:notification">
            <streams>
                <stream>
                    <name>NETCONF</name>
                    <description>default NETCONF event stream</description>
                    <replaySupport>true</replaySupport>
                    <replayLogCreationTime>2020-09-29T09:49:54+00:00</replayLogCreationTime>
                </stream>
                <stream>
                    <name>oam</name>
                    <description>Vendor notifications</description>
                    <replaySupport>true</replaySupport>
                    <replayLogCreationTime>2020-11-09T13:20:01+00:00</replayLogCreationTime>
                </stream>
            </streams>
        </netconf>
    </data>
</rpc-reply>
```

!!!
Number 641 represents the session ID. It is read from 
the NETCONF hello message. If multiple sessions are 
created between the NETCONF server and NETCONF client and are
logically grouped by the same node ID, then logs from multiple
sessions are stored to the same logging file (this is needed to
distinguish between the sessions). Multiple NETCONF sessions between
the UniConfig and NETCONF server are created for each subscription to
the NETCONF stream.
!!!

#### NETCONF Notifications

- A broker is used for logging of incoming NETCONF notifications.
- Per-device logging is supported, logs for NETCONF notifications are
    stored under the directory 'log/netconf-notifications' and named by
    the '[node-id].log' pattern.

**Example: - Received two notifications**

```
11:37:03.907 TRACE org.opendaylight.netconf.logging.brokers.NotificationsLoggingBroker - 117123a1: Received NETCONF notification:
<notification xmlns="urn:ietf:params:xml:ns:netconf:notification:1.0">
    <eventTime>2020-11-11T02:36:54.484233-08:00</eventTime>
    <netconf-session-start xmlns="urn:ietf:params:xml:ns:yang:ietf-netconf-notifications">
        <username>admin</username>
        <session-id>35</session-id>
        <source-host>10.255.246.31</source-host>
    </netconf-session-start>
</notification>

11:37:04.515 TRACE org.opendaylight.netconf.logging.brokers.NotificationsLoggingBroker - 117123a1: Received NETCONF notification:
<notification xmlns="urn:ietf:params:xml:ns:netconf:notification:1.0">
    <eventTime>2020-11-11T02:36:55.098119-08:00</eventTime>
    <netconf-session-end xmlns="urn:ietf:params:xml:ns:yang:ietf-netconf-notifications">
        <username>admin</username>
        <session-id>35</session-id>
        <source-host>10.255.246.31</source-host>
        <termination-reason>dropped</termination-reason>
    </netconf-session-end>
</notification>
```

#### NETCONF Events

- Logs generated by this broker contain session-related information
    about the establishment or closing of a NETCONF session from the
    view of the NETCONF client placed in UniConfig.
- These logs don't contain full printouts of sent or received NETCONF
    messages.
- Per-device logging is supported, logs for NETCONF events are stored
    under the directory 'log/netconf-events' and named by the
    '[node-id].log' pattern.

**Example:**

```
11:08:59.407 INFO org.opendaylight.netconf.logging.brokers.NetconfEventsLoggingBroker - node1: Connecting remote device with config: Node{getNodeId=Uri [_value=node1], augmentations={interface org.opendaylight.yang.gen.v1.urn.opendaylight.netconf.node.topology.rev150114.NetconfNode=NetconfNode{getActorResponseWaitTime=5, getBetweenAttemptsTimeoutMillis=2000, getConcurrentRpcLimit=0, getConnectionTimeoutMillis=60000, getCredentials=LoginPassword{getPassword=versa123, getUsername=admin, augmentations={}}, getCustomizationFactory=default, getDefaultRequestTimeoutMillis=60000, getDryRunJournalSize=0, getEditConfigTestOption=Set, getHost=Host [_ipAddress=IpAddress [_ipv4Address=Ipv4Address [_value=10.103.5.202]]], getKeepaliveDelay=5, getMaxConnectionAttempts=100, getPort=PortNumber [_value=2022], getSleepFactor=1.0, getYangModuleCapabilities=YangModuleCapabilities{getCapability=[http://tail-f.com/ns/docgen/experimental1?module=docgen&amp;revision=2019-11-01], isOverride=false, augmentations={}}, isEnabledNotifications=true, isReconnectOnChangedSchema=false, isSchemaless=false, isTcpOnly=false}, interface org.opendaylight.yang.gen.v1.http.frinx.io.yang.uniconfig.config.rev180703.UniconfigConfigNode=UniconfigConfigNode{getBlacklist=Blacklist{getExtension=[tailf:display-when false], augmentations={}}, isUniconfigNativeEnabled=true}}}
11:09:00.554 DEBUG org.opendaylight.netconf.logging.brokers.NetconfEventsLoggingBroker - node1: Session established
11:09:00.558 DEBUG org.opendaylight.netconf.logging.brokers.NetconfEventsLoggingBroker - node1: Session advertised capabilities: ...
11:09:00.832 DEBUG org.opendaylight.netconf.logging.brokers.NetconfEventsLoggingBroker - node1: Connector for node created successfully
```

### Supported Logging Settings

Current logging broker settings are stored in the Operational datastore
under the 'logging-status' root container. The following example shows a
GET query that displays the logging broker settings:

```bash
curl --location --request GET 'http://127.0.0.1:8181/rests/data/logging-status?content=nonconfig' \
--header 'Accept: application/json'
```

**Response:**

```json
{
    "logging-status": {
        "broker": [
            {
                "broker-identifier": "netconf_messages",
                "is-logging-broker-enabled": true,
                "is-logging-enabled-on-all-devices": true,
                "enabled-devices": [
                    "xr6",
                    "xr7"
                ]
            },
            {
                "broker-identifier": "restconf",
                "is-logging-broker-enabled": true,
                "restconf-logging:hidden-http-methods": [
                    "GET"
                ],
                "restconf-logging:hidden-http-headers": [
                    "Authorization",
                    "Cookie"
                ]
            },
            {
                "broker-identifier": "netconf_notifications",
                "is-logging-broker-enabled": true,
                "is-logging-enabled-on-all-devices": true
            },
            {
                "broker-identifier": "netconf_events",
                "is-logging-broker-enabled": true,
                "is-logging-enabled-on-all-devices": true
            }
        ],
        "global": {
            "hidden-types": [
                "password",
                "encrypted"
            ]
        }
    }
}
```

Logging settings are encapsulated inside multiple list entries ('broker'
list) where each list entry contains settings for one logging broker.
Description of the settings that are placed under a single logging
entry:

- **broker-identifier**: Unique identifier of the logging broker.
    Currently, 5 brokers are supported: 'netconf\_messages', 'restconf',
    'netconf\_notifications', 'netconf\_events', and cli\_messages.
- **is-logging-broker-enabled**: Flag that specifies whether the
    logging broker is enabled. If the logging broker is disabled, then
    no logging messages are generated.
- **is-logging-enabled-on-all-devices**: If this flag is set to
    'true', then logs are separated to distinct files in the scope of
    all devices. If it is set to 'false', then logging is enabled only
    for devices that are listed in the 'enabled-devices' leaf-list /
    array. This setting is unsupported in the 'restconf' logging broker
    since RESTCONF currently doesn't differentiate the node ID in the
    requests or responses.
- **enabled-devices**: If 'is-logging-enabled-on-all-devices' is set
    to 'false', then logs are generated only for devices that are
    specified in this list, it acts as a simple filtering mechanism
    based on the whitelist. Blacklist approach is not supported, it is
    not possible to set 'is-logging-enabled-on-all-devices' to 'true'
    and specify devices for which logging feature is disabled. This
    field is not supported in the 'restconf' logging broker.

RESTCONF-specific settings:

- **restconf-logging:hidden-http-methods** - HTTP requests (and
    associated HTTP responses) are not logged if request's HTTP method
    is set to one of the methods in this list. Names of the HTTP methods
    must be specified using upper-case format.
- **restconf-logging:hidden-http-headers** - List of HTTP headers
    (names of the headers) which content is hidden in the logs. Names of
    the HTTP headers are not case-sensitive.

Global settings that are common in all logging brokers:

- **hidden-types** - Value of leaf or leaf-list that uses one of these
    types is hidden in the logs using asterisk characters. It can be
    used for masking of passwords or other confidential data from logs.

### Initial Configuration

By default, all logging brokers are disabled and logging is disabled on
all devices, the user must explicitly specify a list of devices for
which per-device logging is enabled. Also, RESTCONF-specific filtering
is not configured, all HTTP requests and responses are fully logged, no
content is dismissed.

Initial logging configuration can be adjusted by adding the
'loggingController' configuration into the
'config/lighty-uniconfig-config.json' file. The structure of this
configuration section conforms YANG structure that is described by the
'logging' and 'restconf-logging' modules, it is possible to copy the
state of the Operational datastore under 'logging-status' into the
'loggingController' root JSON node.

The next JSON snippet shows the sample configuration
'loggingController', logging brokers 'netconf\_messages' and
'netconf\_notifications' are enabled; the 'netconf\_messages' broker is
enabled for all devices while 'netconf\_notifications' is enabled only
for 'xr6' and 'xr7' devices.

!!!
If unknown parameters are specified in a configuration file, they will be
ignored and a warning, that the corresponding parameter
was ignored, will be logged.
!!!

```json
{
  "loggingController": {
    "broker": [
      {
        "broker-identifier": "netconf_messages",
        "is-logging-broker-enabled": true,
        "is-logging-enabled-on-all-devices": true
      },
      {
        "broker-identifier": "netconf_notifications",
        "is-logging-broker-enabled": true,
        "is-logging-enabled-on-all-devices": false,
        "enabled-devices": [
          "xr7",
          "xr6"
        ]
      }
    ]
  }
}
```

### Controlling of Logging Using RPC Calls

Since logging settings are stored in the Operational datastore, it is
possible to adjust these settings on runtime only using RPC calls. The
following subsections describe available RPCs.

#### Enable Logging Broker

- An RPC is used for enabling the logging broker. The enabled logging
    broker is available to write logs.
- The input contains only the name of the the logging broker,
    'broker-identifier'.

**Example: - Enable logging broker with the identifier 'restconf'**

```bash
curl --location --request POST 'http://localhost:8181/rests/operations/logging:enable-logging' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "broker-identifier": "restconf"
    }
}'
```

The output shows a positive response given the broker was previously in
a disabled state:

```json
{
    "output": {
        "message": "Successfully updated logging broker [restconf]",
        "status": "complete"
    }
}
```

#### Disable Logging Broker

- An RPC is used for turning off the logging broker. A disabled
    logging broker doesn't write any logs despite other settings.
- The input contains only the name of the the logging broker,
    'broker-identifier'.

**Example: - Disabling the logging broker with the identifier 'restconf'**

```bash
curl --location --request POST 'http://localhost:8181/rests/operations/logging:disable-logging' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "broker-identifier": "restconf"
    }
}'
```

The output shows a positive response given the broker was previously in
an enabled state:

```json
{
    "output": {
        "message": "Successfully updated logging broker [restconf]",
        "status": "complete"
    }
}
```

#### Enable Default Device Logging

- An RPC is used for setting the default device logging to 'true',
    logs will be written for all devices without filtering any logs
    based on their node ID.
- The input contains only the name of the the logging broker,
    'broker-identifier'.
- Invocation of this RPC causes clearing of the leaf-lest
    'enabled-devices'.

**Example: - Enable default device logging in the 'netconf\_messages'
logging broker**

```bash
curl --location --request POST 'http://localhost:8181/rests/operations/logging:enable-default-device-logging' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "broker-identifier": "netconf_messages"
    }
}'
```

The output shows a positive response given the broker was previously in
a disabled state:

```json
{
    "output": {
        "message": "Successfully updated logging broker [netconf_messages] default logging behaviour",
        "status": "complete"
    }
}
```

#### Disable Default Device Logging

- An RPC is used for setting default device logging to 'false', logs
    will be written only for devices that are named in the leaf-list
    'enabled-devices'. If the leaf-list 'enabled-devices' doesn't
    contain a node ID, then logging in the corresponding logging broker
    is effectively turned off.
- The input contains only the name of the the logging broker,
    'broker-identifier'.

**Example: - Disable default device logging in 'netconf\_messages' logging
broker**

```bash
curl --location --request POST 'http://localhost:8181/rests/operations/logging:disable-default-device-logging' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "broker-identifier": "netconf_messages"
    }
}'
```

The output shows a positive response given the broker was previously in
an enabled state:

```json
{
    "output": {
        "message": "Successfully updated logging broker [netconf_messages] default logging behaviour",
        "status": "complete"
    }
}
```

#### Enable Device Logging

- An RPC is used for enabling logging of specified devices that are
    identified by node IDs.
- The input contains the name of the the logging broker
    'broker-identifier' and a list of node IDs called 'device-list'.

**Example: - Enable logging for devices with node IDs: 'node1', 'node2',
and 'node3' in the 'netconf\_events' logging broker**

```bash
curl --location --request POST 'http://localhost:8181/rests/operations/logging:enable-device-logging' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "broker-identifier": "netconf_events",
        "device-list": [
            "node1",
            "node2",
            "node2"
        ]
    }
}'
```

The output shows a positive response:

```json
{
    "output": {
        "message": "Successfully updated logging broker [netconf_events]",
        "status": "complete"
    }
}
```

#### Disable Device Logging

- An RPC is used for turning off logging of specified devices that are
    identified by node IDs.
- The input contains the name of the the logging broker
    'broker-identifier' and a list of node IDs called 'device-list'.

**Example: - Disable logging for device with node ID 'node1' in the
'netconf\_events' logging broker**

```bash
curl --location --request POST 'http://localhost:8181/rests/operations/logging:disable-device-logging' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "broker-identifier": "netconf_events",
        "device-list": [
            "node1"
        ]
    }
}'
```

The output shows a positive response:

```json
{
    "output": {
        "message": "Successfully updated logging broker [netconf_events]",
        "status": "complete"
    }
}
```

#### Setting Global Hidden Types

- An RPC is used for setting identifiers of hidden YANG type
    definitions. Values of leaves and leaf-lists that are described by
    these types are masked in the output logs.
- This RPC overwrites all already configured hidden types. An empty
    list of hidden types disables filtering of data values.
- Filtering of values applies to all logs, including RESTCONF logs.

**Example: - Setting 3 hidden types**

```bash
curl --location --request POST 'http://localhost:8181/rests/operations/logging:set-global-hidden-types' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "hidden-types": [
            "types:password",
            "types:encrypted",
            "types:hash"
        ]
    }
}'
```

The output shows a positive response:

```bash
{
    "output": {
        "message": "Global logging controller configuration has been successfully updated",
        "status": "complete"
    }
}
```

#### Setting Hidden HTTP Headers

- An RPC is used for overwriting the list of HTTP headers which
    content is masked in the output of the RESTCONF logs.
- This RPC modifies behavior of only the 'restconf' logging broker.
- HTTP headers in both requests and responses are masked.
- The list of hidden HTTP headers denotes header identifiers.
- The identifier of 'hidden' HTTP header still shows in the output
    logs, however, the content of such header is replaced by asterisk
    characters.

**Example: - Hiding content of 'Authorization' and 'Cookie' HTTP headers**

```bash
curl --location --request POST 'http://localhost:8181/rests/operations/restconf-logging:set-hidden-http-headers' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "hidden-http-headers": [
            "Authorization",
            "Cookie"
        ]
    }
}'
```

A positive response is shown in the output:

```json
{
    "output": {
        "message": "List of hidden HTTP headers have been successfully updated",
        "status": "complete"
    }
}
```

#### Setting Hidden HTTP Methods

- An RPC is used for overwriting the list of HTTP methods. RESTCONF
    communication, that may include invocation of hidden HTTP methods,
    is not displayed in the output logs.
- Both requests and responses with hidden HTTP methods are not written
    to the log files.
- This RPC modifies behavior of only 'restconf' logging behaviour.

**Example: - Hiding GET and PATCH communication in the RESTCONF logs**

```bash
curl --location --request POST 'http://localhost:8181/rests/operations/restconf-logging:set-hidden-http-methods' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "hidden-http-methods": [
            "GET",
            "PATCH"
        ]
    }
}'
```

A positive response is shown in the output:

```json
{
    "output": {
        "message": "List of hidden HTTP methods have been successfully updated",
        "status": "complete"
    }
}
```
