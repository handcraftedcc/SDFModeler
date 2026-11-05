# Combining shapes

Each prim's **Combine Settings** describes how it interacts with the model built so far. The same controls appear on groups and assemblers. On a group, they apply to the completed group; on an assembler, they control its source assembly when reused downstream.

## Mode and Type

| Mode | Result | Types |
| --- | --- | --- |
| Union | Adds the incoming shape | Chamfer, Round, Columns, Stairs, Soft |
| Intersect | Keeps the overlap with the incoming shape | Chamfer, Round, Columns, Stairs |
| Difference | Cuts the incoming shape out of the previous result | Chamfer, Round, Columns, Stairs |
| Other | Creates a more specialized interaction | Pipe, Engrave, Groove, Tongue, Offset |

For a simple boolean, start with Chamfer and Radius 0. Increasing **Radius** adds the selected transition where the surfaces meet. Round gives a rounded transition, Chamfer a straight one, and Columns and Stairs add repeating detail. **Count** controls those repeated details. Soft is an additional smooth union option.

The result depends on the angle and overlap between the surfaces. Try the operation on two simple prims first if you're figuring out the settings.

## Other modes

**Pipe** builds a pipe-like shape around the surfaces' intersection. **Engrave** creates an engraved intersection treatment. **Groove** and **Tongue** create paired recessed or raised interactions, with Radius A and Radius B controlling their sizes.

**Offset** uses the dedicated Offset control for its specialized combination. It is a two-shape operation; for simply growing or shrinking one shape, use the Offset Settings on SDFM Properties instead.

## Shell

Shell creates a wall around the zero surface. On a prim, it shells that prim before combination. On an SDFM Group, it shells the completed group, which can give a very different result from shelling each member separately.

In the current GPU evaluator, **Shell Thickness** is the distance to either side of the original surface. A value of 0.025 gives a nominal total wall thickness of 0.05. Around complex blends or deformations this is approximate, since the field may no longer be an exact distance everywhere.

## SDFM Properties

SDFM Properties edits the transform and evaluation attributes on incoming SDFM prims. It is also used internally by several assets.

The **Shell Settings**, **Offset Settings**, and **Combine Settings** toggles determine which categories it writes. Check these toggles when adding it to an existing setup: enabled categories can replace settings already carried by the prims.

**Offset Amount** grows the shape with positive values and shrinks it with negative values. Offset is applied before Shell in the GPU evaluator.

Properties changes the individual incoming records. Use an **SDFM Group** if you want one combination setting or shell around their combined result. The advanced Group Settings and Modifier Settings in Properties 1.1 are the underlying controls used by the dedicated group and modifier nodes; the dedicated nodes set up the required structure for you.

**Colorize By Mode** helps distinguish the source guides by their operation. It isn't a material assignment for the final mesh.
