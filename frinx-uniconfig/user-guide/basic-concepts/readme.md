---
order: 10000
---

# Basic Concepts


UniConfig is a network controller that enables network operators to
automate simple and complex procedures in their heterogeneous networks.
UniConfig uses CLI, NETCONF and gNMI to connect to network devices and
provides a RESTCONF interface on its northbound to provide an API to
applications. UniConfig users use clients in various programming
languages to communicate from their applications with the controller.
FRINX provides a Java client and python workers to integrate with its
workflow automation in FRINX Machine. Other clients can be generated
from the OpenAPI documentation of the UniConfig API.

UniConfig is stateless and stores all state information before and after
transactions in a PostgreSQL database. UniConfig provides transaction
capabilities on its northbound API, so that multiple clients can
interact with UniConfig at the same time in a well-structured way. In
addition, transactions are also supported towards all network devices
independent of the capabilities of these devices. Transactions can be
rolled back on error automatically and on user demand by specifying a
transaction ID from the transaction log. Clients can use an “immediate
commit” model (changes sent to the controller get applied to the devices
immediately) or a “build and commit” model (changes are staged on the
controller until a commit operation pushes all changes in a transaction
to one or multiple devices).

To support N+1 redundancy and horizontal scale (meaning adding more
controller instances allows the system to serve more network devices and
more clients) UniConfig can be deployed together with a load balancer
(E.g.: Traefik). The combination of a state-less load balancer and
multiple UniConfig instances achieves high availability and supports
many network devices and client applications to configure the network.

An open-source device library allows users to connect UniConfig to CLI
devices that do not support SDN protocols like NETCONF and gNMI. This
library is open to users, independent software vendors and any 3rd party
to contribute to and use to achieve their automation goals.

Finally, the UniConfig shell, allows users to interact with all
UniConfig operations and the connected devices in a model driven way
through CLI.

UniConfig runs in containers, VMs or as application and can be deployed
stand-alone or as part of the "FRINX Machine" network automation
solution.
