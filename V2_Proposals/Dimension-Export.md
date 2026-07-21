```
Targeted Version: CommonMCOBJ V2
Author(s): Maryam Sheikh (MCprep) <mahid@standingpad.org>
Last Updated: July 20th, 2026 Common Era
Proposed Mandation Status: Required
```

# Dimension Export Proposal

This proposal aims to add a standard way for OBJ exporters to indicate
which dimension a given export belongs to, for tools and plugins to
reference where needed.

Currently, the dimension associated with an export is not given, meaning
plugins have to either make a guess based on what materials most blocks
use, or otherwise assume all exports to belong to the Overworld. This proposal
aims to bridge the gap.

## Common Header Additions

We propose the addition of a `dimension` key in the CommonMCOBJ header, set
to the `string` type. This value would represent the ID associated with the
dimension (ex. `minecraft:overworld`), with both the namespace and dimension.

```py
# COMMON_MC_OBJ_START
# version: 2
# exporter: example_exporter
# ...
# dimension: minecraft:overworld
```

The namespace of the ID will be required, to handle modded dimensions. For example,
with the Aether mod:

```py
# COMMON_MC_OBJ_START
# version: 2
# exporter: example_exporter
# ...
# dimension: aether:the_aether
```
