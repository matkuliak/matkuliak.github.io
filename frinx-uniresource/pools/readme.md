---
order: 3
---

# Pools

A resource pool is an entity that allocates and deallocates resources
for a single specific resource type. Resource pools are completely
isolated from each other and there can be multiple resource pools for
the same resource type even providing the same resource instances.
Resource pools encapsulate the allocation logic and keep track of
allocated resources. A pool instance should manage resources within the
same network or logical network part (e.g. subnet, datacenter, region or
the entire, global network).

Example pools:

- IPv4 address pool allocating IP addresses from a range / subnet
- VLAN pool allocating all available VLAN numbers 0 - 4096
- Route distinguisher pool allocating route distinguishers from a
    specific, per customer, input

Depending on resource type and user’s requirements, pools need to be
capable of allocating resources based on various criteria / algorithms.
Currently, following pool types are supported by UniResource:

## SetPool

Pool with statically allocated resources. Users have to define all the
resources to be served in advance. The pool just provides one after
another until each resource is in use.

This type of pool is suitable for cases where a set of resources to be
served already exists.

Properties of SetPool

- Config
    - Set of unique resources to provide
    - Name of the pool
    - Resource recycling - whether deallocated resources should be
        used again
- Operational
    - Utilisation - % of pool capacity used

## SingletonPool

SingletonPool serves just a single resource for every request.

This type of pool can be utilized in special uses cases such as serving
a globally unique single AS number of an ISP. Instead of hardcoding the
AS number as a constant in e.g. workflows, it can be “managed” and
stored in the UniResource.

Properties of SingletonPool

- Config
    - A single unique resources to provide
    - Name of the pool

## AllocatingPool

AllocatingPool is a type of pool that enables algorithmical resource
allocation. Instead of using a pre-allocated set of resources to simply
distribute, it can create resources whenever asked for a new resource.
This type of pool allows users to define a custom allocation logic,
attach it to the pool and have use-case specific resource allocations
available. Important feature of this pool type is the ability to accept
new allocation logic from users in the form of a script without having
to rebuild the UniResource in any way.

This type of pool can be used when

- a predefined set of resources cannot be used
- resource creation requires additional inputs
- or in general whenever using an allocation script makes more sense
    then using a predefined set of resources

Properties of AllocatingPool

- Config
    - Allocation strategy - a script defining the allocation logic
    - Name of the pool
    - Resource recycling - whether deallocated resources should be
        used again
    - Limit - hard limit on total number of resource that can be
        produced
- Operational
    - Utilisation - % of pool limit used

Example AllocationPools:

- Pool providing all available VLAN numbers
- Pool providing just odd VLAN numbers
- Pool providing random VLAN numbers
- Pool providing Route Distinguishers that include customer specific
    information (which is passed as “additional input” as part of
    resource claim request)
- Pool providing IPv4-mapped IPv6 addresses from a specific range /
    subnet
- In general, anything that a user might need

## Allocation strategy overview

Allocation strategy encapsulates the allocation logic and is always tied
to (an) instance(s) of AllocatingPool. The strategy is defined in form
of a script using Javascript (or similar) language and its
responsibility is:

To produce a new (unique) resource instance based on a set of previously
allocated resources and any additional, user submitted input.

Apart from a resource being unique, there are no other requirements on
what the strategy needs to do. It gives users the freedom to implement
any logic.

Allocation strategy can take any input provided in a structure named
**userInput**. This input is provided by the user every time they claim a
new resource.

Allocation strategy also gets access to a
**list of already allocated resources** and any
**properties associated with the pool** being utilized.

### Pool hierarchies

UniResource allows pools to be organized into hierarchies e.g.

![Pool hierarchies](rm_hierarchy.png)

#### Labels

Labels enhance resource management by allowing a pool to be marked with
a custom string. Multiple pools can have the same label forming a
logical group of pools.

A group of pools under the same label can be dedicated to some logical
part of a network (e.g. datacenter, subnet, region etc.).

A single pool should typically have only one label i.e. it should not be
re-used across unrelated networks.

The following diagrams represent some of the configurations that can be
achieved using Labels:

##### Configuration: **Pool instance per Label**

Enables: Resource reuse in multiple networks

![Instance per label](instance_per_label.png)

##### Configuration: **Pool instance under multiple labels**

Enables: Unique resources across different networks

![Instance multiple labels](instance_multiple_labels.png)


##### Configuration: **Pool grouping**

Enables: Dividing resource pools into groups based on network regions.
Enables users to simply ask for a resource based on label name +
resource type (removing the need to know specific pools)

![Pool grouping](instance_grouping.png)


##### Configuration: **Multiple pool instances under the same Label**

Enables: Resource pool expansion in case an existing pool runs out of
resources. Serves as an alternative to existing pool reconfiguration. If
multiple pools of the same type are grouped under the same label, the
pools are drained of resources in the order they have been added to this
group/label.

![Multiple pool instances](multiple_instance_label.png)
