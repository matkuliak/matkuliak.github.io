# Subtree Manager

The subtree manager copies (merge/replace) subtrees between source and
target paths in Configuration or Operational datastore of UniConfig.
When one of these RPCs is called, Subtree Manager (SM) reads the
configuration from the source path and according to type of operation
(merge / replace), copies the subtree data to target path. Target path
is a parent path UNDER which data is copied. SM also distinguishes type
of source / target datastore.

All RPCs support merging/replacing of configuration between two
different schemas ('version drop' feature). This feature is handy, when
it is necessary to copy some configuration between two mounted nodes
that are described by slightly different YANG schemas. The following
changes between schemas are tolerated:

- Skipping non-existing composite nodes and leaves,
- Adjusting namespace and revision in node identifiers, only name of
    nodes must match with target schema,
- Moving nodes between choice and augmentation schema nodes,
- Adjusting value format to target type definition of leaf or
    leaf-list schema node.

## RPC copy-one-to-one

Provides a list of supported operations on subscriptions, includes
request examples and workflow diagrams.

[!ref text="RPC copy-one-to-one"](../subtree-manager/rpc_copy-one-to-one)

## RPC copy-one-to-many

Provides a list of supported operations on subscriptions, includes
request examples and workflow diagrams.

[!ref text="RPC copy-one-to-many"](../subtree-manager/rpc_copy-one-to-many)

## RPC copy-many-to-one

Provides a list of supported operations on subscriptions, includes
request examples and workflow diagrams.

[!ref text="RPC copy-many-to-one"](../subtree-manager/rpc_copy-many-to-one)

## RPC calculate-subtree-diff

Provides a list of supported operations on subscriptions, includes
request examples and workflow diagrams.

[!ref text="RPC calculate-subtree-diff"](../subtree-manager/rpc_calculate-subtree-diff)

## RPC calculate-subtree-git-like-diff

Provides a list of supported operations on subscriptions, includes
request examples and workflow diagrams.

[!ref text="RPC calculate-subtree-git-like-diff"](../subtree-manager/rpc_calculate-subtree-git-like-diff)