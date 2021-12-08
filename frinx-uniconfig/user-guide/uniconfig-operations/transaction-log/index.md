Transaction Log
===============

The transaction log consists of a transaction tracker and a
revert-changes RPC. The transaction tracker stores information called
transaction-metadata about performed transactions into the operational
snapshot. Whereas revert-changes RPC can be used to revert changes that
have been made in a specific transaction. A user only need to have ID of
transaction for that. One or more transactions can be reverted using one
revert-changes RPC.
