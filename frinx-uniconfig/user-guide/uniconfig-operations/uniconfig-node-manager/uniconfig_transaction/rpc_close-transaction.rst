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


