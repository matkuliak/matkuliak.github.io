---
label: UniConfig Operations
order: 8000
---

# UniConfig Operations

## Sending and receiving data (RESTCONF)

RESTCONF represents REST API to access datastores and UniConfig operations.

## UniConfig Node Manager API

The responsibility of this component is to maintain configuration on devices based on intended configuration. Each device and its configuration is represented as a node in the uniconfig topology and the configuration of this node is described by using OpenConfig YANG models. The Northbound API of Uniconfig Manager (UNM) is RPC driven and provides functionality for commit with automatic rollback and synchronization of configuration from the network.

## Device discovery

This component is used to check reachable devices in a network. The manager checks the reachability via the ICMP protocol. Afterwards, the manager is able to check whether various TCP/UDP ports are open or not.

## Dry-run Manager API

The manager provides functionality showing CLI commands which would be sent to network element.

## Snapshot Manager API

The snapshot manager creates and deletes uniconfig snapshots of actual uniconfig topology. Multiple snapshots can be created in the system.

## Subtree Manager API

The subtree manager copies (merge/replace) subtrees between source and target paths.

## Templates Manager API

This component is responsible for application of templates into UniConfig nodes.

## Transaction Log API

This component is responsible for tracking transactions.

## Dedicated transaction (Immediate Commit Model)

The immediate commit creates new transactions for every call of an RPC. The transaction is then closed so no lingering data will occur.

## Utilities

This sub-directory contains UniConfig utilities.
