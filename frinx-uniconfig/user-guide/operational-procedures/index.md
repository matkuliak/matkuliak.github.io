# Operational Procedures

## Logging

UniConfig distribution uses Logback as the implementation of the logging
framework. Logback is the successor to to the log4j framework with many
improvements such as more options for configuration, better performance,
and context-based separation of logs. Context-based separation of logs
is used widely in UniConfig to achieve per-device logging based on the
set marker in the logs.

[!ref text="Logging"](../operational-procedures/logging)

## TLS

TLS, is a widely adopted security protocol designed to facilitate
privacy and data security for communications over the Internet. In the
default version of UniConfig TLS authentication is disabled.

[!ref text="TLS"](../operational-procedures/tls)

## OpenAPI

UniConfig distributions contain '.yaml' file that generates list of all
usable RPCs and their examples. You can view it locally or on our hosted version that always shows
latest OpenAPI version.

[!ref text="OpenAPI"](../operational-procedures/openapi)

## Data Security Models

UniConfig supports encryption and hashing of values in RESTCONF and
UniConfig shell API and managing of confidential data during transfer
between UniConfig database and network devices.

[!ref text="OpenAPI"](../operational-procedures/data-security-models)
