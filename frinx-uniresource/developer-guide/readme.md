---
order: 1
---

# Developer Guide

## Dependency on symphony

!!!warning
UniResource currently depends on a project called symphony.
!!!

This project is not publicly accessible and without access to it,
UniResource cannot be built. In that case, use pre built docker images
from dockerhub.

## Folder structure

- [ent](https://github.com/FRINXio/resource-manager/tree/master/ent) -
    ORM schema and generated code for the DB
- [ent/schema](https://github.com/FRINXio/resource-manager/tree/master/ent/schema)
    - ORM schema
- [graph/graphql](https://github.com/FRINXio/resource-manager/tree/master/graph/graphql)
    - graphQL schema and generated code for graphQL server
- [graph/graphhttp](https://github.com/FRINXio/resource-manager/tree/master/graph/graphhttp)
    - graphQL server
- [pools](https://github.com/FRINXio/resource-manager/tree/master/pools)
    - core codebase for pools and resoruce allocation
- [viewer](https://github.com/FRINXio/resource-manager/tree/master/viewer)
    - multitenancy, RBAC and DB connection management
- [psql](https://github.com/FRINXio/resource-manager/tree/master/psql)
    - psql DB connection provider
- [logging](https://github.com/FRINXio/resource-manager/tree/master/logging)
    - logging framework
- [api-tests](https://github.com/FRINXio/resource-manager/tree/master/api-tests)
    - integration tests
- [pkg](https://github.com/FRINXio/resource-manager/tree/master/pkg) -
    helm chart for UniResource

## Build

It is advised to build UniResource as a docker image using
[Dockerfile](https://github.com/FRINXio/resource-manager/blob/master/Dockerfile)
and run it as a docker container.

The reason is that UniResource uses wasmer and pre built js and python
engines for wasm. These are not part of the codebase and thus simply
running UniResource would fail, unless you provide these resources e.g.
by copying them out of UniResource built docker image.

!!!warning
UniResource utilizes wire to generate wiring code between major
!!!

components. Regenerating wiring is not part of standard build process !
After modifying any of the **wire.go** files perform:

```
    wire ./...
```

## GraphQL schema

UniResource exposes graphQL API and this is the
[schema](https://github.com/FRINXio/resource-manager/blob/master/graph/graphql/schema/schema.graphql).

## Built in strategies

UniResource provides a number of built in strategies for built in
resource types and are loaded into UniResource at startup.

[Built in strategies code
base](https://github.com/FRINXio/resource-manager/tree/master/pools/allocating_strategies/strategies)

[Built in strategies unit
tests](https://github.com/FRINXio/resource-manager/tree/master/pools/allocating_strategies/strategies/src/tests)

These strategies need to be tested/built and packaged for UniResource.
This test/build process in scrips section of
[package.json](https://github.com/FRINXio/resource-manager/blob/master/pools/allocating_strategies/strategies/package.json)
while the packaging part can be found in
[generate.go](https://github.com/FRINXio/resource-manager/blob/master/pools/allocating_strategies/generate.go).

Resource types associated with these strategies can be found in
[load\_builtin\_resources.go](https://github.com/FRINXio/resource-manager/blob/master/pools/allocating_strategies/load_builtin_resources.go).

## Unit tests

```
./unit-test.sh
```

## Integration tests

### API tests

There's a number of [api
tests](https://github.com/FRINXio/resource-manager/tree/master/api-tests)
available and can be executed using
[integration-test.sh](https://github.com/FRINXio/resource-manager/blob/master/integration-test.sh).
These tests need to be executed against UniResource running as a black
box (ideally as a container).

### Wasmer

There's a number of tests testing core components that require wasmer,
quickjs and python packages to be available. It is recommended to run
these tests in a docker container.

Example execution:

```
export WASMER_BIN=~/.wasmer/bin/wasmer
export WASMER_JS=~/.wasmer/globals/wapm_packages/_/quickjs@0.0.3/build/qjs.wasm
export WASMER_PY=~/.wasmer/globals/wapm_packages/_/python@0.1.0/bin/python.wasm
export WASMER_PY_LIB=~/.wasmer/globals/wapm_packages/_/python@0.1.0/lib/
go test -run Integration ./pools/...
```

### Additional info

#### Telementry

Support for tracing (distributed tracing). Streams data into a collector
such as Jaeger. Default is Nop. See main parameters or
telementry/config.go for further details to enable jaeger tracing

#### Health

Basic health info of the app (also checks if mysql connection is
healthy)

    # server can serve requests
    http://localhost:8884/healthz/liveness
    # server works fine
    http://localhost:8884/healthz/readiness

### Metrics

Prometheus style metrics are exposed at:

    http://localhost:8884/metrics
