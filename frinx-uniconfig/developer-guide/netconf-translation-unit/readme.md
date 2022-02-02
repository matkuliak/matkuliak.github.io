---
order: 2
---

# NETCONF Unified Translation Unit

Unified translation units are located in
<https://github.com/FRINXio/unitopo-units> repository.

Kotlin is used as preferred programming language in NETCONF translation
units because it provides [type
aliases](https://kotlinlang.org/docs/reference/type-aliases.html) and
better
[null-safety](https://kotlinlang.org/docs/reference/null-safety.html).

## TranslateUnit

Translate unit class must implement interface
**io.frinx.unitopo.registry.spi.TranslateUnit**. Naming convention for
translate unit class is just name Unit. Translate unit class is usually
instantiated, initialized and closed from Blueprint.

Implementation of TranslateUnit must be registered into
**TranslationUnitCollector** and must provide set of supported underlay
YANG models. Snippet below shows registration of
[Unit](https://github.com/FRINXio/unitopo-units/blob/master/junos/junos-17/junos-17-interface-unit/src/main/kotlin/io/frinx/unitopo/unit/junos/interfaces/Unit.kt)
for junos device version 17.3.

```kotlin
class Unit(private val registry: TranslationUnitCollector) : TranslateUnit {
    private var reg: TranslationUnitCollector.Registration? = null

    fun init() {
        reg = registry.registerTranslateUnit(this)
    }

    fun close() {
        reg?.let { reg!!.close() }
    }

    override fun getUnderlayYangSchemas() = setOf(
            UnderlayInterfacesYangInfo.getInstance())
```

Implementation of TranslateUnit must implement these methods:

**toString(): String**

Return unique string among all translation units which will be used as
ID for the translation unit (e.g. "IOS XR Interface (OpenConfig)
translate unit")

**getYangSchemas(): Set**

Return YANG models containing composite nodes handled by
handlers(readers/writers). It must return empty Set if no handlers are
implemented.

**getUnderlayYangSchemas(): Set**

Return YANG module informations about underlay models used in the
translation unit. These YANG modules describes configuration of NETCONF
capable device.

**getRpcs(underlayAccess: UnderlayAccess): Set\>**

Return RPC services implemented in the translation unit. Default
implementation returns an emptySet. Parameter **underlayAccess**
represents object containing methods for communication with a device via
NETCONF and should be passed to readers/writers.

**provideHandlers(rRegistry: ModifiableReaderRegistryBuilder,**
**wRegistry: ModifiableWriterRegistryBuilder,** **underlayAccess:
UnderlayAccess): Unit**

Handlers(readers/writers) need to be registered in this method.
**underlayAccess** represents object containing methods for communication
with a device via NETCONF and should be passed to readers/writers.

How to register readers/writers is described in
CLI Translation Unit \<cli-translation-unit\>

## Readers

Readers are handlers responsible for reading and parsing the data coming
from a device.

There are 2 types of readers: Reader and ListReader. Reader can be used
to handle container or argument nodes and ListReader should handle list
nodes from YANG.

- Both types need to implement **readCurrentAttributes** to fill the
    builder with appropriate values
- ListReader needs to also implement **getAllIds()** where it
    retrieves a key for each item to be present in current list. After
    the list is received, framework will invoke
    **readCurrentAttributes** for each item from getAllIds

### Mandatory interfaces to implement

Each reader needs to implement one of these interfaces based on type of
target node in YANG.For more information about methods please read
javadocs.

**ConfigListReaderCustomizer** - implement this interface if target
composite node in YANG is list and represents config data.

**ConfigReaderCustomizer** - implement this interface if target
composite node in YANG is container or augmentation and represents
config data.

**OperListReaderCustomizer** - implement this interface if target
composite node in YANG is list and represents operational data.

**OperReaderCustomizer** - implement this interface if target composite
node in YANG is container or augmentation and represents operational
data.

### Base Readers

Each base reader for netconf readers should be generic. The generic
marks the data element within device YANG that is being parsed into. The
base reader should contain abstract methods:

- **fun readIid(\<args\>): InstanceIdentifier\<T\>** - each child
    reader should fill in the device specific InstanceIdentifier that
    points to the information needed for this reader. Arguments may vary
    and they are used to be more specific IID (e.g. when creating an IID
    to gather information about a specific interface, you may want to
    pass interface name as argument).
- **fun readData(data: T?, configBuilder: ConfigBuilder, \<args\>)**
    - this method is used to transform OpenConfig data (contained in
    ConfigBuilder) into device data (T) using .

!!!
Naming of the methods should be unified in order to be easily parsed
by auto-generated documentation.
!!!

## Writers

A writer needs to implement all 3 methods: Write, Update, Delete in
order to fully support default rollback mechanism of the framework

!!!
Time showed that update like 1. delete, 2. write is anti-pattern and
should not be used. There is just one case where it is necessary: when
re-writing list entry, you must first delete the previous entry, then
write the new one, otherwise the previous entry would still be present
and the new entry will be added to the list.
!!!

A writer can properly work only if there is a reader for the same
composite node.

The framework provides safe methods to use when handling data on device:

- **safePut** deletes or adds managed data. Does not touch data that
    was previously on the device and is not handled by the writer.
- **safeMerge** stores just the changed data into device. Does not
    touch data that was previously on the device and is not handled by
    the writer.
- **safeDelete** removes data from the device only if the managed node
    does not contain any other information (even one not handled by the
    writer)

### Mandatory interfaces to implement

Each writer needs to implement one of these interfaces based on type of
target node in YANG. Unlike mandatory interfaces for reading, only
interfaces for writing config data are available (because it is not
possible to write operational data). For more information about methods
please read javadocs.

**ListWriterCustomizer** - implement this interface if target composite
node in YANG is list. An implementation needs to be registered as
GenericListWriter.

**WriterCustomizer** - implement this interface if target composite node
in YANG is container or augmentation. An implementation needs to be
registered as GenericWriter.

### Base Writers

Each base writer should be generic and contain abstract methods:

-   **fun getIid(id: InstanceIdentifier\<Config\>):
    InstanceIdentifier\<T\>** -this method returns InstanceIdentifier
    that points to a node where data should be written
-   **fun getData(data: Config): T** - this method transforms OpenConfig
    data into device specific data (T)

