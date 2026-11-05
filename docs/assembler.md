# SDFM GPU Assembler

The GPU Assembler is where the shape setup becomes a surface. It prepares the source prims in SOPs, evaluates them using OpenCL in COPs, then returns a VDB or mesh. **Mesh** is the default output.

Connect the shapes in the order you want them evaluated. You can use the assembler's ordered inputs directly or merge branches upstream. A group's internal order is evaluated independently before that group's result joins the surrounding assembly.

## Working region

The assembler evaluates a fixed workspace. **Center**, **Size**, and **Uniform Scale** define it; **Bound** adds padding around that box.

Press **Fit Working Region** to fit the current source guides. This is a one-time fit, so later changes to the shapes won't automatically resize the workspace. Allow room for shells, blends, bends, and repeated shapes that extend beyond the visible source bounds. Infinite curve extrusion and infinite SDF arrays are also clipped by this region.

Keeping the workspace fixed is part of the GPU workflow. Moving a prim can update its description while keeping the base volume layout stable. Changing the working region or resolution changes that layout and can require more rebuilding and transfer work.

## Resolution

| Setting | How to use it |
| --- | --- |
| Resolution Mode: Voxel Count | Targets an approximate total voxel count across the padded workspace |
| Resolution Mode: VoxelSize | Sets a base voxel size, adjusted by Quality |
| Voxel Count | Higher values add detail and increase processing and memory requirements |
| Voxel Size | Smaller values resolve smaller features |
| Quality | Scales voxel density in VoxelSize mode for quick previews or final detail |

At **1x** Quality, the actual voxel size matches Voxel Size. At **0.5x**, it is twice as large; at **2x**, it is half as large. Halving voxel size can mean roughly eight times as many voxels for the same region.

Voxel Count is a target, rather than an exact allocation count. Grid alignment and VDB activation affect the final number. For a fixed count, a larger workspace means less detail per unit of model space.

A good starting point is a modest resolution with the workspace reasonably close to the model. Increase detail once the shapes and operations are working. Thin walls, small cap bevels, and narrow grooves need enough voxels to survive sampling.

## First output: SDF Output

| Output Mode | Result |
| --- | --- |
| VDB | The evaluated `surface` SDF, for further volume work or separate meshing |
| Mesh | Converts the evaluated VDB to polygons |
| Primitives (bypass) | Passes the grouped source prims through instead of evaluating the SDF |

Mesh mode adds three controls:

- **Isovalue** chooses the distance contour to mesh. Leave it at 0 for the authored surface. Changing it moves the extracted surface through the field.
- **Adaptivity** reduces polygon density where possible. Higher values trade surface detail for fewer polygons.
- **Compute Vertex Normals** adds normals during conversion. It is off by default in the current definition.

**Show Primitive Guides** displays source guides alongside the working setup. Use the evaluated output to judge the final booleans and deformations.

The interface also contains **Reset Cached Pass**. The current definition has no callback on that button, so it shouldn't be relied on to clear the GPU result. See [Troubleshooting](troubleshooting.md) if the output looks stale.

## Second output: Assembly Output

Assembly Output carries the source prims between group markers, like SDFM Group. It lets you preview a part here and use the same editable setup inside a larger assembly.

The assembler's **Combine Settings** and **Shell** belong to this enclosing group. They control how the part is treated when the source assembly is evaluated, including when passed into another assembler.

The parent's working region and resolution determine the final evaluation. A larger preview workspace on the child doesn't enlarge the parent's workspace, and increasing the child's mesh resolution doesn't increase the parent's resolution.

## What affects performance?

Voxel count is usually the first thing to check. The evaluator visits the shape description for each active voxel, so many source prims and heavily sampled curves also add work. Prim Array creates extra descriptors; an SDF array reuses the source through a domain transformation, with the cell-boundary behavior described in [Modifiers](modifiers.md).

Mesh extraction adds a separate step after GPU evaluation. Use VDB output if your next nodes work on volumes. Source curve preparation and final meshing still happen outside the SDF kernel; GPU evaluation doesn't make the whole network free of CPU work or data transfers.
