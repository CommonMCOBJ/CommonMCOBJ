```
Targeted Version: CommonMCOBJ V2 (March 2026)
Author(s): Mahid Sheikh (MCprep) <mahid@standingpad.org>
Last Updated: July 10th, 2024 Common Era
Proposed Mandation Status: Optional
```
# Biome Data Export Proposal
This proposal aims to standardize the export of Minecraft Biome Data (which hence will simply be referred to as Biome Data), to allow plugins to perform adjustments to materials that are based on biome information, through a JSON-based file format called `.cmcobj_bd`, which would accompany the OBJ and MTL export.

## Common Header Additions
With Biome Data Exports, a new header option `has_biome_data` would indicate whether Biome Data has been exported or not. This would be a value of the `bool` type and added towards the end of the header (although order does not matter).

## Data Format
The format of `.cmcobj_bd` is defined as a JSON structure in the following format:
```
{
    "biome_name_1" : {
        "temperature" : float,
        "downfall" : float,
        "precipitation" : bool,
        "biomeID" : int,
        "bounds" : [
            [[int, int, int], [int, int, int]]
        ],
    },

    "biome_name_2" : {
        "temperature" : float,
        "downfall" : float,
        "precipitation" : bool,
        "biomeID" : int,
        "bounds" : [
            [[int, int, int], [int, int, int]],
            [[int, int, int], [int, int, int]]
        ],
    },
} 
```

### Climate
The climate values `temperature`, `downfall`, and `precipitation` shall be equal to their in-game values (See [Minecraft.Wiki](https://minecraft.wiki/w/Biome#Climate), [Archive URL](https://web.archive.org/web/20240703205349/https://minecraft.wiki/w/Biome#Climate)). `temperature` is to be derived from the Base Temperature of the biome. In-game fluctuations shall not be taken into account.

Although colors are hardcoded for some biomes (See [Minecraft.Wiki](https://minecraft.wiki/w/Color#Hard-coded_colors), [Archive URL](https://web.archive.org/web/20240703211128/https://minecraft.wiki/w/Color#Hard-coded_colors)), this shall not be exported.

### Bounds
Biome Data Bounds are based on the existing CommonMCOBJ Selections (See [Selections](https://github.com/CommonMCOBJ/CommonMCOBJ?tab=readme-ov-file#selections)). These are to be exported in Minecraft Coordinates, and are subject to `export_offset` and `block_origin_offset`.

In the `.cmcobj_bd` format, the `bounds` list contains a lists of 2 bounds, which are each lists of 3 integers. A single biome may have multiple bounding boxes, at varying locations and heights.

Biome Data Bounds are in effect approximate, and may not accurately reflect biomes in game. More accurate approximations can be exported by exporting more smaller bounds, as opposed to a few large bounds. However, speed vs accuracy must be taken into account. Bounds of 1 block by 1 block is not recommended.

### Exported Filenames
`.cmcobj_bd` files are to exported as `<obj_name>.cmcobj_bd`, where `<obj_name>` is the name of the OBJ file that the data references.
