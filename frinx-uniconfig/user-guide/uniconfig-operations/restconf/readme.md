# UniConfig - Sending and receiving data (RESTCONF)

## Overview

RESTCONF is described in [RESTCONF RFC
8040](https://tools.ietf.org/html/rfc8040). Simple said, RESTCONF
represents a REST API to access datastores and UniConfig operations.

### Datastores

There are two datastores:

1. **Config**: Contains data representing the intended state, it is
    possible to read and write it via RESTCONF.
2. **Operational**: Contains data representing the actual state, it is
    possible to only read it via RESTCONF.

!!!
Each request must start with the URI */rests/*. By default,
RESTCONF listens on port 8181 for HTTP requests.
!!!

### REST Operations

RESTCONF supports: **OPTIONS**, **GET**, **PUT**, **POST**, **PATCH**,
and **DELETE** operations. Request and response data can either be in
the XML or JSON format.

- XML structures according to YANG are defined at:
    [XML-YANG](https://tools.ietf.org/html/rfc6020).
- JSON structures are defined at:
    [JSON-YANG](https://tools.ietf.org/html/draft-lhotka-netmod-yang-json-02).

Data in the request must set the **Content-Type** field correctly in the
HTTP header with the allowed value of the media type. The media type of
the requested data has to be set in the **Accept** field. Get the media
types for each resource by calling the OPTIONS operation.

Most of the paths use [Instance
Identifier](https://wiki.opendaylight.org/view/OpenDaylight_Controller:MD-SAL:Concepts#Instance_Identifier).
**\<identifier\>** is used in the explanation of the operations and has
to keep these rules:

- Identifier must start with \<moduleName\>:\<nodeName\>\> where
    \<moduleName\> is a name of the YANG module and \<nodeName\> is the
    name of a node in the module. If the next node name is placed in the
    same namespace like the previous one, it is sufficient to just use
    \<nodeName\> after the first definition of
    \<moduleName\>:\<nodeName\>. Each \<nodeName\> has to be separated
    by /.

\<nodeName\> can represent a data node which is a list node,
container, leaf, or leaf-list YANG built-in type. If the data node is a
list, there must be defined ordered keys of the list behind the data
node name, for example, \<nodeName\>=\<valueOfKey1\>,\<valueOfKey2\>. ..

!!!
The following example shows how reserved characters are percent-encoded within a key value. The value of "key1" contains a comma, single-quote, double-quote, colon, double-quote, space, and forward slash (,'":" /). Note that double-quote is not a reserved character and does not need to be percent-encoded. The value of "key2" is the empty string, and the value of "key3" is the string "foo". 

Example URL: /rests/data/example-top:top/list1=%2C%27"%3A"%20%2F,,foo
!!!

- The format \<moduleName\>:\<nodeName\> has to be used in this case
    as well. Module A has node A1. Module B augments node A1 by adding
    node X. Module C augments node A1 by adding node X. For clarity, it
    has to be known which node is X (for example: C:X).

## Mount point

The purpose of **yang-ext:mount** container is to access southbound
mountpoint, when the node is already installed in Uniconfig (After
install-node RPC). It exposes operations for reading device data which
can only be done under connection-specific topology (cli/netconf) with
defined node-id in URI. In this case, the URI has to be in the format
\<identifier\>/**yang-ext:mount**/\<identifier\>. The first
\<identifier\> is the path to a mount point and the second
\<identifier\> is the path to subtree behind the mount point. An URI can
end in a mount point itself by using \<identifier\>/yang-ext:mount. In
this case, if there is no content parameter, whole operational and
configuration data will be read.

**Examples of retrieving data behind yang-ext:mount**

In this request, we are using parameter **content=config**, this means
we are reading candidate NETCONF datastore. Value **config** of
parameter content is translated into get-config NETCONF RPC.

```bash RPC Request, Yang-ext:mount using content=config parameter
curl --location --request GET 'http://localhost:8181/rests/data/network-topology:network-topology/topology=cli/node=R1/yang-ext:mount?content=config' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
    "frinx-oam:oam": {
        ...
    },
    "frinx-evpn:evpn": {
        ...
    },
    "frinx-openconfig-interfaces:interfaces": {
        "interface": [
            {
                "name": "Loopback123",
                "config": {
                    "type": "iana-if-type:softwareLoopback",
                    "enabled": true,
                    "description": "commitbadconfig",
                    "name": "Loopback123"
                }
            }
            ...
        ]
    },
    "frinx-logging:logging": {
        ...
    },
    "frinx-snmp:snmp": {
        ...
    },
    "frinx-openconfig-network-instance:network-instances": {
        "network-instance": [
            {
                "name": "default",
                "protocols": {
                    "protocol": [
                        {
                            "identifier": "frinx-openconfig-policy-types:STATIC",
                            "name": "default",
                            "config": {
                                "identifier": "frinx-openconfig-policy-types:STATIC",
                                "name": "default"
                            }
                        }
                    ]
                },
                "config": {
                    "name": "default",
                    "type": "frinx-openconfig-network-instance-types:DEFAULT_INSTANCE"
                }
            }
        ]
    }
}
```

In this reqeust we are using parameter **content=nonconfig**, this means
we are reading running NETCONF datastore. Value **nonconfig** is
translated into get NETCONF RPC. We can compare it with data directly
from device using show running-config command.

```bash RPC Request, Yang-ext:mount using content=nonconfig parameter
curl --location --request GET 'http://localhost:8181/rests/data/network-topology:network-topology/topology=cli/node=R1/yang-ext:mount?content=nonconfig' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
    "frinx-openconfig-platform:components": {
        "component": [
            {
                "name": "OS",
                "state": {
                    "software-version": "5.3.4[Default]",
                    "name": "OS",
                    "id": "IOS XR"
                },
                "config": {
                    "name": "OS"
                }
            }
        ]
    },
    "frinx-oam:oam": {
        "cfm": {
            "config": {
                "enabled": false
            }
        }
    },
    "frinx-configuration-metadata:configuration-metadata": {
        "last-configuration-fingerprint": "Wed Sep 29 09:55:23 2021"
    },
    "frinx-evpn:evpn": {
        ...
    },
    "frinx-openconfig-interfaces:interfaces": {
        ...
    },
    "frinx-openconfig-lldp:lldp": {
        "config": {
            "system-name": "XR5"
        }
    },
    "frinx-logging:logging": {
        ...
    },
    "frinx-snmp:snmp": {
        ...
    },
    "frinx-openconfig-network-instance:network-instances": {
       ...
    }
}
```


**Examples of invocation of yang actions behind yang-ext:mount**.

```bash RPC Request, Invocation of yang action -> Clear VRRP global statistics
curl --location --request POST 'http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=R1/yang-ext:mount/clear:clear/clear:statistics/vrrp/global' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
}
```


**Invocation of yang action -\> List available firmware packages on disk**

```bash RPC Request, Invocation of yang action -> List available firmware packages on disk
curl --location --request POST 'http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=R1/yang-ext:mount/system:system/firmware/list' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
    "output" : {
        "packages" : [
            {
                "name" : "Name of the package"
            }
            ...
        ]
    }
}
```


**Invocation of yang action -\> Erase running-config-then load**

```bash RPC Request, Invocation of yang action -> Erase running-config-then load
curl --location --request POST 'http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=R1/yang-ext:mount/system:erase/running-config-then/load' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "file": "/home/admin/start_config.cfg"
    }
}'
```

```xml RPC Response, Status: 200
<output xmlns="namespace">
    <status>Erasing config and restarting services</status>
</output>
```

!!!
To completely understand installing of node see [Device installation](https://docs.frinx.io/frinx-uniconfig/user-guide/network-management-protocols/uniconfig-installing/).
!!!

## HTTP methods

#### OPTIONS /rests

- Returns the XML description of the resources with the required
    request and response media types in Web Application Description
    Language (WADL).

#### GET /rests/data/\<identifier\>?content=config

- Returns a data node from the Config datastore.
- \<identifier\> points to a data node that must be retrieved.

#### GET /rests/data/\<identifier\>?content=nonconfig

- Returns the value of the data node from the Operational datastore.
- \<identifier\> points to a data node that must be retrieved.

#### GET /rests/data/\<identifier\>

- Returns a data node from both Config and Operational datastores. The
    outputs from both datastores are merged into one output.
- \<identifier\> points to a data node that must be retrieved.

#### PUT /rests/data/\<identifier\>

- Updates or creates data in the Config datastore and returns the
    state about success.
- \<identifier\> points to a data node that must be stored.
- Content type does not have to be specified in URI - it can only be
    the Configuration datastore.

```bash Example:
curl --location --request PUT 'http://<uniconfig-ip>:8181/rests/data/module1:foo/bar' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '<bar>
  …
</bar>'
```

```bash Example with mountpoint:
curl --location --request PUT 'http://<uniconfig-ip>:8181/rests/data/module1:foo1/foo2/yang-ext:mount/module2:foo/bar' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '<bar>
  …
</bar>'
```

#### POST /rests/data/\<identifier\>

- Creates the data if it does not exist in the Config datastore, and
    returns the state about success.
- \<identifier\> points to a data node where data must be stored.
- The root element of data must have the namespace (data is in XML) or
    module name (data is in JSON).

```bash Example:
curl --location --request POST 'http://<uniconfig-ip>:8181/rests/data/<identifier>' \
--header 'Accept: application/xml' \
--header 'Content-Type: application/xml' \
--data-raw '<bar xmlns=“module2namespace”>
  …
</bar>'
```

```bash Example with mount point:
curl --location --request POST 'http://<uniconfig-ip>:8181/rests/data/module1:foo1/foo2/yang-ext:mount/module2:foo' \
--header 'Accept: application/xml' \
--header 'Content-Type: application/xml' \
--data-raw '<bar xmlns=“module2namespace”>
  …
</bar>'
```

#### POST /rests/data

- Creates the data if it does not exist under data root.
- In the following example, the 'toaster' module is the root container
    in YANG (it doesn't have any parent). This example also makes it
    clear that URI doesn't contain 'toaster' node in comparison to a PUT
    request that must contain the name of the created node in URI.

```bash Example:
curl --location --request POST 'http://localhost:8181/rests/data' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "toaster:toaster": {
        "toaster:toasterManufacturer": "General Electric",
        "toaster:toasterModelNumber": "123",
        "toaster:toasterStatus": "up"
    }
}'
```

#### DELETE /rests/data/\<identifier\>

- Removes the data node in the Config datastore and returns the state
    about success.
- \<identifier\> points to a data node that must be removed.

#### PATCH /rests/data/\<identifier\>

- The patch request merges the contents of the message-body with the
    target resource in the Configuration datastore (content-type query
    parameter is not specified).
- \<identifier\> points to a data node on which PATCH operations is
    invoked.
- This request is implemented by Plain PATCH functionality, see more
    details on the following page: [RFC-8040 documentation - Plain PATCH
    operation](https://tools.ietf.org/html/rfc8040#section-4.6.1).
- Plain patch can be used to create or update, but not delete, a child
    resource within the target resource. Any pre-existing data which is
    not explicitly overwritten will be preserved. This means that if you
    store a container, its child entities will also merge recursively.

The following example shows the PATCH request used for modification of
Ethernet interface IP address and two connection settings. Note that
other settings under system:system container are left untouched
including other leaves under 'connection' container and 'ethernet' list
item.

```bash RPC Request
curl --location --request PATCH 'http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=n1/configuration/system:system' \
--header 'Content-Type: application/json' \
--data-raw '{
    "system:system": {
        "wan": {
            "endpoint": {
                "interfaces": {
                    "ethernet": [
                        {
                            "name": "ethernet1",
                            "inet": {
                                "address": "192.168.10.1/24"
                            }
                        }
                    ]
                }
            }
        },
        "connection": {
            "syn-flood-check": true,
            "ack-flood-check": true
        }
    }
}'
```

#### POST /rests/operations/\<moduleName\>:\<rpcName\>

- Invokes RPC on the specified path.
- \<moduleName\>:\<rpcName\> - \<moduleName\> is the name of the
    module and \<rpcName\> is the name of the RPC in this module.
- The Root element of the data sent to RPC must have the name “input”.
- The result has the status code and optionally retrieved data having
    the root element “output”.

```bash Example:
curl --location --request POST 'http://<uniconfig-ip>:8181/rests/operations/module1:fooRpc' \
--header 'Content-Type: application/xml' \
--header 'Accept: application/xml' \
--data-raw '<input>
  …
</input>'
```

The answer from the server could be:

```bash Response
<output>
  …
</output>
```

```bash RPC Request, An example using a JSON payload:
curl --location --request POST 'http://localhost:8181/rests/operations/toaster:make-toast' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "toaster:toasterDoneness": "10",
        "toaster:toasterToastType": "wheat-bread"
    }
}'
```

!!!
**GET /rests/operations** request can be used to retrieve all
available RPCs that are registered in distribution.

More information is available in the [RESTCONF RFC 8040](https://tools.ietf.org/html/rfc8040).
!!!

#### POST /rests/data/\<path-to-operation\>

- Invokes action on the specified path in the data tree.
- Placeholder \<path-to-operation\> represents data path to operation
    definition that is specified under composite data schema node in
    YANG (only containers and lists may contain action definition).
- Content query parameter doesn't have to be specified (it will be
    ignored), action is represented equally in Operational and Config
    datastore.
- Both RFC-8040 (YANG 1.1) and TAIL-F actions are supported. TAIL-F
    actions can be placed in both YANG 1.0 and YANG 1.1 schemas. There
    aren't any differences in the invocation of these types of actions
    using RESTCONF API.
- The body of the action invocation request may contain a root 'input'
    container. If the action definition has no specified input
    container, it is not required to specify the body in the request.
- The response contains the status code and optionally retrieved data
    having the root element 'output'.
- Currently, FRINX UniConfig only supports invocation of actions under
    NETCONF mountpoint, \<path-to-operation\> must contain
    'yang-ext:mount' container.
- Structure of 'input' and 'output' elements are the same as the
    structure of these containers when we invoke YANG RPC.

Assume the following YANG snippet with root container named
'interfaces':

```
container interfaces {
    action compute-stats {
        input {
            leaf month {
                type string;
            }
            leaf year {
                type uint16;
            }
            leaf percentile {
                type uint8;
            }
        }
        output {
            leaf avg-rx-kbps {
                type uint32;
            }
            leaf avg-tx-kbps {
                type uint32;
            }
        }
    }
}
```

Invocation of the action named 'compute-stats' that is placed under the
'interfaces' container of NETCONF mountpoint:

```bash Request
curl --location --request POST 'http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=dev/yang-ext:mount/interfaces:interfaces/compute-stats' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "month": "April",
        "year": 2018,
        "percentile": 50
    }
}'
```

```json The response body:
{
    "interfaces:output": {
        "avg-rx-kbps": 14524,
        "avg-tx-kbps": 47787
    }
}
```

!!!
Difference between RPCs and actions: Actions are bound to a data tree
and they can be placed under containers and lists (they cannot be
specified as root entities in YANG schema). RPCs are not placed in the
data tree and for this reason, they can only be specified as root
entities in the YANG schema.
!!!

## Selecting Data

For selecting and identifying data is good to use query parameter
**fields**. This parameter has to be used only with the **GET** method.
The response body is output filtered by field-expression as the value of
fields parameter.

### Fields

The response body is the output filtered by the field-expression as a
value of the fields parameter.

!!!
The example of using the fields parameter: **path?fields=field\_expression**
!!!

There are several rules, that need to be followed:

1. For filtering more than one field of the same parent, ";" needs to
    be used. Example : **path?fields=field1;field2**, where field1 and
    field2 has the same parent, which is the very last part of the path.
2. For nesting, "/" needs to be used. Example :
    **path?fields=field1;pathField/field2**, where field1 and field2 has
    not the same parent, but pathField is on the same level as field1.
3. Third character "(" , ")" is used to specify sub-selectors.
    Example: **path ? fields = pathField( field1; pathField2( field2; pathField3( field3 ) ) )**
    :   This is a different approach to do nesting, however, the
        difference between "(" and "/" is that once we use "/" for
        specifying some field, we cannot identify another field from the
        upper layers.

    Example: **path ? fields = pathField / field1; pathField2 / field2**
    :   This is the case where pathField1 and pathField2 have the same
        parent, this is not allowed, because once we use ";" it is
        expected to specify fields on the same layer as field1

Examples: With 2 approaches (nesting, sub-selecting)

Example of filtering the entire configuration of all interfaces (name, with the config):

```
 **Using sub-selectors**
 http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration?fields=frinx-openconfig-interfaces:interfaces(interface)&content=nonconfig

**Using nesting**
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration?fields=frinx-openconfig-interfaces:interfaces/interface/name;config&content=nonconfig
```

Example of filtering all names of interfaces and all names of configs of interfaces:

```
**Using sub-selectors**
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration?fields=frinx-openconfig-interfaces:interfaces(interface(name;config(name)))&content=nonconfig

**Using nesting**
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration?fields=frinx-openconfig-interfaces:interfaces/interface/name;config/name&content=nonconfig
```

Example of filtering all names of interfaces with type from the config of interfaces:

```
**Using sub-selectors**
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration?fields=frinx-openconfig-interfaces:interfaces(interface(name;config(type)))&content=nonconfig

**Using nesting**
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration?fields=frinx-openconfig-interfaces:interfaces/interface/name;config/type&content=nonconfig
```

## Filtering Data

For filtering data based on specific value is good to use
**jsonb-filter** query parameter. This parameter has to be used only
with the **GET** method.

### Jsonb-filter

Jsonb-filter is a query parameter that is used for filtering data based
on one or more parameters. This filter is an effective mechanism for
filtering a list of items. Using the jsonb-filter we can retrieve only
those list items that meet the defined conditions.

!!!
The example of using the jsonb-filter query parameter: **parent-path?jsonb-filter=expression**

PostgreSQL documentation: [JSON Functions and Operators](https://www.postgresql.org/docs/13/functions-json.html)
!!!

#### Jsonb-filter expression

The base expression must contain **path**, **operator** and **value**.
The jsonb-filter can contain one or more expressions joined with AND
(&&) or OR (||) operator. if the && operator is used it must be encoded.

**Path**

The path to the data that the users want to filter. The path can be:

1.  **Relative path**

In this case, the path must be prefixed with <**@**>. This path is relative to the **parent-path**

```
{@/description}
```

2.  **Absolute path**

In this case, a path must be prefixed with **\$**. This path must start with a top-level parent container

```
{$/Cisco-IOS-XR-ifmgr-cfg:interface-configurations/interface-configuration=act,Bundle-Ether1/description}
```

!!!
Sometimes especially absolute paths can contain a key of some item
with special characters. In this case it is necessary wrap this key in
a special syntax **(\#example-key-name)** and also encode these
wrapping symbols - **%28%23example-key-name%29**. If the key is a
composite key, it is necessary to wrap the whole key with these
symbols. If the user is not sure if the path contains special
characters, it is always recommended to use this special syntax.
!!!

**Single key:**

{\$/frinx-openconfig-interfaces:interfaces/interface=%28%23MgmtEth0/RP0/CPU0/0%29}

**Composite key:**

{\$/Cisco-IOS-XR-ifmgr-cfg:interface-configurations/interface-configuration=%28%23act,GigabitEthernet0/0/0/2%29}

**Operator**

When the path is constructed then the user can use one of the operators
in the table below

| Value/Predicate Description | Description |
| --- | --- |
| == | Equality operator |
| != | Non-equality operator |
| \<\> | Non-equality operator (same as !=) |
| \< | Less-than operator |
| \<= | Less-than-or-equal-to operator |
| \> | Greater-than operator |
| \>= | Greater-than-or-equal-to operator |
| true | Value used to perform a comparison with JSON true literal |
| false | Value used to perform a comparison with JSON false literal |
| null | Value used to perform a comparison with JSON null value |
| && | Boolean AND |
| \|\| | Boolean OR |
| ! | Boolean NOT |
| like\_regex | Tests whether the first operand matches the regular expression given by the second operand |
| starts with | Equality operator |
| exists | Equality operator |
| is unknown | Equality operator |


**Value**

The last element of the jsonb-filter expression is a value based on
which the user wants to filter the data.

#### Jsonb-filter examples

**1. Examples of using the relative paths in the jsonb-filter**

Example of filtering the list of interfaces based on the enabled
parameter where the **equality operator** is used as the operator

```
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration/frinx-openconfig-interfaces:interfaces/interface?jsonb-filter={@/config/enabled} == true&content=nonconfig
```

Example of filtering the list of interfaces based on the mtu parameter
where the **less-than** is used as the operator

```
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration/frinx-openconfig-interfaces:interfaces/interface?jsonb-filter={@/config/mtu} < 1600&content=nonconfig
```

Example of filtering the list of interfaces based on the name parameter
where the **like\_regex** is used as the operator

```
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration/frinx-openconfig-interfaces:interfaces/interface?jsonb-filter={@/config/name} like_regex "Bundle.*"&content=nonconfig
```

Example of filtering the list of interfaces where a combination of
expressions is used

```
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration/frinx-openconfig-interfaces:interfaces/interface?jsonb-filter=({@/config/name} != "Bundle-Ether1" %26%26 {@/config/name} starts with "Bundle-Ether") || ({@/config/name} like_regex "Gigabit.*" %26%26 {@/config/enabled} == true)&content=nonconfig
```

Example of filtering the list of interfaces where the **exists**
operator is used

```
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration/frinx-openconfig-interfaces:interfaces/interface?jsonb-filter=(exists({@/subinterfaces}))&content=nonconfig
```

**2. Example of using the absolute path in the jsonb-filter**

Example of filtering the list of interfaces based on the name parameter
where **equality operator** is used as the operator. Interface name
"GigabitEthernet0/0/0/2" is a key value that contains slashes. For this
reason, it is necessary to wrap this key into wrapping symbols
(\#GigabitEthernet0/0/0/) and also encode these symbols
%28%23GigabitEthernet0/0/0/2%29.

```
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration/frinx-openconfig-interfaces:interfaces/interface=GigabitEthernet0%2F0%2F0%2F2?jsonb-filter={$/frinx-openconfig-interfaces:interfaces/interface=%28%23GigabitEthernet0/0/0/2%29/config/enabled}==false&content=nonconfig
```

## Pagination

To further extend the ability to filter data according to our needs, we
can use pagination in the **GET** method.

There are 3 pagination parameters that can be used individually or in
combination with each other :

1.  **offset :** This parameter lets us choose on which list entry value
    we want data to start rendering.
2.  **limit :** Limit gives us the option to control how many node
    values are going to be displayed in our **GET** request.
3.  **fetch=count :** Used to obtain the amount of children nodes in a
    specific node.

!!!
Beware that pagination works only for list nodes.
!!!

The example of using individual pagination parameter:

```
http://127.0.0.1:8181/restconf/data/network-topology:network-topology/topology=uniconfig/node=iosxr/configuration/frinx-openconfig-interfaces:interfaces?offset=1
```

The example of using two pagination parameters simultaneously:

```
http://127.0.0.1:8181/restconf/data/network-topology:network-topology/topology=uniconfig/node=iosxr/configuration/frinx-openconfig-interfaces:interfaces?offset=1&limit=4
```

The example of using fetch count parameter:

```
http://127.0.0.1:8181/restconf/data/network-topology:network-topology/topology=uniconfig/node=iosxr/configuration/frinx-openconfig-interfaces:interfaces?fetch=count
```

The response body of fetch count parameter with a path from the previous
example:

```
6
```

## Sorting

This utility helps us to sort list data from **GET** request according
to our needs in **ascending** or **descending** order.

To sort some data, use a query parameter called **sortby** that will
include at least one identifier of child leaf and sort direction. The
first part of the value represents leaf identifier, the second part
enclosed in brackets represents sort direction ('asc' or 'desc'). If
there are multiple leaves based on which sorting is done, they are
separated by semicolon.

!!!
Sorting, just like pagination, can only be used on list nodes.
!!!

The example of using sortby parameter with 1 value (sorting by the value
of 'name' leaf):

```
http://127.0.0.1:8181/restconf/data/network-topology:network-topology/topology=uniconfig/node=iosxr/configuration/frinx-openconfig-interfaces:interfaces/interface?sortby=name(asc)
```

The example of using sortby parameter with 2 values (sorting by values
of 'name' and 'revision' leaves, in that order):

```
http://127.0.0.1:8181/restconf/data/network-topology:network-topology/topology=uniconfig/node=iosxr/configuration/frinx-openconfig-interfaces:interfaces/interface?sortby=name(asc);revision(desc);
```

The example of using sortby and pagination simultaneously:

```
http://127.0.0.1:8181/restconf/data/network-topology:network-topology/topology=uniconfig/node=iosxr/configuration/frinx-openconfig-interfaces:interfaces/interface?offset=1&limit=7&sortby=name(asc);type(asc);
```

It is possible to specify module-name as part of the leaf identifier.
Module-name must be specified only if there are multiple children leaves
with the same identifier but specified from different namespaces.
Example:

```
http://127.0.0.1:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=dev1/configuration/ietf-netconf-acm:nacm/rule-list=oper/rule?sortby=tailf-acm:context(asc)
```

In the case of union types specified on leaf nodes, sorting is done in
the blocks that are ordered by the following strategy:

1. leaves without value
2. empty type
3. boolean type
4. random numeric type
5. types that can be represented by JSON string

## Inserting

### Insert query parameter

The 'insert' query parameter can be used to specify how an item should be inserted within an list or leaf-list.
This parameter is only supported for the POST and PUT methods. It is also only supported if the target list or leaf-list
is marked as 'ordered-by user' in YANG model.

The allowed values for 'insert' query parameter:

| Value  | Description |
| --- | --- |
| first | Insert the new item as the new first entry. |
| last | Insert the new item as the new last entry (default value). |
| before | Insert the new item before the insertion point, as specified by the value of the 'point' query parameter. |
| after  | Insert the new data after the insertion point, as specified by the value of the "point" parameter. |

!!!
If the values 'before' or 'after' are used, then a 'point' query parameter for the 'insert' query parameter MUST
also be present.
!!!

### Point query parameter

The 'point' query parameter is used to specify the insertion point for an item that is being created or moved within an
'ordered-by user' list or leaf-list. Like the 'insert' query parameter, 'point' query parameter is only supported for
the POST and PUT methods and also if the target list or leaf-list is marked as 'ordered-by user' in YANG model.
The value of the 'point' query parameter is a string that indicates the key of the insertion point item. If the key
is composite, the key items must be separated by a comma.

### Examples

Next five examples show usage of 'insert' and 'point' query parameters for leaf-list. First example shows how leaf-list
looks before update. There are no differences in the use of the list and leaf-list.

#### List before update

```bash RPC Request
curl --location --request GET 'http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=xr5/configuration/frinx-openconfig-qos:qos/classifiers/classifier=test/terms/term=1/conditions/frinx-qos-extension:qos-group?content=nonconfig`' \
--header 'Content-Type: application/json' \
--header 'Content-Type: content=nonconfig' \
--header 'Accept: application/json'
```

```json RPC Response
{
    "frinx-qos-extension:qos-group": [
        1,
        2,
        3
    ]
}
```

#### Insert item at the top of the list

```bash RPC Request
curl --location --request PUT 'http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=xr5/configuration/frinx-openconfig-qos:qos/classifiers/classifier=test/terms/term=1/conditions/frinx-qos-extension:qos-group=4?insert=first' \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
--data-raw '{
    "frinx-qos-extension:qos-group": [
        4
    ]
}'
```

```json RPC Response, Updated list
{
    "frinx-qos-extension:qos-group": [
        4,
        1,
        2,
        3
    ]
}
```

#### Insert item at the bottom of the list

```bash RPC Request
curl --location --request PUT 'http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=xr5/configuration/frinx-openconfig-qos:qos/classifiers/classifier=test/terms/term=1/conditions/frinx-qos-extension:qos-group=4?insert=last' \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
--data-raw '{
    "frinx-qos-extension:qos-group": [
        4
    ]
}'
```

```json RPC Response, Updated list
{
    "frinx-qos-extension:qos-group": [
        1,
        2,
        3,
        4
    ]
}
```

#### Insert item after specific item

```bash RPC Request
curl --location --request PUT 'http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=xr5/configuration/frinx-openconfig-qos:qos/classifiers/classifier=test/terms/term=1/conditions/frinx-qos-extension:qos-group=4?insert=after&point=2' \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
--data-raw '{
    "frinx-qos-extension:qos-group": [
        4
    ]
}'
```

```json RPC Response, Updated list
{
    "frinx-qos-extension:qos-group": [
        1,
        2,
        4,
        3
    ]
}
```

#### Insert item before specific item

```bash RPC Request
curl --location --request PUT 'http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=xr5/configuration/frinx-openconfig-qos:qos/classifiers/classifier=test/terms/term=1/conditions/frinx-qos-extension:qos-group=4?insert=before&point=2' \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
--data-raw '{
    "frinx-qos-extension:qos-group": [
        4
    ]
}'
```

```json RPC Response, Updated list
{
    "frinx-qos-extension:qos-group": [
        1,
        4,
        2,
        3
    ]
}
```


## JSON Attributes

Node attributes can be encoded in JSON by wrapping all the attributes in
the '@' container and values or arrays in the '\#' JSON element. This
notation is inspired by one that is used in the 'js2xmlparser'
open-source tool (conversion between JSON and XML structures):
[js2xmlparser](https://github.com/michaelkourlas/node-js2xmlparser#readme)

RESTCONF supports both serialization and deserialization of attributes,
GET response shows all set attributes in the read data-tree and
PUT/POST/PLAIN PATCH methods can be used for the writing of data nodes
with attributes. Warning: attributes cannot be directly addressed using
RESTCONF URI that would contain the '@' element in the path, because
attributes are always bound to some data node, they are not represented
by distinct nodes in the data-tree.

Reserved '@' container may contain multiple attributes. Each attribute
is encoded in the same fashion as leaf nodes, there is an identifier of
the attribute and attribute value.

Format of the attribute that is defined in the [module]:

```
"[module]:[attribute-name]": [value]
```

Format of the attribute that is defined in the same module as the parent
data entity:

```
"[attribute-name]": [value]
```

### Example - leaf with attributes

Leaf without attributes:

```
{
  "sample-leaf": 3
}
```

The same leaf with set 2 attributes: 'm1:attribute-1' and
'm1:attribute-2':

```json
{
  "sample-leaf": {
    "@": {
      "m1:attribute-1": "value",
      "m1:attribute-2": 7
    },
    "#": 3
  }
}
```

### Example: Container with Attributes

A container without attributes:

```json
{
  "sample-container": {
    "nested-leaf-1": "str1",
    "nested-container": {
      "l1": 10,
      "l2": true
    }
  }
}
```

The same container with set 2 attributes: 'm1:switch' and
'm2:multiplier':

```json
{
  "sample-container": {
    "@": {
      "m1:switch": true,
      "m2:multiplier": 10
    },
    "nested-leaf-1": "str1",
    "nested-container": {
      "l1": 10,
      "l2": true
    }
  }
}
```

### Example: Leaf-list with Attributes

Leaf-list without attributes:

```json
{
  "sample-leaf-list": [10, 20, 30]
}
```

The same leaf with set 1 attribute: 'mx:split':

```json
{
  "sample-leaf-list": {
    "@": {
      "mx:split": true
    },
    "#": [10, 20, 30]
  }
}
```

### Example: Leaf-list Entry with Attributes

Leaf-list without attributes:

```json
{
  "sample-leaf-list": [10, 20]
}
```

Two leaf-list entries, leaf-list entry with value '10' has one attribute
with identifier 'm1:prefix'. The second leaf-list entry '20' doesn't
have any attributes assigned.

```json
{
  "sample-leaf-list": [
    {
      "@": {
        "m1:prefix": "arp-"
      },
      "#": 10
    },
    20
  ]
}
```

### Example: List with Attributes

List without attributes:

```json
{
  "sample-list": [
    {
      "key": "k1",
      "value": 1
    },
    {
      "key": "k2",
      "value": 2
    }
  ]
}
```

The same list with applied single attribute: 'constraints:length'.

```json
{
  "sample-list": {
    "@": {
      "constraints:length": 100
    },
    "#": [
      {
        "key": "k1",
        "value": 1
      },
      {
        "key": "k2",
        "value": 2
      }
    ]
  }
}
```

### Example: List Entry with Attributes

List with two list entries without attributes:

```json
{
  "sample-list": [
    {
      "key": "k1",
      "value": 1
    },
    {
      "key": "k2",
      "value": 2
    }
  ]
}
```

The same list entries, the first list entry doesn't contain any
attribute, but the second list entry contains 2 attributes: 'm1:switch'
and 'm2:multiplier'.

```json
{
  "sample-list": [
    {
      "key": "k1",
      "value": 1
    },
    {
      "key": "k2",
      "value": 2,
      "@": {
        "m1:switch": true,
        "m2:multiplier": 10
      }
    }
  ]
}
```

## Device Schema Filters

By default, all input and output data produced by RESTCONF for the
selected device is fully compliant with its YANG models. Any violation
of the YANG schema definitions will result in an error. Some of these
restrictions can be addressed by adding the 'schemaFilters'
configuration parameter for the RESTCONF.

### Configuration Options Overview

Following configuration options for 'schemaFilters' make RESTCONF
processing less restrictive:

### Configuration Example

The following example demonstrates how to enable schema filters for
selected extensions and make RESTCONF ignore unknown definitions and
definitions with a 'deprecated status' attribute.

```json
"schemaFilters": {
  "ignoredDataOnWriteByExtensions": [
    "tailf:hidden full"
  ],
  "hiddenDataOnReadByExtensions": [
    "tailf:hidden deprecated",
    "tailf:hidden debug"
  ],
    "ignoreUnsupportedDefinitionsOnWrite": true,
    "hideDeprecatedDefinitionsOnRead": true
}
```

### Unhide Parameter for READ Operation

RESTCONF supports the 'unhide' query parameter for the GET requests to
include hidden definitions into the response. This parameter value can
be populated with a comma-separated list of extensions to unhide or the
keyword 'all' to include all possible hidden definitions in the
response.

Example of using the 'unhide' parameter for the GET request.

**Using unhide with a list of extensions**

```
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=device/configuration?unhide=tailf:hidden debug,tailf:hidden deprecated
```

**Using unhide parameter to unhide all hidden definitions**

```
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=device/configuration?unhide=all
```


## Leafref validation
According to YANG standard there are constraints for leafrefs.
These constraints are not validated by default. Leafref validation
can be enabled using checkForReferences query parameter with value
set to true.

### Example:

#### Using leafref validation

```
curl --location --request DELETE 'http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=device/configuration/
> frinx-openconfig-interfaces:interfaces/interface=eth0?checkForReferences=true' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json'
```

#### Example output of failed validation

```json
{
    "errors": {
        "error": [
            {
                "error-message": "Leafref validation failed. Violated leafref constraint on leaf /network-topology/topology/node/configuration/interfaces/interface/name -
                                    node is referenced by leaf on path: /network-topology/topology/node/configuration/referencing/path,
                "error-tag": "invalid-value",
                "error-type": "protocol"
            }
        ]
    }
}
 ```

If checkForReferences parameter is set to false or is not provided UniConfig will
not perform leafref validation and there will be no leafref validation error.