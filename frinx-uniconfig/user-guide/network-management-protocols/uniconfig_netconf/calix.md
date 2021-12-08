Calix devices
=============

> **note**
>
> To mount a Calix device is necessary to increase the memory assigned
> to JVM at least to 6GB

Mount Calix device
------------------

To mount the Calix device run:

``` {.sourceCode .bash}
curl -X PUT \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=calix \
 -d '{
 "node": [
   {
     "node-id": "calix",
     "netconf-node-topology:host": "10.19.0.16",
     "netconf-node-topology:port": 830,
     "netconf-node-topology:keepalive-delay": 0,
     "netconf-node-topology:tcp-only": false,
     "netconf-node-topology:username": "USERNAME",
     "netconf-node-topology:password": "PASSWORD",
     "uniconfig-config:uniconfig-native-enabled": true,
     "uniconfig-config:install-uniconfig-node-enabled": true,
     "uniconfig-config:blacklist": {
         "uniconfig-config:path": [],
         "uniconfig-config:extension": []
     }
   }
 ]
}'
```

Where:

> -   calix: is the name of the device
> -   10.19.0.16: is the ip address of the device
> -   830: is the port number of the device
> -   USERNAME: is the username to access the device
> -   PASSWORD: is the respective password
> -   "uniconfig-config:uniconfig-native-enabled": allows to enable
>     mounting through UniConfig Native
> -   "uniconfig-config:install-uniconfig-node-enabled": allows to
>     disable mounting to uniconfig and unified layers
> -   "uniconfig-config:path": allows to specify a list of root elements
>     from models present on device to be ignored by UniConfig Native
> -   "uniconfig-config:extension": allows to specify a list of module's
>     extensions to be ignored by UniConfig Native

In case of success the return code is 201.

Check if Calix device is connected
----------------------------------

To check if the device is properly connected run:

``` {.sourceCode .bash}
curl -X GET \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=calix?content=nonconfig
```

In case of success the return code is 200, and the response body
contains something similar to:

``` {.sourceCode .bash}
{
   "node": [
       {
           "node-id": "calix",
           "netconf-node-topology:unavailable-capabilities": {
               "unavailable-capability": [
                   {
                       "capability": "(urn:ietf:params:xml:ns:netconf:notification:1.0?revision=2008-07-14)notifications",
                       "failure-reason": "missing-source"
                   },

           ...
               ]
           },
           "netconf-node-topology:available-capabilities": {
               "available-capability": [
                   {
                       "capability-origin": "device-advertised",
                       "capability": "http://tail-f.com/ns/netconf/extensions"
                   },
                   {
                       "capability-origin": "device-advertised",
                       "capability": "(http://www.calix.com/ns/ipfix-vrf?revision=2018-10-23)ipfix-vrf"
                   },

           ...
               ]
           },
           "netconf-node-topology:host": "10.19.0.16",
           "netconf-node-topology:connection-status": "connected",
           "netconf-node-topology:port": 830
       }
   ]
}
```

Check if Calix device configuration is available in UniConfig
-------------------------------------------------------------

To check if the Calix device configuration has been properly loaded in
the UniConfig config datastore, run:

``` {.sourceCode .bash}
curl -X GET \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=calix/frinx-uniconfig-topology:configuration?content=config
```

In case of success the return code is 200 and the response body contains
something similar to:

``` {.sourceCode .bash}
{
  "frinx-uniconfig-topology:configuration": {
      "exa-base:config": {
          "system": {
              "bng:quarantine": {
                  "default-duration": "86400"
              },
              "aaa": {
                  "user": [
                      {
                          "name": "monitor",
                          "role": [
                              "oper"
                          ],
                          "password": "$1$bo6RaxHE$prYA2waVd/o4atvb1H8l8/"
                      },
                      {
                          "name": "calixsupport",
                          "role": [
                              "calixsupport"
                          ],
                          "password": "$1$2GV.JGzm$wKm7TIsZZgQMlAgvMwnSe/"
                      },
                      {
                          "name": "networkadmin",
                          "role": [
                              "networkadmin"
                          ],
                          "password": "$1$henWME92$LqNxDU3.wWG19Fz.AlL5H0"
                      },
...
```
