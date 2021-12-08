Subtree Manager
===============

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

-   skipping non-existing composite nodes and leaves,
-   adjusting namespace and revision in node identifiers, only name of
    nodes must match with target schema,
-   moving nodes between choice and augmentation schema nodes,
-   adjusting value format to target type definition of leaf or
    leaf-list schema node.

