![Common MC OBJ](logo.png)

CommonMCOBJ is a spec to allow Minecraft OBJ exporters to provide extra metadata about the source world and OBJ (that would otherwise be lost) in a standard way.

## Motivation
OBJ exporters are a vital piece of software for Minecraft artists. Without them, artists would not be able to bring their creations in Minecraft into the world of 3D software. 

Over the years plugins for artists such as MCprep have come on the scene to improve Mineraft animation workflows in general purpose software such as Blender. Many of these tools perform tasks on OBJs that would otherwise be to time consuming for regular users.

However, plugins in 3D software depend on certain information about that OBJ that, more often then not, is not provided, most commonly whether texture atlases are used or not. In addition, many users are unsatisfied with current limitations that exist with said software, such as lack of biome colors, which can only be provided by OBJ exporters, as they have full knowledge of the world being exported.

To address these problems, the CommonMCOBJ spec defines a set of conventions for OBJ exporters to act as a glue between plugins in 3D software, the export properties of the OBJ, and the source world for the OBJ.

# Spec
The following defines the spec for CommonMCOBJ.

## Selections
An OBJ selection is a rectangular selection that defines what parts of a world is exported. This selection does not have to be restricted to full chunks, so parts of a chunk are allow to be included. In the export, selections are defined by 2 vertices, one on the top left, and one on the bottom right, and these vertices are defined as XY coordinates from the in-game XYZ coordinates.

In addition, selections have a depth that dictates how deep an exporter will go to export the OBJ. Depth shall be represented as the in-game Y level that it corresponds to.

## Split Blocks
Optionally, an exporter is allowed to create split groups of blocks. Blocks shall be split by their type (ex. all sea lanterns are split into a single group, all grass blocks are split into one group, etc.).

## Biome Information
Although the common header defines a `has_biomes` key, no actual common standard for biome information has been created yet. Future revisions of the CommonMCOBJ spec will define a common interface for biome information.

## Common Header
OBJs following the CommonMCOBJ spec will have a header at the start of the file to give special information about the OBJ and source world.

It is structured as follows:
```py
# COMMON_MC_OBJ_START
# version: 1
# exporter: Name of the exporter, all lowercase, with spaces substituted by underscores 
#
# world_name: Name of the source world
# world_path: Path of the source world
#
# selection_vertex_top: (X, Y) coordinates of the top vertice (top left) for the OBJ selection
# selection_vertex_bottom: (X, Y) coordinates of the bottom vertice (bottom right) for the OBJ selection
# selection_depth: Max depth of the OBJ selection
#
# is_centered: true if centered, false if not
# z_up: true if the Z axis is up instead of Y, false is not
# texture_type: ATLAS or INDIVIDUAL_TILES
# has_biomes: true if biome information is exported, false if not
# has_split_blocks: true if blocks have been split, false if not
#
# COMMON_MC_OBJ_END
```

Using Python as a representation of the header:
```py
class CommonMCOBJ:
    # Version of the CommonMCOBJ spec
    version: int

    # Exporter name in all lowercase
    exporter: str 
    
    # Name of source world
    world_name: str 

    # Path of source world
    world_path: str 

    selection_vertex_top: (int, int)
    selection_vertex_bottom: (int, int)
    selection_depth: int
    
    # Is the OBJ's origin centered to the geometry?
    is_centered: bool

    # Is the Z axis of the OBJ considered up?
    z_up: bool

    # Are the textures using large texture atlases or 
    # individual textures?
    texture_type: "ATLAS" | "INDIVIDUAL_TILES"

    # Is biome information exported with the world?
    #
    # NOTE: this should be set as false for now, a 
    # common interface for biome information will 
    # be defined in future versions of the spec
    has_biomes: bool

    # Are blocks split by type?
    has_split_blocks: bool
```

In addition, exporters may export their own separate headers to provide extra information that the spec lacks support. Software can then use the `exporter` key to determine when to use these extra headers.

```py
# COMMON_MC_OBJ_START
# version: 1
# exporter: example_exporter
# ...
# COMMON_MC_OBJ_END

# Custom header
# ...
```

These can be placed above or below the CommonMCOBJ header as the CommonMCOBJ header defines start and end markers.
