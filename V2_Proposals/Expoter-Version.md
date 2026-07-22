```
Targeted Version: CommonMCOBJ V2
Author(s): Maryam Sheikh (MCprep) <mahid@standingpad.org>
Last Updated: July 21st, 2026 Common Era
Proposed Mandation Status: Required
```

# Exporter Version Proposal

This proposal aims to add a standard way for various OBJ exporters
to indicate their own version in a standard way. With this, plugins
and tools can make various decisions based on the version of a particular
OBJ exporter used to export an OBJ.

Currently, the only exporter info revealed is the exporter itself, not
the actual version. This means plugins can't alter their own behavior
based on the version of an exporter, such as to work around bugs. This
proposal aims to bridge the gap.

## Common Header Additions

We propose the addition of a `exporter_version` key in the CommonMCOBJ
header, set to the `string` type. This value would represent the version
of the exporter that was used to export the OBJ. How this version is
interpreted will depend on the exporter, which is already given with the
`exporter` key.

```
# COMMON_MC_OBJ_START
# version: 2
# exporter: example_exporter
# exporter_version: 1
```

Because `exporter_version` is of type `string`, any value would be allowed:

```
# COMMON_MC_OBJ_START
# version: 2
# exporter: example_exporter
# exporter_version: 24.02 Womp Womp
```
