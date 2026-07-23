```
Targeted Version: CommonMCOBJ V2
Author(s): Maryam Sheikh (MCprep) <mahid@standingpad.org>
Last Updated: July 22nd, 2026 Common Era
Proposed Mandation Status: Required
```

# Minecraft Version Export Proposal

This proposal aims to export the Minecraft version of a
source world in a standard way. Currently, there is no way
to get the version of the source world for a given OBJ export.
This aims to bridge the gap.

## Some Considerations

Minecraft versioning has changed several times over the game's
lifespan. In addition, versioning is not syned between both the
Java and Bedrock editions of the game.

For the purposes of this proposal, only the release versions of Minecraft
Java edition will be considered as valid (i.e. all versions of Minecraft
Java edition released on or after November 18th, 2011). Versions such
as Classic, Infdev, Alpha, etc. will not be considered valid under this
spec. In addition, only version numbers for Minecraft Java edition will
be considered valid, and version numbers for Minecraft Bedrock edition will
be considered invalid.

## Common Header Additions

We propose the addition of an `mc_version` key in the CommonMCOBJ header,
set to one of the following types (Note: any values in quotes are to be
interpreted as literals of the `string` type. Quotes should not be included
in the actual export):

- Exact formats:
    - `("JAVA_CAL_VER", int, int, int)`
    - `("JAVA_CAL_VER", int, int, "RC", int)`
    - `("JAVA_CAL_VER", int, int, "PRE_RELEASE", int)`
    - `("JAVA_CAL_VER", int, int, "SNAPSHOT", int)`
    - `("JAVA_SEM_VER", int, int, int)`
    - `("JAVA_SEM_VER", int, int, int, "RC", int)`
    - `("JAVA_SEM_VER", int, int, int, "PRE_RELEASE", int)`
    - `("JAVA_SNAP_VER", int, int, string)`
- Approximate formats:
    - `("JAVA_OTHER", string)`
        - `string` must be in double quotes (`"`)
    - `"NON_REPRESENTABLE"`

OBJ exporters should export the version number as closely as possible. For example:

- `1.21.11` shall be exported as `(JAVA_SEM_VER, 1, 21, 11)`
- `26.1.1` shall be exported as `(JAVA_CAL_VER, 26, 1, 1)`
- `Snapshot 25w44a` shall be exported as `(JAVA_SNAP_VER, 25, 44, a)`
- `26.3 Snapshot 1` shall be exported as `(JAVA_CAL_VER, 26, 3, SNAPSHOT, 1)`

In situations where the version cannot be exported to an exact format,
an approximate format may be used. If the world is still derived from
a version of Minecraft Java Edition released on or after November 18th, 2011
(e.g. Combat Tests), then `("JAVA_OTHER", string)` shall be used. If the world
is not derived from a version of Minecraft Java Edition released on or
after November 18th, 2011, than `"NON_REPRESENTABLE"` shall be used. For example:

- `Combat Test 3` may be exported as:
    - `(JAVA_OTHER, "Combat Test 3")`
    - `(JAVA_OTHER, "1.14.4 Combat Test 3)`
    - etc.
- `1.18 Experimental Snapshot 1` may be exported as:
    - `(JAVA_OTHER, "1.18 Experimental Snapshot 1")`
    - `(JAVA_OTHER, "1_18_experimental-snapshot-1")`
    - etc.
- `Alpha 1.1` shall be exported as `NON_REPRESENTABLE`
- Bedrock `1.19.83` shall be exported as `NON_REPRESENTABLE`
