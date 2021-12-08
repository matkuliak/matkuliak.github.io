RPC check-installed-nodes
=========================

This RPC checks if the devices given in the input are installed or not.
It checks for the database content of every device and if there is some,
then the device is installed.

RPC Examples
------------

### Successful example

RPC input contains a device while no devices are installed.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Successful example

RPC input contains devices (R1 and R2) and device R1 is installed.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC input doesn't specify any nodes.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *
