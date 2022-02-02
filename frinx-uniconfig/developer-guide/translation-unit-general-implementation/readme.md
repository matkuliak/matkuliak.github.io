---
order: 4
---

# Developing a new translation unit

This section provides a tutorial for developing a new translation unit.

The easiest way how to develop a new transaction unit is to copy
existing one and change what you need to make it work. E.g. if you are
creating an interface translation unit, the best way is to copy existing
interface translation unit for some other device, that is already
implemented. You can find existing units on github:

[https://github.com/FRINXio/cli-units](https://github.com/FRINXio/cli-units)

[https://github.com/FRINXio/unitopo-units](https://github.com/FRINXio/unitopo-units)

What you need to change:

- .pom file of the unit
    - point to correct unit parent
    - dependencies
    - name of the unit should be in format `<device>-<domain>-unit`
        (e.g. ios-interface-unit, xr-acl-unit)
- package name should be in format `io.frinx<cli|netconf>.` , device
    name and domain (e.g. io.frinx.cli.unit.ios.interface)

What you need to add:

- add your unit as a dependency to artifacts/pom

What your unit needs to contain:

- Unit class
- handlers (readers/writers)
- unit tests

## Best practices for handlers (readers/writers)

**Do not push code that contains following:**

1.  Static imports
2.  Commented out code
3.  Reflection
4.  Trailing whitespaces or tabs
5.  Double blank lines

**Before pushing the code make sure:**

1.  New classes/interfaces have the correct license header
2.  New classes/interfaces/yang model have correct date
3.  All new dependencies and imports are actually used
4.  All variables/methods are actually used
5.  All defined exceptions can be thrown from the code
6.  Comments are appropriate to the code behavior
7.  Code has correct spacing
8.  All comments are in English

- Constants
    - Chunk
    - Show commands
    - java regexes
