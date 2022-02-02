RPC create-transaction
======================

This REST call creates UniConfig transaction. The transaction represents session between client and UniConfig instance.
Every other REST call like mount device, change configuration, commit, sync from network, etc. should be called within the UniConfig transaction.
The transaction allows a client to identify if it still communicates with the same UniConfig instance.
If UniConfig instance does not know about the transaction then the request will fail because transaction expired or has never been created.

Transaction is represented as a cookie with name *UNICONFIGTXID* and value is UUID.

UniConfig transactions are used in HA deployments.


RPC Examples
~~~~~~~~~~~~

Successful example
++++++++++++++++++

.. admonition:: RPC Request

    .. container:: toggle

        .. container:: header

            **RPC request:**

        .. cssclass:: customclass

            |

            :Method: POST
            :URL: `http://192.168.56.11:8181/rests/operations/uniconfig-manager:create-transaction?maxAgeSec=20`

            Query params
                :maxAgeSec: number of seconds for how long the transaction is valid. Transaction expires after that in UniConfig instance. Default is 30 seconds if the parameter is not specified.


.. admonition:: RPC Response

    .. container:: toggle

        .. container:: header

            **RPC response:**

        .. cssclass:: customclass

            |

            :Status code: 201

            Headers
                .. code-block:: json
                    :linenos:

                    Set-Cookie: UNICONFIGTXID=92a26bac-d623-407e-9a76-3fad3e7cc698;Version=1;Comment="uniconfig transaction created";Path=/rests/;Max-Age=20

Subsequent requests must set cookie if they want to be included in UniConfig transaction.

.. admonition:: RPC Request

    .. container:: toggle

        .. container:: header

            **RPC request:**

        .. cssclass:: customclass

            |

            :Method: POST
            :URL: `http://192.168.56.11:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=n1`

            Headers
                :Content-Type: application/json
                :Cookie: UNICONFIGTXID=92a26bac-d623-407e-9a76-3fad3e7cc698

            Body
                .. code-block:: json
                    :linenos:

                    {
                        "node":[
                            {
                                "node-id":"n1",
                                "netconf-node-topology:host":"10.19.0.235",
                                "netconf-node-topology:port":830,
                                "netconf-node-topology:keepalive-delay":10,
                                "netconf-node-topology:connection-timeout-millis":100000,
                                "netconf-node-topology:default-request-timeout-millis":100000,
                                "netconf-node-topology:tcp-only":false,
                                "netconf-node-topology:username":"sysadmin",
                                "netconf-node-topology:password":"sysadmin",
                                "uniconfig-config:uniconfig-native-enabled": true
                            }
                        ]
                    }

.. admonition:: RPC Response

    .. container:: toggle

        .. container:: header

            **RPC response:**

        .. cssclass:: customclass

            |

            :Status: 201
----

Failed example
++++++++++++++

This is a case when mount node request contains UniConfig transaction ID and UniConfig instance does not know about the transaction because the transaction has never been created or has been closed or has already expired.

.. admonition:: RPC Request

    .. container:: toggle

        .. container:: header

            **RPC request:**

        .. cssclass:: customclass

            |

            :Method: POST
            :URL: `http://192.168.56.11:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=n1`

            Headers
                :Content-Type: application/json
                :Cookie: UNICONFIGTXID=92a26bac-d623-407e-9a76-3fad3e7cc698

            Body
                .. code-block:: json
                    :linenos:

                    {
                        "node":[
                            {
                                "node-id":"n1",
                                "netconf-node-topology:host":"10.19.0.235",
                                "netconf-node-topology:port":830,
                                "netconf-node-topology:keepalive-delay":10,
                                "netconf-node-topology:connection-timeout-millis":100000,
                                "netconf-node-topology:default-request-timeout-millis":100000,
                                "netconf-node-topology:tcp-only":false,
                                "netconf-node-topology:username":"sysadmin",
                                "netconf-node-topology:password":"sysadmin",
                                "uniconfig-config:uniconfig-native-enabled": true
                            }
                        ]
                    }


.. admonition:: RPC Response

    .. container:: toggle

        .. container:: header

            **RPC response:**

        .. cssclass:: customclass

            |

            :Status: 403

            Body
                .. code-block:: json
                    :linenos:

                    Unknown uniconfig transaction 92a26bac-d623-407e-9a76-3fad3e7cc698


.. note::

  If the RPC request is without the cookie UNICONFIGTXID then the RPC request passes because the implementation is backwards compatible.



RPC close-transaction
=====================

The REST call closes UniConfig transaction, which represents a session between client and UniConfig instance.
If UniConfig instance does not know about the transaction then the request will fail because transaction expired or has never been created.

Transaction is represented as a cookie with name *UNICONFIGTXID* and value is UUID.

UniConfig transactions are used in HA deployments.


RPC Examples
~~~~~~~~~~~~

Successful example
++++++++++++++++++

.. admonition:: RPC Request

    .. container:: toggle

        .. container:: header

            **RPC request:**

        .. cssclass:: customclass

            |

            :Method: POST
            :URL: `http://192.168.56.11:8181/rests/operations/uniconfig-manager:close-transaction`

            Headers
                :Cookie: UNICONFIGTXID=92a26bac-d623-407e-9a76-3fad3e7cc698


.. admonition:: RPC Response

    .. container:: toggle

        .. container:: header

            **RPC response:**

        .. cssclass:: customclass

            |

            :Status code: 200

            Headers
                .. code-block:: json
                    :linenos:

                    Set-Cookie: UNICONFIGTXID=92a26bac-d623-407e-9a76-3fad3e7cc698;Version=0;Comment="uniconfig transaction deleted";Max-Age=0

----

Failed example
++++++++++++++

This is a case when close-transaction request contains UniConfig transaction ID and UniConfig instance does not know about the transaction because the transaction has never been created or has been closed or has already expired.

.. admonition:: RPC Request

    .. container:: toggle

        .. container:: header

            **RPC request:**

        .. cssclass:: customclass

            |

            :Method: POST
            :URL: `http://192.168.56.11:8181/rests/operations/uniconfig-manager:close-transaction`

            Headers
                :Cookie: UNICONFIGTXID=92a26bac-d623-407e-9a76-3fad3e7cc698

.. admonition:: RPC Response

    .. container:: toggle

        .. container:: header

            **RPC response:**

        .. cssclass:: customclass

            |

            :Status: 403

            Body
                .. code-block:: json
                    :linenos:

                    Unknown uniconfig transaction 92a26bac-d623-407e-9a76-3fad3e7cc698
----

Failed example
++++++++++++++

This is a case when close-transaction request does not contain UniConfig transaction ID.

.. admonition:: RPC Request

    .. container:: toggle

        .. container:: header

            **RPC request:**

        .. cssclass:: customclass

            |

            :Method: POST
            :URL: `http://192.168.56.11:8181/rests/operations/uniconfig-manager:close-transaction`

            Headers

.. admonition:: RPC Response

    .. container:: toggle

        .. container:: header

            **RPC response:**

        .. cssclass:: customclass

            |

            :Status: 400


