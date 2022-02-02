---
order: 2
---

# UniResource Architecture

Following diagram outlines the high level architecture of UniResource.

![Architecture](rm_arch.png)

User authentication and authorization as well as user and tenant
management is outside of UniResource. UniResource is typically deployed
behind an api-gateway that handles authentication and authorization
relying on an external Identity Managmenet system.

The only aspect of tenancy management that needs to be handled by
UniResource is: per tenant database creation and removal. Each tenant
has its own database in database server.

## Technology stack

This section provides details on intended technologies to develop
UniResource with.

UniResource will rely on technologies used by the Inventory project
currently residing at: <https://github.com/facebookincubator/magma>
since both projects are similar and have similar requirements.

- Database
    - [Entgo.io](https://entgo.io/)
        - Ent is an ORM framework for go
        - Also handles schema migration: creates or updates tables in
            DB according to ent schema
        - RBAC rules can be defined as part of the schema
    - Postgres
        - PSQL is the DB of choice, but thanks to ent framework hiding
            the interactions with the database, other SQL DB could be
            used in the future
- Backend server
    - GraphQL
        - Primary API of UniResource will be exposed over GraphQL
            (over HTTP)
        - [Gqlgen](https://gqlgen.com/getting-started/)
            - Gqlgen is a graphql framework for go
            - Works well on top of entgo.io ORM
    - WASM
        - Web assembly runs any user defined code executing allocation
            logic for user defined resource pools
        - Separate process
        - Isolated and limited for safety and performance
- AAA
    - Tenant and user management is out of scope of UniResource and
        will be handled by an external identity management system.

## Entity model

Following diagram outlines the core entity model for UniResource:

![Entities](rm_entity.png)
