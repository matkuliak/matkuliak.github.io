Operational Procedures
======================

**High Availability Cluster**

Deployment of UniConfig in a high-availability cluster.

**Logging**

UniConfig distribution uses Logback as the implementation of the logging
framework. Logback is the successor to to the log4j framework with many
improvements such as more options for configuration, better performance,
and context-based separation of logs. Context-based separation of logs
is used widely in UniConfig to achieve per-device logging based on the
set marker in the logs.

**AAA**

Authentication, Authorization and Accounting (AAA) is a term for a
framework controlling access to resources, enforcing policies to use
those resources and auditing their usage. These processes are the
fundamental building blocks for effective network management and
security.

**TLS**

TLS, is a widely adopted security protocol designed to facilitate
privacy and data security for communications over the Internet. In the
default version of UniConfig TLS authentication is disabled.

**OpenAPI**

UniConfig distributions contain .yaml file that generates list of all
usable RPCs and their examples. and can be easily viewed either locally.
You can view it locally or on our hosted version that always shows
latest OpenAPI version.
