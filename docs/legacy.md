# Legacy tools and older versions

This repository includes tools from the original VEX workflow as well as the current GPU workflow. An asset being present doesn't mean the GPU Assembler supports its data. Older tools might still work with an older assembler, but compatibility isn't guaranteed.

## Assemblers and primitive versions

| Definition | Role |
| --- | --- |
| SDFM GPU Assembler 1.00 | Current OpenCL/COPs evaluation workflow |
| SDFM Assembler 1.22, 1.23, 1.24 | Earlier VEX assemblers, with cached-pass and volume-processing internals |
| SDFM Prim 1.6 | Main built-in primitive node for these guides |
| SDFM Prim 1.4, 1.5 | Earlier versions retained in the repository |
| SDFM Properties 1.1 | Shared properties, including group/modifier attributes |
| SDFM Properties 1.0 | Earlier properties definition |

The older assemblers expose Voxel Count/VoxelSize resolution, working-region controls, primitive guides, and volume postprocessing choices: None, Convert to VDB, VDB + Optimize, and Prep for Reuse. Their output and caching workflow differs from the GPU Assembler's Mesh/VDB/bypass menu. Don't assume the same parameter names imply the same implementation.

## Mesh and volume prims

**SDFM Mesh Prim 1.0 / 1.1** wraps input geometry as a packed mesh primitive with transforms, Shell, Combine Settings, and Disable Output. It is part of the older mesh-evaluation path.

**SDFM Volume Prim 1.1 / 1.2** prepares volume inputs for the older workflow. Its controls include Convert to VDB, Voxel Size, Use Reference VDB, optional Resample, and Extrapolate/Dilation, followed by transforms, Shell, Combine Settings, and Disable Output.

The current GPU kernel doesn't evaluate arbitrary mesh or volume prims. Its supported type table contains Box, Sphere, Tube, Capsule, Torus, Pyramid, Octahedron, Curve Wire, and Curve Extruded. Unknown names currently fall back to Box, so an apparently successful cook isn't proof of mesh or volume support.

## Mesh conversion and cleanup utilities

These tools work on geometry or volumes around the older workflow. They aren't SDF domain modifiers, and their compatibility with current outputs hasn't been revalidated as part of this documentation.

| Node | Purpose and main controls |
| --- | --- |
| SDFM Prim Convert 2 Polygons 1.0 | Converts supported source prims to polygon representations. Has per-shape tessellation, bevel divisions, normal cusp angle, and Do Shell. It doesn't evaluate the full assembly's booleans. |
| SDFM Volume 2 Mesh 1.0 | Smooths and converts volumes to polygons. Includes filter type/radius/iterations, optional piece segmentation and separation, Isovalue, Adaptivity, inverse iso-offset, and normals. |
| SDFM Post Sharpen Edges 1.3 | Uses curvature and nearby surface information to reposition mesh points toward sharper edges. Includes voxel-scaled search distance and maximum offset, point count, angle/plane thresholds, optional postprocessing, fuse distance, and normal cusp angle. |
| SDFM Post Fix Edges 1.1 | Manual edge/corner repair entries: Edges And Point, Fuse Points, Multiple Edges, and Edge Smooth. Includes selections, tension, resampling, smoothing, fusing, and normals. |
| SDFM Post Fix Faces 1.0 | Repairs selected faces, with optional Rebuild Curvature, remesh size, smoothing strength/filter quality, and normals. |

Several cleanup distances are expressed as multiples of `voxelsize`. Preserve the assembler's voxel-size metadata if experimenting with these tools downstream, and inspect the result at your chosen resolution.

## Older curve tools

Earlier curve experiments and deprecated scene nodes should be treated as legacy. The current Curve Wire 1.0 and Curve Extruded 1.0 definitions are implemented in the GPU evaluator; they are the curve workflow covered in [Primitives](primitives.md).

There are no additional distinct older curve asset families in the active top-level `otls` inventory inspected for these guides. Backups of curve prototypes exist locally, but aren't a supported installation source. Don't install backup definitions over the current assets to follow an old example.

For maintaining a scene, keep a copy of the original file and its asset versions before replacing nodes. Rebuild a small part with the current prims and GPU Assembler first, and compare the shape before migrating the rest.
