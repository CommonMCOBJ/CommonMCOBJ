```
Targeted Version: CommonMCOBJ V2 (March 2026)
Author(s): Mahid Sheikh (MCprep) <mahid@standingpad.org>
Last Updated: September 27th, 2024 Common Era
Proposed Mandation Status: Optional
```
# Biome Data Export Proposal
This proposal aims to standardize the export of Minecraft Biome Data (which hence will simply be referred to as Biome Data), to allow plugins to perform adjustments to materials that are based on biome information, through a JSON-based file format called `.cmcobj_bd`, which would accompany the OBJ and MTL export, as well as a PNG texture to drive it all.

This would come with 3 options: Colored Output, where the PNG outputted has all biome dependent colors already, Gray Output, where the PNG outputted maps biome data defined in `.cmcobj_bd` to RGB values, and Dual Output, which exports both a Colored Output PNG and a Gray Output PNG.

## Cmcobj_BD Data Format
The format of `.cmcobj_bd` is defined as a JSON structure in the following format:
```
{
    "biome_name_1" : {
        "temperature" : float,
        "downfall" : float,
        "precipitation" : bool,
        "biomeID" : int,
        "grayMapping" : [int, int, int]
    },

    "biome_name_2" : {
        "temperature" : float,
        "downfall" : float,
        "precipitation" : bool,
        "biomeID" : int,
        "grayMapping" : [int, int, int]
    },
} 
```

### Climate
The climate values `temperature`, `downfall`, and `precipitation` shall be equal to their in-game values (See [Minecraft.Wiki](https://minecraft.wiki/w/Biome#Climate), [Archive URL](https://web.archive.org/web/20240703205349/https://minecraft.wiki/w/Biome#Climate)). `temperature` is to be derived from the Base Temperature of the biome. In-game fluctuations shall not be taken into account. `biomeID` shall represent the in-game, integer ID of the biome (See [Minecraft.Wiki](https://minecraft.wiki/w/Biome#Biome_IDs), [Archive URL](https://web.archive.org/web/20240711000715/https://minecraft.wiki/w/Biome#Biome_IDs)). 

Although colors are hardcoded for some biomes (See [Minecraft.Wiki](https://minecraft.wiki/w/Color#Hard-coded_colors), [Archive URL](https://web.archive.org/web/20240703211128/https://minecraft.wiki/w/Color#Hard-coded_colors)), this shall not be exported. Plugins may utilize the `biomeID` property to perform this operation themselves.

### Gray Mapping
The `grayMapping` field shall only be defined if a Gray Output has been performed, and will be mapped to the RGB value that has been assigned to that particular biome by the OBJ exporter. OBJ exporters may choose to assign biomes however they wish.

### Exported Filenames
`.cmcobj_bd` files are to exported as `<obj_name>.cmcobj_bd`, where `<obj_name>` is the name of the OBJ file that the data references.

## PNG Exports
PNG images outputted shall treat each pixel as one block, from a top-down view, and dealing with the blocks on the surface of the overworld. The first pixel of the image represents `export_bounds_min` (excluding depth) and the last pixel of the image represents `export_bounds_max` (excluding height).

Surface of the overworld is all blocks that are exposed to air blocks from one or more sides.

### Exported Filenames
PNGs are to be exported as `<obj_name>_type.png`, where `type` can either be `gray` or `rgb`.

## Colored Output
With Colored Output, the OBJ exporter shall export a PNG of the OBJ as defined in [PNG Exports](#png-exports). The color of each pixel is the tint applied to the block represented by said pixel based on the biome conditions. In addition, the OBJ exporter shall also export a `.cmcobj_bd` file, with no `grayMapping` field for any of the biomes.

In the case of overlapping surface blocks causing 2 separate tint colors, the highest block shall be whose tint is exported.

## Gray Output
With Gray Output, the OBJ exporter shall export a PNG of the OBJ as defined in [PNG Exports](#png-exports). The color of each pixel shall be whatever RGB value was assigned to the `grayMapping` value of the biome the block represented by said pixel is in. The color shall then be handled in 3D software based on the mapped colors. In addition, the OBJ exporter shall also export a `.cmcobj_bd` file, with `grayMapping` defined for each biome.

## Dual Output
With Dual output, the OBJ exporter shall export 2 PNGs of the OBJ as defined in [PNG Exports](#png-exports), one based on [Colored Output](#colored-output), and one based on [Gray Output](#gray-output). In addition, the OBJ exporter shall also export a `.cmcobj_bd` file, with `grayMapping` defined for each biome.

## Common Header Additions
With Biome Data Exports, a new header option `biome_data_type` would indicate how biome data has been exported, if at all. This would be a value of the `string` type and added towards the end of the header (although order does not matter).

This would add 4 options:
- `RGB_IMAGE`: if Colored Output was done
- `GRAY_IMAGE`: if Gray Output was done
- `DUAL_IMAGE: if Dual Output was done
- `NONE`: if no biome data was exported
