Architecture
============

Pre-requisite reading
---------------------

-   Honeycomb design documentation:

<https://wiki.fd.io/view/Honeycomb>
<https://docs.fd.io/honeycomb/1.18.04/release-notes-aggregator/release_notes.html>

-   CLI plugin available presentations:

<https://www.dropbox.com/sh/ry2ru5vizv7st8u/AAAntbCRHb1yS_NmEpbXG1WBa?dl=0>

Building on honeycomb
---------------------

The essential idea behind the southbound plugins comes from Honeycomb.
Honeycomb defines, implements and uses the same pipeline and the same
framework to handle data. The APIs, some implementations and also SPIs
used in the southbound plugin's translation layer come from Honeycomb.
However, the southbound plugin creates multiple instances of Honeycomb
components and encapsulates them behind a mount point.

The following series of diagrams shows the evolution from Opendaylight
to Honeycomb and back into Opendaylight as a mountpoint:

High level Opendaylight overview with its concept of a Mountpoint:

[![ODL](ODL.png)](ODL.png)

High level Honeycomb overview:

[![HC](HC1.png)](HC1.png)

Honeycomb core (custom MD-SAL implementation) overview:

[![Honeycomb's core](HCsMdsal.png)](HCsMdsal.png)

How Honeycomb is encapsulated as a mount point in Opendaylight:

[![Honeycomb's core as mountpoint](cliMountpoint.png)](cliMountpoint.png)

### Major components

The following diagram shows the major components of the southbound
plugin and their relationships:

[![CLI plugin components](cliInComponents.png)](cliInComponents.png)

### Modules

The following diagram shows project modules and their dependencies:

[![CLI plugin modules](projectComponents.png)](projectComponents.png)
