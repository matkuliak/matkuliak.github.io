# Snapshot Manager

The snapshot manager creates and deletes UniConfig snapshots of actual
UniConfig topology. Multiple snapshots can be created in the system.

Snapshots may be used for manual rollback. Manual rollback enables
simple reconfiguration of the entire network using one of the previous
states saved in snapshots. That means that UniConfig nodes in config
datastore are replaced with UniConfig snapshot nodes.


## Create snapshot

[!ref text="Create Snapshot"](../snapshot-manager/rpc_create-snapshot)

## Delete snapshot

[!ref text="Delete Snapshot"](../snapshot-manager/rpc_delete-snapshot)

## Replace config with snapshot

[!ref text="Replace config with snapshot"](../snapshot-manager/rpc_replace-config-with-snapshot)

## Obtain snapshot metadata

[!ref text="Obtain snapshot metadata"](../snapshot-manager/obtain_snapshot_metadata)