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
An OBJ selection is a bounding box that defines what part of a world is exported. This selection does not have to be restricted to full chunks, but it does have to be restricted to full blocks.

In CommonMCOBJ, this bounding box is defined with 2 variables that define the min and max values for the X, Y, and Z coordinates, where the Y coordinate represents the height. These coordinates will be used to determine the length, width, and height of the bounding box for the selection.

As an example, say we have an OBJ exported from a selection of 2 coordinates: `(30, 25, 131)` and `(71, 13, 53)`, with a max depth of up to `-50` and a max height of up to `50`. In this example, the min X and Z values will be `30` and `53` respectively, and the max X and Z values will be `71` and `131` respectively. 

<img src="Images/selection_x_z.png" width=50% height=50%>

The min and max Y coordinates meanwhile are not will not be 13 and 25, but whatever max depth and max height the exporter was configured to export with, so `-50` and `50`.

<img src="Images/selection_y.png" width=50% height=50%>

## Split Blocks
Optionally, an exporter is allowed to create split groups of blocks. Blocks shall be split by their type (ex. all sea lanterns are split into a single group, all grass blocks are split into one group, etc.).

## Common Header
OBJs following the CommonMCOBJ spec will have a header at the start of the file to give special information about the OBJ and source world. *Parenthesis are necessary*.

It is structured as follows:
```py
# COMMON_MC_OBJ_START
# version: 1
# exporter: Name of the exporter, all lowercase, with spaces substituted by underscores 
#
# world_name: Name of the source world
# world_path: Path of the source world
#
# exported_bounds_min: (min X, min Y, min Z)
# exported_bounds_max: (max X, max Y, max Z)
#
# is_centered: true if centered, false if not
# z_up: true if the Z axis is up instead of Y, false is not
# texture_type: ATLAS or INDIVIDUAL_TILES
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

    # Min values of the selection bounding box
    exported_bounds_min: (int, int, int)

    # Max values of the selection bounding box
    exported_bounds_max: (int, int, int)
    
    # Is the OBJ's origin centered to the geometry?
    is_centered: bool

    # Is the Z axis of the OBJ considered up?
    z_up: bool

    # Are the textures using large texture atlases or 
    # individual textures?
    texture_type: "ATLAS" | "INDIVIDUAL_TILES"

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
