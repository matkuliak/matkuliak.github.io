# TLS-based Authentication

In the default version of UniConfig TLS authentication is disabled. To
enable TLS for RESTCONF you must setup two things:

1. Key-store and trust-store that hold all keys and certificates. If
    authentication of individual clients is not required, trust-store
    doesn't have to be created at all. Key-store must always be
    initialized.
2. Enabling of TLS in UniConfig by editing the lighty configuration
    file.

## Setting of Key-store and Trust-store

Steps required for preparation of key-store and trust-store:

1. Create a directory under the UniConfig root directory that will
    contain key-store and optionally trust-store files, for example:

```
mkdir ./tls
cd ./tls
```

2.  Create a new key-store. There are two options depending on whether
    you already own the certificate that you would like to use for the
    identification of UniConfig on the RESTCONF layer.

  a.  Create a new key-store with the generated RSA key-pair (in the
     example the length of 2048 and validity of 365 days is used).
     After execution of the following command, the prompt will ask you
     for information about currently generated certificate that will be
     pushed into the newly generated key-store secured by a password
     (this secret will be used later in the configuration file -
     remember it).

       ```
       keytool -keystore .keystore -alias jetty -genkey -keyalg RSA -storetype PKCS12 -validity 365 -keysize 2048
       ```

 b.  Create a new key-store with already generated RSA key-pair (your
     certificate that you would like to use for authentication in ODL).

     ```
     keytool -import -file [your-certificate-file] -alias jetty -keystore .keystore
     ```

3. (Optional step) Create a new trust-store using an existing
    certificate (an empty truststore cannot be created). If you have
    multiple client certificates, they can be pushed to truststore with
    the same command executed multiple times (but alias must be unique
    for each of the imported certificates). Example:

```
keytool -import -file [client-app-certificate] -alias [unique-name-of-certificate] -keystore .truststore
```

!!!
You can easily convert OPENSSL PEM certificates to DER format that is
supported by keytool:

```
openssl x509 -outform der -in certificate.pem -out certificate.der
```
!!!

!!!
If your application needs to own distribution's certificate, you can
export certificate from generated key-pair that we have pushed into
the keystore (PKCS12 or OPENSSL format):

```
keytool -export -keystore .keystore -alias jetty -file odl.cer
penssl pkcs12 -in .keystore -out certificate.pem
```
!!!

## Enabling of TLS in UniConfig

Preparation of the TLS key-store and trust-store is not enough for
enabling TLS within the RESTCONF API. It is also required to point
UniConfig to these created storages and explicitly enable TLS by setting
a corresponding flag. The configuration file that must be modified can
be found on the following path relative to the UniConfig root directory:

```
vim config/lighty-uniconfig-config.json
```

Then, you must append the TLS configuration snippet (it must be placed
under the root JSON node) to the configuration file. The following
example snippet enables TLS authentication, disables user-based
authentication (hence trust-store is not required at all), and points
UniConfig to the key-store file that we have created in the previous
section.

```json
"tls": {
    "enabledTls": true,
    "enabledClientAuthentication": false,
    "keystorePath": "tls/.keystore",
    "keystorePassword": "key-pass"
}
```

If your deployment requires authentication of individual RESTCONF users
as well, you should also specify the trust-store fields by setting the
'enabledClientAuthentication' field to 'true'.

```json
"tls": {
    "enabledTls": true,
    "enabledClientAuthentication": true,
    "keystorePath": "tls/.keystore",
    "keystorePassword": "key-pass",
    "truststorePath": "tls/.truststore",
    "truststorePassword": "trust-pass"
}
```

You can also specify included or excluded cipher suites and TLS versions
that can or cannot be used for establishing a secured tunnel between the
Jetty server and clients. The following default configuration is based
on actual recommendations (you should adjust it as needed):

```json
"tls": {
  ...,
  "includedProtocols": [
      "TLSv1.2",
      "TLSv1.3"
  ],
  "excludedProtocols": [
      "TLSv1",
      "TLSv1.1",
      "SSL",
      "SSLv2",
      "SSLv2Hello",
      "SSLv3"
  ],
  "includedCipherSuites": [
      "TLS_ECDHE.*",
      "TLS_DHE_RSA.*"
  ],
  "excludedCipherSuites": [
      ".*MD5.*",
      ".*RC4.*",
      ".*DSS.*",
      ".*NULL.*",
      ".*DES.*"
  ]
}
```

!!!
It is enough to specify only the included protocols and included
cipher suites (all other entries are denied), or excluded protocols
and excluded cipher suites (all other entries are permitted). If you
specify the same entries under both the included and excluded cipher
suites or protocols, the excluded entry has higher priority. For
example, the final set of usable cipher suites is:
setOf(includedCipherSuites), setOf(excludedCipherSuites).
!!!