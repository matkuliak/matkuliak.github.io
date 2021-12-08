FAQs
====

* * * * *

**What is the datastore used in FRINX UniConfig ?**

Uniconfig uses a custom in memory database which is part of MD-SAL and
it is a very fast storage for YANG modeled data. UniConfig uses
datastore only for caching data in the scope of a single transaction.
For persistence purposes, UniConfig uses PostgreSQL database.

* * * * *

**Are service instances stored in the UniConfig layer of FRINX ?**

Only the „outputs“ of a service are stored and managed by UniConfig
(e.g. service generates bgp config for 10 devices, which is pushed into
UniConfig). The services themselves are responsible for managing their
configuration/operational state and rely on the same database to store
configuration or operational data.

* * * * *

**How does FRINX deal with model changes ?**

OpenConfig models are compiled as part of the UniConfig and because of
this reason it is possible to change these models only before
compilation. On the other side, NETCONF models can be dynamically loaded
from device and also manually updated using dedicated RPC:

<https://docs.frinx.io/frinx-uniconfig/UniConfig/user-guide/network-management-protocols/uniconfig_netconf/netconf-intro.html#registration-or-refreshing-of-netconf-cache-repository-using-rpc>

* * * * *

**Does FRINX provide auto rollback on all affected devices, when a
transaction fails on one or more devices ?**

Yes, all onboarded devices have full rollback implemented. But it is
also possible to disable auto-rollback in UniConfig, so that
successfully configured devices will keep their configuration.

* * * * *

**Is it possible to show the differences between the actual device
configuration and the operational datastore while synchronizing
configuration into FRINX ?**

Yes, it is possible. To achieve this follow these steps:
:   -   1. sync (update operational)
    -   2. show diff
    -   3.  drop the changes from device by replacing operational with
            config

* * * * *

**Is any NETCONF device fully supported, or must OpenConfig be mapped to
netconf as well ?**

You can either use the native device models (via UniConfig native) or
use the existing translation units between OpenConfig and vendor models.

* * * * *

**Are the libraries that are used to access the Config Data Store model
driven ?**

UniConfig has a DataBroker interface and a concept of
InstanceIdentifier. Those are the model driven APIs for data access.

*More info:*
<https://wiki.opendaylight.org/view/OpenDaylight_Controller:MD-SAL:Concepts>

* * * * *

**What would an access to the configuration data store look like in code
?**

A: Just to demonstrate API, in this example InterfaceConfigurations is
read from CONF DS and put back to CONF DS.

> ``` {.sourceCode .java
>       :linenos:}
> ReadWriteTransaction rwTx = dataBroker.newReadWriteTransaction();
> InstanceIdentifier`<InterfaceConfigurations>` iid = InstanceIdentifier.create(InterfaceConfigurations.class);
> InterfaceConfigurations ifcConfig = xrNodeReadTx.read(LogicalDatastoreType.CONFIGURATION, iid).checkedGet();
> rwTx.put(LogicalDatastoreType.CONFIGURATION, iid, ifcConfig);
> rwTx.submit();
> ```
>
> > B: In this example InterfaceConfigurations is read from OPER DS.
> >
> > ``` {.sourceCode .java
> >      :linenos:}
> > ReadWriteTransaction rwTx = dataBroker.newReadWriteTransaction();
> > InstanceIdentifier`<InterfaceConfigurations>` iid = InstanceIdentifier.create(InterfaceConfigurations.class);
> > InterfaceConfigurations ifcConfig = xrNodeReadTx.read(LogicalDatastoreType.OPERATIONAL, iid).checkedGet();
> > ```

* * * * *

**Is it possible in FRINX to run transaction on two disjunct sets of
devices simultaneously ?**

UniConfig supports build-and-commit model using which it is possible to
configure devices in the isolated transactions and commit them in
parallel. If there are some conflicts between configured sets of
devices, then the second transaction that is committed, will fail
(however, it cannot happen on disjunct sets of devices).

* * * * *

**What access control measures does FRINX offer ?**

FRINX UniConfig supports local authentification, password
authentification, public key authentification Token authentification,
RADIUS based authentification and subtree based authentification via AAA
Shiro project.

* * * * *

**How does FRINX report problems with device interaction ?**

If a device can not be reached during a UniConfig transaction (after
trying reestablishing the connection) a timeout will occur and the cause
for the transaction failure will be reported. UniConfig also uses
keepalive messages for continuous verification of connection to devices
(both using NETCONF and CLI management protocols).

* * * * *

**Is it possible to backup configuration ?**

UniConfig stores all committed configuration of devices, templates, and
snapshots in the PostgreSQL database. We suggest to use existing
techniques for backup that are also provided by PostgreSQL.

* * * * *

**Is it possible to enforce policies over configuration changes ?**

All customer specific validations and policy enforcements can be
implemented in layers above UniConfig

* * * * *

**In which languages are the libraries to access FRINX written ?**

UniConfig is written in JAVA and Kotlin which can use data objects
generated from YANG. RESTful API (RESTCONF) can be used with language
that implements REST client (for example, Python).

* * * * *

**Does FRINX detect if a cluster node is down on its own or does it rely
on a high availability framework ?**

UniConfig instance is stateless - it doesn’t persist any configuration
in its datastore (PostgreSQL is used for persistence) and it doesn’t
keep permanent connections (connections to devices are created on-demand
in the transaction). Because of the stateless architecture, UniConfig
instances in the ‘cluster’ don’t have to communicate with each other and
they don’t require any coordination. You must only keep in mind that
requests that belong to the same transaction must be forwarded to the
same UniConfig backend - for this purpose you can use any HA component
that supports sticky sessions based on cookies (such as HA-proxy or
Traefik).

* * * * *

**Is it possible for FRINX to report problems to a network monitoring
system ?**

FRINX UniConfig can propagate NETCONF notifications and internal
UniConfig notifications or data-change-events from web sockets on
Northbound API.

* * * * *

**Is it possible to do additional logging on the logging provided by
UniConfig ?**

Yes it is. Each component writes logs at different verbosity levels of
logging (ERROR, WARN, INFO, DEBUG, TRACE). We are using the logback
framework for logging of messages - logging can be adjusted by
modification of config/logback.xml file in the standard way. This file
can be updated also on runtime. The second approach for adjusting of
logging of some specific components is using logging controller:
<https://docs.frinx.io/frinx-uniconfig/UniConfig/user-guide/operational-procedures/logging/logging.html>

* * * * *

**Where do I find the status of the device and where do I find error
messages, when mounting does not work ?**

Mounting/unmounting process is done automatically - device is mounted
when UniConfig must read/write some data from/to device and device is
automatically unmounted at the end of the transaction if no other
transaction is using the same mountpoint. Users should not care about
the mounting process since it is transparent - it is useful only for
debugging purposes. To get status of the mounting process for all
devices in the system, issue following request (it will show status as
well as last connect attempt cause):

CLI devices

> ``` {.sourceCode .bash}
> curl --location --reQuestionuest GET 'http://localhost:8181/rests/data/network-topology:network-topology/topology=cli?content=nonconfig' \
> --header 'Authorization: Basic YWRtaW46YWRtaW4=' \
> --header 'Accept: application/json' \
> --header 'Content-Type: application/json'
> ```

NETCONF devices

> ``` {.sourceCode .bash}
> curl --location --reQuestionuest GET 'http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf?content=nonconfig' \
> --header 'Authorization: Basic YWRtaW46YWRtaW4=' \
> --header 'Accept: application/json' \
> --header 'Content-Type: application/json'
> ```

* * * * *

**What does installation and mounting exactly do ?**

Mounting of device:
:   -   1. Opening IO session to device (TCP session with SSH and/or
        NETCONF on top of SSH session).
    -   2.  Exposing mountpoint that can be used from internal API and
            RESTCONF API for interaction with device.

Installation of device contains internally also code for mounting of device:
:   -   1. Opening internal transaction
    -   2. Mounting of device with input parameters (CLI / NETCONF)
    -   3. Syncing configuration from device
    -   4. Writing configuration and mount information into database
    -   5. Unmounting device
    -   6.  Committing transaction

* * * * *

**Why I can not mount Junos device on UniConfig ?**

> **note**

If mounting Junos devices is not possible and UniConfig gives response :

``` {.sourceCode .guess}
2020-03-23 03:26:07,174 ERROR [default-pool-5-2] (org.opendaylight.netconf.sal.connect.netconf.NetconfDevice) - RemoteDevice{junos}: Initialization in sal failed, disconnecting from device
java.lang.IllegalStateException: Cannot build Netconf device schema context.
at org.opendaylight.netconf.cache.impl.utils.NetconfSchemaContextFactory.buildSchemaContext(NetconfSchemaContextFactory.javanswer:64)
at org.opendaylight.netconf.cache.model.SchemaRepositoryRefs.buildSchemaContext(SchemaRepositoryRefs.javanswer:68)
at org.opendaylight.netconf.sal.connect.netconf.NetconfDevice$SchemaSetup.run(NetconfDevice.javanswer:515)
at java.util.concurrent.Executors$RunnableAdapter.call(Executors.javanswer:511)
at com.google.common.util.concurrent.TrustedListenableFutureTask$TrustedFutureInterruptibleTask.runInterruptibly(TrustedListenableFutureTask.javanswer:125)
at com.google.common.util.concurrent.InterruptibleTask.run(InterruptibleTask.javanswer:57)
at com.google.common.util.concurrent.TrustedListenableFutureTask.run(TrustedListenableFutureTask.javanswer:78)
at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.javanswer:1149)
at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.javanswer:624)
at io.netty.util.concurrent.FastThreadLocalRunnable.run(FastThreadLocalRunnable.javanswer:30)
at java.lang.Thread.run(Thread.javanswer:748)
Caused by: java.lang.IllegalStateException: There are not any sources left for building of schema context.
at org.opendaylight.netconf.cache.impl.utils.NetconfSchemaContextFactory.setUpSchema(NetconfSchemaContextFactory.javanswer:114)
at org.opendaylight.netconf.cache.impl.utils.NetconfSchemaContextFactory.buildSchemaContext(NetconfSchemaContextFactory.javanswer:61)
... 10 more
```

It is necessary to set up on Junos device netconf session compliant to
RFC and Yang schemas (rfc-compliant, yang-compliant)

``` {.sourceCode .bash}
> configure
# set system services netconf rfc-compliant
# set system services netconf yang-compliant
# commit
```
