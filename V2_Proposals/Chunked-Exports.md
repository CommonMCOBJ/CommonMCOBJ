```txt
Targeted Version: CommonMCOBJ V2 (March 2026)
Author(s): Mahid Sheikh (MCprep) <mahid@standingpad.org>
Last Updated: October 29th, 2024 Common Era
Proposed Mandation Status: Optional
```

# Chunked Exports Proposal

This proposal aims to add a standard way for OBJ Exporters to handle chunking exports
into smaller pieces. The idea is to allow for large worlds to be exported into smaller
selections of the main OBJ, so that large exports don't take too long to import into
3D programs.

In the context of this proposal, chunks refers to a part of a larger export, which
would be split into smaller pieces (in this proposal referred to as chunks) to better
handle large exports.

## Common Header Additions

We propose the addition of a `bool` variable `is_chunked` and `string` variable `chunk_path`
to the CommonMCOBJ header. `is_chunked` would let a plugin know if a
world is on its own or has chunks, while `chunk_path` would give a path to a
folder containing the OBJs of each chunk.

In addition, we also propose a `string` variable called `chunk_layer`, which can
be assigned any string value. For example, some chunks might be assigned to a
"Foreground" layer, while other chunks could be assigned to a "Background"
layer. This would allow for users to easily manage an export once imported into
a scene, and allow for easy optimization in 3D viewports during the animation
process.

Finally, we propose the addition of a `int` variable `chunk_resolution`, to allow
indicating LOD resolution in the exported chunk. By default, this shall be represented
with a value of `1`. Further details on LOD resolution will be specified below.

## Chunking Process

If supported, an OBJ exporter is allowed to create any size chunks they wish,
although at minimum, they may only be 16x16 blocks (the size of one in-game
chunk, see [Minecraft.Wiki](https://minecraft.wiki/w/Chunk), [Archive URL](https://web.archive.org/web/20241007153312/https://minecraft.wiki/w/Chunk)).
Chunks do not have to be uniform size, i.e. Chunk A does not have to be the same
size as Chunk B. Ideally however, users should have the option to choose the size
of chunks, with the export being cut up into as many chunks of the uniform size
selected as possible (unless not possible within the selected bounds). Chunks must
be contiguous.

### LOD

> [!NOTE]
> This part of the proposal would be optional for OBJ exporters to implement.

Chunks may optionally have an altered resolution in order to reduce geometry,
by combining several nearby blocks together into one larger block. The resolution,
represented by `chunk_resolution` would be constant across the whole chunk (except
if the resolution is 0).

`chunk_resolution` represents how much blocks have been "downsampled" in the export.
For example, a `chunk_resolution` of 2 indicates that blocks in a `2x2x2` volume
have been combined into a single block, while a `chunk_resolution` of 4 indicates
that blocks in a 4x4x4 area have been combined into a larger block.

If a chunk has variable resolution (i.e. some parts may have a resolution of 2, while
some may have a resolution of 4, etc.), then `chunk_resolution` shall be set to 0.

## Exported Files

When exporting the chunks, they should be stored in a folder specified in the
`chunk_path` variable of the header in the same directory as the OBJ and MTL. This
folder shall contain both the OBJ and MTL files of each chunk.
