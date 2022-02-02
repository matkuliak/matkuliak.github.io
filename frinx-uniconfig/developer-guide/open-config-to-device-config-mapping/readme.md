---
order: 5
---

# OpenConfig to device config mapping

## Finding mapping between device and the model

Preferred YANG models for device config and operational data are
[OpenConfig
models](https://github.com/openconfig/public/tree/master/release/models).

These models usually represent configuration part in *container config*
and operational part in *container state*. Operational data is config
data + operational data.

YANG models used in UniConfig framework need to be located in
<https://github.com/FRINXio/openconfig>. In case the desired
functionality is not modeled yet, you can create new YANG with its own
structure or it can augment existing OpenConfig models. Guideline, how
to write OpenConfig models can be found at
<http://www.openconfig.net/docs/style-guide/>.

### Choosing the right YANG models

Before writing a custom YANG model for a unit, it is important to check
whether such a model doesn't already exist. There are plenty of YANG
models available, modeling many aspects of network device management.
The biggest groups of models are:

- OpenConfig
    <https://github.com/openconfig/public/tree/master/release/models>
- IETF <https://github.com/YangModels/yang/tree/master/standard/ietf>

It is usually wiser to choose an existing YANG model instead of
developing a custom one. Also, it is very important to check for
existing units already implemented for a device. If there are any, the
best approach will most likely be to use YANG models from the same
family as existing units use.

## Existing documentation

There is translation-units-docs
[page](https://github.com/FRINXio/translation-units-docs) as a single
point of truth for mapping. **Use** `{{ip}}` **notation** for variables
in the templates. This notation is postman compatible.
