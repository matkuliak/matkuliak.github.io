UniConfig Node Manager
======================

The responsibility of this component is to maintain configuration on devices
based on intended configuration. Each device and its configuration is
represented as a node in the uniconfig topology and the configuration
of this node is described by using OpenConfig YANG models.
The Northbound API of Uniconfig Manager (UNM) is RPC driven and provides
functionality for commit with automatic rollback
and synchronization of configuration from the network.

When a commit is called, the UNM creates a diff based on intended state from
CONFIG DS and actual state from OPER DS. This Diff is used as the basis for
device configuration. UNM prepares a network wide transaction which
uses Unified mountpoints for communication with different types of devices.

An additional git like diff RPC was created so it shows all the changes
grouped under root elements in a git-like style.

In the case where the configuration of one device fails,
the UNM executes automatic rollback where the previous configuration
is restored on all modified devices.

Synchronization from the network reads configuration from devices and stores
it as an actual state to the OPER DS.

.. toctree::
    :maxdepth: 1

    uniconfig_check_installed_devices/check-installed-devices
    uniconfig_install_multiple_nodes/install-multiple-nodes
    uniconfig_uninstall_multiple_nodes/uninstall-multiple-nodes
    rpc_commit/commit
    rpc_checked-commit/checked-commit
    rpc_validate/validate
    rpc_sync-from-network/sync-from-network
    rpc_is-in-sync/is-in-sync
    rpc_replace-config-with-oper/replace-config-with-oper
    rpc_calculate-diff/calculate-diff
    rpc_calculate-git-like-diff/calculate-git-like-diff
    rpc_show_connection_status/show_connection_status
    rpc_health/health
    uniconfig_transaction/
