Authentication, Authorization and Accounting (AAA) Services
===========================================================

Overview
--------

Authentication, Authorization and Accounting (AAA) is a term for a
framework controlling access to resources, enforcing policies to use
those resources and auditing their usage. These processes are the
fundamental building blocks for effective network management and
security.

Authentication provides a way of identifying a user, typically by having
the user enter a valid user name and valid password before access is
granted. The process of authentication is based on each user having a
unique set of criteria for gaining access. The AAA framework compares a
user's authentication credentials with other user credentials stored in
a database. If the credentials match, the user is granted access to the
network. If the credentials don't match, authentication fails and access
is denied.

Authorization is the process of finding out what an authenticated user
is allowed to do within the system, which tasks can do, which API can
call, etc. The authorization process determines whether the user has the
authority to perform such actions.

Accounting is the process of logging the activity of an authenticated
user, for example, the amount of data a user has sent and/or received
during a session, which APIs called, etc.

### Terms And Definitions

AAA
:   Authentication, Authorization and Accounting.

Token
:   A claim of access to a group of resources on the controller.

Domain
:   A group of resources, direct or indirect, physical, logical, or
    virtual, for the purpose of access control.

User
:   A person who either owns or has access to a resource or group of
    resources on the controller.

Role
:   Opaque representation of a set of permissions, which is merely a
    unique string as admin or guest.

Credential
:   Proof of identity such as user name and password, OTP, biometrics,
    or others.

Client
:   A service or application that requires access to the controller.

Grant
:   It is the entity associating a user with his role and domain.

TLS
:   Transport Layer Security

CLI
:   Command Line Interface

Security Framework for AAA services
-----------------------------------

AAA services are based on the [Apache Shiro](https://shiro.apache.org/)
Java Security Framework. The main configuration file for AAA is located
at config/shiro.ini relative to the OpenDaylight Karaf home directory.

How to enable AAA
-----------------

AAA is enabled through installing the odl-aaa-shiro feature. The vast
majority of OpenDaylight's northbound APIs (and all RESTCONF APIs) are
protected by AAA by default when installing the odl-restconf feature,
since the odl-aaa-shiro is automatically installed as part of them. *AAA
features are installed automatically upon starting UniConfig*.

How to disable AAA
------------------

AAA is enabled by default, use only for disabling.

Edit the etcshiro.ini file and replace the following:

    /** = authcBasic

with

    /** = anon

Then restart the Karaf process. Using "/\*\* = anon" will disable
policies functionality.

AAA Realms
----------

AAA plugin utilizes the Shiro Realms to support pluggable authentication
& authorization schemes. There are two parent types of realms:

-   AuthenticatingRealm
    -   Provides no Authorization capability.
    -   Users authenticated through this type of realm are treated
        equally.
-   AuthorizingRealm
    -   AuthorizingRealm is a more sophisticated AuthenticatingRealm,
        which provides the additional mechanisms to distinguish users
        based on roles.
    -   Useful for applications in which roles determine allowed
        capabilities.

OpenDaylight contains five implementations:

-   TokenAuthRealm
    -   An AuthorizingRealm built to bridge the Shiro-based AAA service
        with the h2-based AAA implementation.
    -   Exposes a RESTful web service to manipulate IdM policy on a
        per-node basis. If identical AAA policy is desired across a
        cluster, the backing data store must be synchronized using an
        out of band method.
    -   A python script located at libs/*\**-idmtool.py is included to
        help manipulate data contained in the TokenAuthRealm.
    -   Enabled out of the box. This is the realm configured by default.
-   ODLJndiLdapRealm
    -   An AuthorizingRealm built to extract identity information from
        IdM data contained on an LDAP server.
    -   Extracts group information from LDAP, which is translated into
        OpenDaylight roles.
    -   Useful when federating against an existing LDAP server, in which
        only certain types of users should have certain access
        privileges.
    -   Disabled out of the box.
-   ODLJndiLdapRealmAuthNOnly
    -   The same as ODLJndiLdapRealm, except without role extraction.
        Thus, all LDAP users have equal authentication and authorization
        rights.
    -   Disabled out of the box.
-   ODLActiveDirectoryRealm
    -   Wraps the generic ActiveDirectoryRealm provided by Shiro. This
        allows for enhanced logging as well as isolation of all realms
        in a single package, which enables easier import by consuming
        servlets.
-   KeystoneAuthRealm
    -   This realm authenticates OpenDaylight users against the
        OpenStack™s Keystone server.
    -   Disabled out of the box.

> **note**
>
> More than one Realm implementation can be specified. Realms are
> attempted in order until authentication succeeds or all realm sources
> are exhausted. Edit the **securityManager.realms = \$tokenAuthRealm**
> property in shiro.ini and add all the realms needed separated by
> commas.

### TokenAuthRealm

#### How it works

The TokenAuthRealm is the default Authorization Realm deployed in
OpenDaylight. TokenAuthRealm uses a direct authentication mechanism as
shown in the following picture:

![TokenAuthRealm direct authentication
mechanism](direct-authentication.png)

A user presents some credentials (e.g., username/password) directly to
the OpenDaylight controller token endpoint /oauth2/token and receives an
access token, which then can be used to access protected resources on
the controller.

#### Configuring TokenAuthRealm

The TokenAuthRealm stores IdM data in an h2 database on each node. Thus,
configuration of a cluster currently requires configuring the desired
IdM policy on each node. There are two supported methods to manipulate
the TokenAuthRealm IdM configuration:

-   idmtool configuration tool
-   RESTful Web Service configuration

##### **Idmtool**

A utility script located at libs/*\**-idmtool.py is used to manipulate
the TokenAuthRealm IdM policy. idmtool assumes a single domain, the
default one (sdn), since multiple domains are not supported in the Boron
release. General usage information for idmtool is derived through
issuing the following command:

    $ python libs/***-idmtool.py -h
    usage: idmtool [-h] [--target-host TARGET_HOST]
                   user
                   {list-users,add-user,change-password,delete-user,list-domains,list-roles,add-role,delete-role,add-grant,get-grants,delete-grant}
                   ...

    positional arguments:
      user                  username for BSC node
      {list-users,add-user,change-password,delete-user,list-domains,list-roles,add-role,delete-role,add-grant,get-grants,delete-grant}
                            sub-command help
        list-users          list all users
        add-user            add a user
        change-password     change a password
        delete-user         delete a user
        list-domains        list all domains
        list-roles          list all roles
        add-role            add a role
        delete-role         delete a role
        add-grant           add a grant
        get-grants          get grants for userid on sdn
        delete-grant        delete a grant

    optional arguments:
      -h, --help            show this help message and exit
      --target-host TARGET_HOST
                            target host node

###### Add a user

    python libs/***-idmtool.py admin add-user newUser
    Password:
    Enter new password:
    Re-enter password:
    add_user(admin)

    command succeeded!

    json:
    {
        "description": "",
        "domainid": "sdn",
        "email": "",
        "enabled": true,
        "name": "newUser",
        "password": "**********",
        "salt": "**********",
        "userid": "newUser@sdn"
    }

> **note**
>
> AAA redacts the password and salt fields for security purposes.

###### Delete a user

    $ python libs/***-idmtool.py admin delete-user newUser@sdn
    Password:
    delete_user(newUser@sdn)

    command succeeded!

###### List all users

    $ python libs/***-idmtool.py admin list-users
    Password:
    list_users

    command succeeded!

    json:
    {
        "users": [
            {
                "description": "user user",
                "domainid": "sdn",
                "email": "",
                "enabled": true,
                "name": "user",
                "password": "**********",
                "salt": "**********",
                "userid": "user@sdn"
            },
            {
                "description": "admin user",
                "domainid": "sdn",
                "email": "",
                "enabled": true,
                "name": "admin",
                "password": "**********",
                "salt": "**********",
                "userid": "admin@sdn"
            }
        ]
    }

###### Change a user™s password

    $ python libs/***-idmtool.py admin change-password admin@sdn
    Password:
    Enter new password:
    Re-enter password:
    change_password(admin)

    command succeeded!

    json:
    {
        "description": "admin user",
        "domainid": "sdn",
        "email": "",
        "enabled": true,
        "name": "admin",
        "password": "**********",
        "salt": "**********",
        "userid": "admin@sdn"
    }

###### Add a role

    $ python libs/***-idmtool.py admin add-role network-admin
    Password:
    add_role(network-admin)

    command succeeded!

    json:
    {
        "description": "",
        "domainid": "sdn",
        "name": "network-admin",
        "roleid": "network-admin@sdn"
    }

###### Delete a role

    $ python libs/***-idmtool.py admin delete-role network-admin@sdn
    Password:
    delete_role(network-admin@sdn)

    command succeeded!

###### List all roles

    $ python libs/***-idmtool.py admin list-roles
    Password:
    list_roles

    command succeeded!

    json:
    {
        "roles": [
            {
                "description": "a role for admins",
                "domainid": "sdn",
                "name": "admin",
                "roleid": "admin@sdn"
            },
            {
                "description": "a role for users",
                "domainid": "sdn",
                "name": "user",
                "roleid": "user@sdn"
            }
        ]
    }

###### List all domains

    $ python libs/***-idmtool.py admin list-domains
    Password:
    list_domains

    command succeeded!

    json:
    {
        "domains": [
            {
                "description": "default odl sdn domain",
                "domainid": "sdn",
                "enabled": true,
                "name": "sdn"
            }
        ]
    }

###### Add a grant

    $ python libs/***-idmtool.py admin add-grant user@sdn admin@sdn
    Password:
    add_grant(userid=user@sdn,roleid=admin@sdn)

    command succeeded!

    json:
    {
        "domainid": "sdn",
        "grantid": "user@sdn@admin@sdn@sdn",
        "roleid": "admin@sdn",
        "userid": "user@sdn"
    }

###### Delete a grant

    $ python libs/***-idmtool.py admin delete-grant user@sdn admin@sdn
    Password:
    http://localhost:8181/auth/v1/domains/sdn/users/user@sdn/roles/admin@sdn
    delete_grant(userid=user@sdn,roleid=admin@sdn)

    command succeeded!

###### Get grants for a user

    python libs/***-idmtool.py admin get-grants admin@sdn
    Password:
    get_grants(admin@sdn)

    command succeeded!

    json:
    {
        "roles": [
            {
                "description": "a role for users",
                "domainid": "sdn",
                "name": "user",
                "roleid": "user@sdn"
            },
            {
                "description": "a role for admins",
                "domainid": "sdn",
                "name": "admin",
                "roleid": "admin@sdn"
            }
        ]
    }

##### **Configuration using the RESTful Web Service**

The TokenAuthRealm IdM policy is fully configurable through a RESTful
web service. You can also use our example [Postman
collections](https://github.com/FRINXio/Postman/tree/carbon/development/learning_labs/aaa).

###### Get All Users

    curl -u admin:admin http://localhost:8181/auth/v1/users
    OUTPUT:
    {
        "users": [
            {
                "description": "user user",
                "domainid": "sdn",
                "email": "",
                "enabled": true,
                "name": "user",
                "password": "**********",
                "salt": "**********",
                "userid": "user@sdn"
            },
            {
                "description": "admin user",
                "domainid": "sdn",
                "email": "",
                "enabled": true,
                "name": "admin",
                "password": "**********",
                "salt": "**********",
                "userid": "admin@sdn"
            }
        ]
    }

###### Create a User

    curl -u admin:admin -X POST -H "Content-Type: application/json" --data-binary @./user.json http://localhost:8181/auth/v1/users
    PAYLOAD:
    {
        "name": "ryan",
        "password": "ryan",
        "domainid": "sdn",
        "description": "Ryan's User Account",
        "email": "ryandgoulding@gmail.com"
    }

    OUTPUT:
    {
        "userid":"ryan@sdn",
        "name":"ryan",
        "description":"Ryan's User Account",
        "enabled":true,
        "email":"ryandgoulding@gmail.com",
        "password":"**********",
        "salt":"**********",
        "domainid":"sdn"
    }

### KeystoneAuthRealm

#### How it works

This realm authenticates OpenDaylight users against the OpenStack's
Keystone server. This realm uses the [Keystone's Identity API
v3](https://developer.openstack.org/api-ref/identity/v3/) or later.

![KeystoneAuthRealm authentication/authorization
mechanism](keystonerealm-authentication.png)

As can shown on the above diagram, once configured, all the RESTCONF
APIs calls will require sending **user**, **password** and optionally
**domain** (1). Those credentials are used to authenticate the call
against the Keystone server (2) and, if the authentication succeeds, the
call will proceed to the MDSAL (3). The credentials must be provisioned
in advance within the Keystone Server. The user and password are
mandatory, while the domain is optional, in case it is not provided
within the REST call, the realm will default to (**Default**), which is
hard-coded. The default domain can be also configured through the
*shiro.ini* file (see the AAA User Guide \<user-guide\>).

The protocol between the Controller and the Keystone Server (2) can be
either HTTPS or HTTP. In order to use HTTPS the Keystone Server's
certificate must be exported and imported on the Controller (see the
Certificate Management \<certificate-management\> section).

#### Configuring KeystoneAuthRealm

Edit the "config/shiro.ini" file and modify the following:

    # The KeystoneAuthRealm allows for authentication/authorization against an
    # OpenStack's Keystone server. It uses the Identity's API v3 or later.
    keystoneAuthRealm = org.opendaylight.aaa.shiro.realm.KeystoneAuthRealm
    # The URL where the Keystone server exposes the Identity's API v3 the URL
    # can be either HTTP or HTTPS and it is mandatory for this realm.
    keystoneAuthRealm.url = https://<host>:<port>
    # Optional parameter to make the realm verify the certificates in case of HTTPS
    #keystoneAuthRealm.sslVerification = true
    # Optional parameter to set up a default domain for requests using credentials
    # without domain, uncomment in case you want a different value from the hard-coded
    # one "Default"
    #keystoneAuthRealm.defaultDomain = Default

Once configured the realm, the mandatory fields are the fully quallified
name of the class implementing the realm *keystoneAuthRealm* and the
endpoint where the Keystone Server is listening *keystoneAuthRealm.url*.

The optional parameter *keystoneAuthRealm.sslVerification* specifies
whether the realm has to verify the SSL certificate or not. The optional
parameter *keystoneAuthRealm.defaultDomain* allows to use a different
default domain from the hard-coded one *"Default"*.

Authorization Configuration
---------------------------

OpenDaylight supports two authorization engines at present, both of
which are roughly similar in behavior:

-   Shiro-Based Authorization
-   MDSAL-Based Dynamic Authorization

### Shiro-Based Static Authorization

OpenDaylight AAA has support for Role Based Access Control (RBAC) based
on the Apache Shiro permissions system. Configuration of the
authorization system is done off-line; authorization currently cannot be
configured after the controller is started. The Authorization provided
by this mechanism is aimed towards supporting coarse-grained security
policies, the MDSAL-Based mechanism allows for a more robust
configuration capabilities. [Shiro-based
Authorization](http://shiro.apache.org/web.html#Web-%7B%7B%5Curls%5C%7D%7D)
describes how to configure the Authentication feature in detail.

> **note**
>
> The Shiro-Based Authorization that uses the *shiro.ini* URLs section
> to define roles requirements is **deprecated** and **discouraged**
> since the changes made to the file are only honored on a controller
> restart.
>
> Shiro-Based Authorization is not **cluster-aware**, so the changes
> made on the *shiro.ini* file have to be replicated on every controller
> instance belonging to the cluster.
>
> The URL patterns are matched relative to the Servlet context leaving
> room for ambiguity, since many endpoints may match (i.e.,
> "/restconf/modules" and "/auth/modules" would both match a
> "/modules/\*\*" rule).

#### Enable admin Role Based Access to the IdMLight RESTful web service

Edit the etcshiro.ini configuration file and add /auth/v1/\**=
authcBasic, roles[admin] above the line /\** = authcBasic within the
urls section.

    /auth/v1/** = authcBasic, roles[admin]
    /** = authcBasic

This will restrict the idmlight rest endpoints so that a grant for admin
role must be present for the requesting user.

> **note**
>
> The ordering of the authorization rules above is important! Try to
> keep most specific rules on the top, similar to structure of access
> lists.

### MDSAL-Based Dynamic Authorization

The MDSAL-Based Dynamic authorization uses the
MDSALDynamicAuthorizationFilter engine to restrict access to particular
URL endpoint patterns. Users may define a list of policies that are
insertion-ordered. Order matters for that list of policies, since the
first matching policy is applied. This choice was made to emulate
behavior of the Shiro-Based Authorization mechanism.

A **policy** is a key/value pair, where the key is a **resource** (i.e.,
a "URL pattern") and the value is a list of **permissions** for the
resource. The following describes the various elements of a policy:

-   **Resource**: the resource is a string URL pattern as outlined by
    Apache Shiro. For more information, see
    <http://shiro.apache.org/web.html>.
-   **Description**: an optional description of the URL endpoint and why
    it is being secured.
-   **Permissions list**: a list of permissions for a particular policy.
    If more than one permission exists in the permissions list they are
    evaluated using logical "OR". A permission describes the
    prerequisites to perform HTTP operations on a particular endpoint.
    The following describes the various elements of a permission:
    -   **Role**: the role required to access the target URL endpoint.
    -   **Actions list**: a leaf-list of HTTP permissions that are
        allowed for a Subject possessing the required role.

This an example on how to limit access to the modules endpoint:

    HTTP Operation:
    put URL: /restconf/config/aaa:http-authorization/policies

    headers: Content-Type: application/json Accept: application/json

    body:
      { "aaa:policies":
        { "aaa:policies":
          [ { "aaa:resource": "/restconf/modules/**",
            "aaa:permissions": [ { "aaa:role": "admin",
                                   "aaa:actions": [ "get",
                                                    "post",
                                                    "put",
                                                    "patch",
                                                    "delete"
                                                  ]
                                 }
                               ]
            }
          ]
        }
      }

The above example locks down access to the modules endpoint (and any
URLS available past modules) to the "admin" role. Thus, an attempt from
the OOB *admin* user will succeed with 2XX HTTP status code, while an
attempt from the OOB *user* user will fail with HTTP status code 401, as
the user *user* is not granted the "admin" role.

When using MDSAL-Based Dynamic Authorization, it is crucial to edit one
more file. In *config/aaa-app-config.xml* you have to insert every url
for which you want to create policy into "URLS" section of the file.

Example:

    <urls>
        <pair-key>/rests/data/network-topology:network-topology/topology=cli/node=node10</pair-key>
        <pair-value>authcBasic, dynamicAuthorization</pair-value>
    </urls>

After configuring this, you would restart UniConfig, and then you can
create desired policy for
*/rests/data/network-topology:network-topology/topology=cli/node=node10*

Order is important here. Try to keep most specific urls at the top.
