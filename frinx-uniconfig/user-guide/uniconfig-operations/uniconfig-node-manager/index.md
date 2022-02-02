# UniConfig Node Manager

The responsibility of this component is to maintain configuration on
devices based on intended configuration. Each device and its
configuration is represented as a node in the uniconfig topology and the
configuration of this node is described by using OpenConfig YANG models.
The Northbound API of Uniconfig Manager (UNM) is RPC driven and provides
functionality for commit with automatic rollback and synchronization of
configuration from the network.

When a commit is called, the UNM creates a diff based on intended state
from CONFIG DS and actual state from OPER DS. This Diff is used as the
basis for device configuration. UNM prepares a network wide transaction
which uses Unified mountpoints for communication with different types of
devices.

An additional git like diff RPC was created so it shows all the changes
grouped under root elements in a git-like style.

In the case where the configuration of one device fails, the UNM
executes automatic rollback where the previous configuration is restored
on all modified devices.

Synchronization from the network reads configuration from devices and
stores it as an actual state to the OPER DS.

- [RPC check-installed-nodes](../uniconfig-node-manager/uniconfig_check_installed_devices)
- [RPC install-multiple-nodes](../uniconfig-node-manager/uniconfig_install_multiple_nodes)
- [RPC uninstall-multiple-nodes](../uniconfig-node-manager/uniconfig_uninstall_multiple_nodes)
- [RPC commit](../uniconfig-node-manager/rpc_commit)
- [RPC checked-commit](../uniconfig-node-manager/rpc_checked-commit)
- [RPC validate](../uniconfig-node-manager/rpc_validate)
- [RPC sync-from-network](../uniconfig-node-manager/rpc_sync-from-network)
- [RPC is-in-sync](../uniconfig-node-manager/rpc_is-in-sync)
- [RPC replace-config-with-operational](../uniconfig-node-manager/rpc_replace-config-with-oper)
- [RPC calculate-diff](../uniconfig-node-manager/rpc_calculate-diff)
- [RPC calculate-git-like-diff](../uniconfig-node-manager/rpc_calculate-git-like-diff)
- [RPC show-connection-status](../uniconfig-node-manager/rpc_show_connection_status)
- [RPC health](../uniconfig-node-manager/rpc_health)
